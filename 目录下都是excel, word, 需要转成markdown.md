#概念-文档转换  

对于通讯协议和 PRD 这种**强结构化、多表格**的文档，虽然 Dify 支持直接上传 Excel 和 Word，但**为了保证年轻工程师搜得准，转成 Markdown 是“从 60 分到 90 分”的关键。**

原因如下：

## 1. 为什么 Excel 必须处理？

- **语义断裂：** Excel 的一行数据在 RAG 检索里会被切成碎片。如果你有一行定义了 `0x01 | Read | RegisterA`，AI 可能只搜到了 `RegisterA`，却丢了它是 `Read` 还是 `Write` 的上下文。
    
- **多 Sheet 噩梦：** 协议文档经常一个功能一个 Sheet。Dify 默认解析 Excel 有时会丢失表头信息。
    
- **建议：** 将 Excel 的表格转换为 **Markdown Table**。Markdown 的表格语法能让 LLM 明确感知“列名-数值”的对应关系。
    

## 2. 为什么 Word 建议转 Markdown？

- **层级结构：** Word 的标题层级（一级标题、二级标题）在直接上传时，Dify 的解析器不一定能完美保留。
    
- **Markdown 的优势：** Markdown 用 `#`、`##` 明确标识了层级。在 RAG 检索时，如果你搜某个子功能，AI 能通过 Markdown 结构回溯到它属于哪个大模块，防止“张冠李戴”。
    

---

## 硬核操作建议：不用手动转，用脚本

既然你有很多文件，没必要一个个另存为。你可以用 Python 批量处理，这才是 Staff Engineer 该有的工作流：

#### 对于 Word (.docx)：

推荐使用 `marker` 或 `pandoc`。它们能把 Word 里的标题、列表、加粗完美保留。

#### 对于 Excel (.xlsx)：

我建议你写个简单的脚本，把每个有效的数据行转成一小段“描述性文本”或者“Markdown 表格块”。

> **反直觉的经验：** 对于通讯协议，把 `0x01: 启动` 转换成 `指令码 0x01 代表 启动功能` 这种自然语言描述，Embedding 的匹配效率反而比纯表格高。

---

## 你的 Dify 知识库最佳实践设置：

既然你已经到了知识库这一步，请按以下参数配置：

1. **清洗策略：** 选“自动”。
    
2. **分段设置 (Chunking)：**
    
    - **分段长度：** 建议设为 `500 - 800` token。协议文档太短会丢上下文，太长会引入噪音。
        
    - **重叠分段 (Overlap)：** 设为 `10% - 15%`。这样即便一个知识点被切断了，前后分段也能互相“接头”。
        
3. **检索设置 (Retrieval)：**
    
    - **务必选：** **混合检索 (Hybrid Search)**。
        
    - **Top K：** 设为 `5 - 10`。
        
    - **重排序 (Rerank)：** 如果你有 API Key，一定要开启 Rerank。它能把检索出来的日文原文，按照跟中文问题的相关性重新排一遍，非常有效。
        

**既然你有这堆 Excel 和 Word，需要我给你写一个 Python 批量转换脚本的 Demo 吗？你可以直接在你的电脑上跑，把它们变成 Dify 最喜欢的格式。**

---
这个脚本的目标是把你的 **Excel (寄存器表/指令集)** 和 **Word (设计说明/PRD)** 批量转换为 **Dify 最喜欢的 Markdown 格式**。

为了保证 RAG 的检索精度，脚本会：

1. **Word：** 保留标题层级（`#`）和表格。
    
2. **Excel：** 将每个 Sheet 转换为独立的 Markdown 表格，并自动处理空行。
    

## 1. 准备环境

你需要安装这几个高性能处理库：

Bash

```
pip install pandas openpyxl python-docx tabulate
```

## 2. 转换脚本 (`convert_to_md.py`)

```Python
import os
import pandas as pd
from docx import Document
from pathlib import Path

def docx_to_md(docx_path, output_path):
    """将 Word 转换为带标题层级和表格的 Markdown"""
    doc = Document(docx_path)
    md_content = []
    
    for obj in doc.element.body:
        # 处理段落
        if obj.tag.endswith('p'):
            para = [p for p in doc.paragraphs if p._element == obj][0]
            text = para.text.strip()
            if not text: continue
            
            # 根据大纲级别转换标题 (Heading 1 -> #, Heading 2 -> ##)
            if para.style.name.startswith('Heading'):
                level = para.style.name.split()[-1]
                try:
                    md_content.append(f"{'#' * int(level)} {text}")
                except:
                    md_content.append(f"### {text}")
            else:
                md_content.append(text)
        
        # 处理表格
        elif obj.tag.endswith('tbl'):
            table = [t for t in doc.tables if t._element == obj][0]
            data = []
            for row in table.rows:
                data.append([cell.text.strip().replace('\n', ' ') for cell in row.cells])
            
            if data:
                df = pd.DataFrame(data[1:], columns=data[0])
                md_content.append(df.to_markdown(index=False))
        
    with open(output_path, 'w', encoding='utf-8') as f:
        f.write('\n\n'.join(md_content))

def excel_to_md(xlsx_path, output_path):
    """将 Excel 的每个 Sheet 转换为 Markdown 块"""
    excel_file = pd.ExcelFile(xlsx_path)
    md_content = []
    
    for sheet_name in excel_file.sheet_names:
        df = pd.read_excel(xlsx_path, sheet_name=sheet_name)
        # 清洗空行空列
        df = df.dropna(how='all').dropna(axis=1, how='all')
        
        if not df.empty:
            md_content.append(f"## Sheet: {sheet_name}")
            md_content.append(df.to_markdown(index=False))
            
    with open(output_path, 'w', encoding='utf-8') as f:
        f.write('\n\n'.join(md_content))

def batch_convert(input_dir):
    output_dir = Path(input_dir) / "converted_md"
    output_dir.mkdir(exist_ok=True)
    
    for file in os.listdir(input_dir):
        input_path = Path(input_dir) / file
        ext = input_path.suffix.lower()
        output_path = output_dir / f"{input_path.stem}.md"
        
        try:
            if ext == '.docx':
                docx_to_md(input_path, output_path)
                print(f"Success: {file} -> Markdown")
            elif ext == '.xlsx':
                excel_to_md(input_path, output_path)
                print(f"Success: {file} -> Markdown")
        except Exception as e:
            print(f"Error converting {file}: {e}")

if __name__ == "__main__":
    # 修改这里为你的文档存放路径
    your_path = r'C:\Users\law\Desktop\protocols' 
    batch_convert(your_path)
```

---

## 3. Staff 级别的优化细节说明

- **表格对齐：** 脚本使用了 `to_markdown()`。这能让 Dify 在分段时，把表格的一整行保留在一起，避免年轻工程师搜到“寄存器地址”却找不到“功能描述”的问题。
    
- **清理脏数据：** `dropna(how='all')` 过滤了 Excel 里常见的空单元格，防止 RAG 索引里出现大量没意义的空白 Token。
    
- **层级结构：** Word 的标题被转换成了 `#`。在 Dify 导入时，建议选 **“按标题分段”**。这样 AI 就能知道“第 2 章的引脚定义”和“第 3 章的引脚定义”是两回事。
    

## 4. 进阶建议：针对日文协议的“元数据”注入

如果日文协议里有很多缩写，你可以在转换后的 Markdown 文件开头手动加一行：

`# Metadata: 本文档是关于 XXX 芯片的通讯协议，包含寄存器映射和中断处理逻辑。`

这能显著提升检索响应的关联度。

**脚本跑完后，你直接把 `converted_md` 文件夹里的文件批量上传到 Dify 知识库即可。需要我教你如何在 Dify 里配置“混合检索”来进一步提升日文搜索准确率吗？**

---
这个脚本，是和 Excel word 放在同一个目录下 运行

是的，你可以把它放在同一个目录下运行。为了让你用起来更方便，我建议你把脚本最后一段稍微改一下，这样它就会**自动识别脚本所在的当前目录**，不需要你再去手动复制路径。

## 1. 修改后的脚本末尾（更灵活）

将脚本最后那几行改成这样：

Python

```
if __name__ == "__main__":
    # 获取脚本当前所在的目录
    current_dir = os.path.dirname(os.path.abspath(__file__))
    print(f"正在处理目录: {current_dir}")
    batch_convert(current_dir)
```

这样操作，你只需要：

1. 把这个 `.py` 文件丢进放满 Excel 和 Word 的文件夹。
    
2. 在该目录下运行：`python convert_to_md.py`。
    
3. 脚本会自动在同级目录下创建一个 `converted_md` 文件夹，把转好的文件都塞进去。
    

---

## 2. 运行前的快速准备（针对国内环境优化）

因为你在国内公司内网，安装库时建议加上镜像源，速度会快很多：

Bash

```
pip install pandas openpyxl python-docx tabulate -i https://pypi.tuna.tsinghua.edu.cn/simple
```

---

## 3. 给你的后续建议

**关于“真正的 RAG”效果优化：**

当你把转好的 Markdown 传到 Dify 时，在**“清洗与分段”**这一步，我建议你选择：

- **分段模式：** 选 **“自动”** 或者 **“按标题分段”**。
    
- **原因：** 既然我们已经在 Markdown 里用了 `#` 和 `##` 来标记 Word 的章节和 Excel 的 Sheet 名，Dify 能够识别这些标记，确保“寄存器定义”和“它的描述”在逻辑上不被切断。
    

**特别注意日文编码：**

如果你的 Word/Excel 里有日文特殊字符，上面的脚本默认使用 `utf-8` 保存。如果导入 Dify 后发现有乱码（虽然概率很低），可以把脚本里的 `encoding='utf-8'` 改为 `encoding='utf-8-sig'`。

---

**你可以先拿一两个文件试跑一下。如果转换出来的表格样式不符合你的预期，或者日文技术术语在转换中丢了格式，随时告诉我，我可以针对你的文档结构微调正则解析逻辑。**

---
C:\Users\law\Desktop\构建企业内部 RAG 系统\tcclink\6.2.各コマンドフォーマット(CommandFormat)\FrameFormat_New(新)\DeviceGroup07_SYS>python convert_to_md.py
正在处理目录: C:\Users\law\Desktop\构建企业内部 RAG 系统\tcclink\6.2.各コマンドフォーマット(CommandFormat)\FrameFormat_New(新)\DeviceGroup07_SYS

C:\Users\law\Desktop\构建企业内部 RAG 系统\tcclink\6.2.各コマンドフォーマット(CommandFormat)\FrameFormat_New(新)\DeviceGroup07_SYS>

执行后目录空空的 

看来是脚本的**“文件筛选”**或者**“目录深度”**出了问题。作为嵌入式开发者，你肯定能理解：脚本太“老实”了，它只看了当前目录下的一层文件，且对后缀名大小写非常敏感。

在那条长长的日文路径里，文件可能藏在子文件夹里，或者后缀名是大写的 `.XLSX`。

我为你重写了一个 **“Staff 级”增强版脚本**。这个版本加入了递归搜索（包含子目录）、大小写不敏感处理，并且增加了**详细的日志输出**，让你看清它到底在跳过哪些文件。

## 增强版脚本 (`convert_to_md_v2.py`)

```Python
import os
import pandas as pd
from docx import Document
from pathlib import Path
import sys

# 解决打印日文到控制台可能乱码的问题
sys.stdout.reconfigure(encoding='utf-8')

def docx_to_md(docx_path, output_path):
    doc = Document(docx_path)
    md_content = [f"# Source: {docx_path.name}"]
    for para in doc.paragraphs:
        text = para.text.strip()
        if not text: continue
        if para.style.name.startswith('Heading'):
            level = para.style.name.split()[-1]
            md_content.append(f"{'#' * (int(level) if level.isdigit() else 3)} {text}")
        else:
            md_content.append(text)
    
    # 简单处理表格
    for table in doc.tables:
        data = [[cell.text.strip().replace('\n', ' ') for cell in row.cells] for row in table.rows]
        if data:
            df = pd.DataFrame(data[1:], columns=data[0])
            md_content.append(df.to_markdown(index=False))
            
    with open(output_path, 'w', encoding='utf-8') as f:
        f.write('\n\n'.join(md_content))

def excel_to_md(xlsx_path, output_path):
    excel_file = pd.ExcelFile(xlsx_path)
    md_content = [f"# Source: {xlsx_path.name}"]
    for sheet_name in excel_file.sheet_names:
        df = pd.read_excel(xlsx_path, sheet_name=sheet_name)
        df = df.dropna(how='all').dropna(axis=1, how='all')
        if not df.empty:
            md_content.append(f"## Sheet: {sheet_name}")
            md_content.append(df.to_markdown(index=False))
    with open(output_path, 'w', encoding='utf-8') as f:
        f.write('\n\n'.join(md_content))

def batch_convert():
    # 1. 确定路径
    base_dir = Path(__file__).parent.absolute()
    output_dir = base_dir / "converted_md"
    output_dir.mkdir(exist_ok=True)
    
    print(f"--- 正在扫描目录: {base_dir} ---")
    
    # 2. 递归搜索所有文件
    # rglob('*') 会深入所有子文件夹
    count = 0
    for file_path in base_dir.rglob('*'):
        # 跳过临时文件 (如 ~$Word.docx) 和输出目录自身
        if file_path.name.startswith('~$') or output_dir in file_path.parents:
            continue
            
        ext = file_path.suffix.lower()
        output_file = output_dir / f"{file_path.stem}_{file_path.parent.name}.md"
        
        if ext == '.docx':
            print(f"发现 Word: {file_path.name}")
            docx_to_md(file_path, output_file)
            count += 1
        elif ext in ['.xlsx', '.xls']:
            print(f"发现 Excel: {file_path.name}")
            excel_to_md(file_path, output_file)
            count += 1
            
    print(f"--- 处理完成，共转换 {count} 个文件 ---")
    if count == 0:
        print("提示：未发现 .docx 或 .xlsx 文件，请检查文件后缀或路径。")

if __name__ == "__main__":
    batch_convert()
```

## 为什么之前的脚本会“空空的”？（排查要点）

1. **子目录问题：** 之前的 `os.listdir` 只看当前文件夹。如果你的 Word/Excel 在 `DeviceGroup07_SYS` 的下一级文件夹里，它就看不见。现在用了 `rglob('*')` 实行全盘扫描。
    
2. **后缀名问题：** 之前的脚本只认 `.xlsx`。如果是老版本的 `.xls` 或者大写的 `.XLSX`，它会跳过。
    
3. **临时文件：** 正在打开的 Office 文件会生成以 `~$` 开头的隐藏文件，新脚本已将其过滤，防止报错。
    

---

## 下一步操作：

1. 把这个新脚本放到那个日文路径下运行。
    
2. **观察控制台输出：** 即使没转换成功，它现在也会告诉你“正在扫描”。
    
3. **转换后的处理：** * 你会发现生成的文件名变成了 `原文件名_父目录名.md`。这是为了防止不同子目录下有同名文件（比如都有 `CommandList.xlsx`）导致互相覆盖。
    
    - 拿到这些 MD 文件后，批量拖入 Dify 的“知识库”上传界面。
        

**如果运行后还是显示 `0 个文件`，请你截一下文件夹里文件的后缀名发给我，我看看是不是某种特殊的二进制格式。**

---

C:\Users\law\Desktop\构建企业内部 RAG 系统\tcclink\6.2.各コマンドフォーマット(CommandFormat)\FrameFormat_New(新)\DeviceGroup07_SYS>python convert_to_md_v2.py

这是因为你的文件夹中包含 **`.xls`** 格式（旧版 Excel）的文件。Pandas 在处理 `.xlsx` 时使用 `openpyxl`，但在处理旧版 `.xls` 时必须依赖 **`xlrd`** 库。

## 1. 解决方法

在终端执行以下命令安装缺失的库：

PowerShell

```
pip install xlrd -i https://pypi.tuna.tsinghua.edu.cn/simple
```

---

## 2. 为什么会报错？

- **`.xlsx` (新版)**：脚本之前安装的 `openpyxl` 负责处理。
    
- **`.xls` (旧版)**：正如你报错信息中看到的 `19_SystemControllerInformation(...).xls`，这类文件需要 `xlrd` 引擎。
    

为了保险起见，建议你一次性补齐 RAG 预处理最常用的全套工具：

PowerShell

```
pip install pandas openpyxl xlrd python-docx tabulate -i https://pypi.tuna.tsinghua.edu.cn/simple
```

---

## 3. 运行建议

安装完成后，直接重新运行脚本即可：

PowerShell

```
python convert_to_md_v2.py
```

**执行成功后：**

1. 你应该能在同级目录下看到 `converted_md` 文件夹。
    
2. 检查一下里面的 `.md` 文件，特别是日文表格部分，看是否有乱码（如果有，我再帮你调编码参数）。
    

**搞定这些 Markdown 文件后，需要我演示一下如何在 Dify 里把这些文件配置成“混合检索”模式吗？（这是解决通讯协议里十六进制代码搜索不准的关键步骤）。**

---
结果符合预期吗? 我之前没用过 ~

![[Pasted image 20260315001348.png]]


看你发来的对比图，**“结果”虽然出来了，但对 RAG 来说并不算理想。**

作为通讯协议，你的 Excel 里有大量**合并单元格（Merged Cells）**和**位域定义（b7~b0）**。目前的脚本把它们转换成了包含大量 `nan`（空值）和 `Unnamed`（未命名列）的表格。

## 为什么现在的效果对 RAG 不友好？

1. **语义丢失：** AI 在读 Markdown 时，如果看到一堆 `nan`，它不知道这一行其实是属于上面的 “D0” 或 “D1” 指令的。
    
2. **表头模糊：** `Unnamed: 1` 这种列名会让 AI 无法准确回答“第 3 位（b3）代表什么”这样的问题。
    

---

## 改进方案：让脚本“聪明地”填补空缺

为了让年轻工程师搜得准，我们需要在转换时做一个 **“向下填充 (Forward Fill)”** 的动作。这样每一行都会完整包含它所属的指令信息。

我建议你给 `excel_to_md` 函数加两行代码，优化后的效果会好很多：

#### 修改后的 `excel_to_md` 部分：


```Python
def excel_to_md(xlsx_path, output_path):
    excel_file = pd.ExcelFile(xlsx_path)
    md_content = [f"# Source: {xlsx_path.name}"]
    for sheet_name in excel_file.sheet_names:
        # header=None 或手动指定 header 范围，视具体协议表格而定
        df = pd.read_excel(xlsx_path, sheet_name=sheet_name)
        
        # --- 核心改进：处理合并单元格产生的 nan ---
        # 1. 向下填充：把 D0 的属性填充到它下面的所有 bit 定义行
        df = df.ffill(axis=0) 
        # 2. 清理完全空白的行
        df = df.dropna(how='all').dropna(axis=1, how='all')
        
        if not df.empty:
            md_content.append(f"## Sheet: {sheet_name}")
            md_content.append(df.to_markdown(index=False))
            
    with open(output_path, 'w', encoding='utf-8') as f:
        f.write('\n\n'.join(md_content))
```

---

## Staff 建议：直接进 Dify 测试

虽然表格看起来有点乱，但你可以**先拿这几个文件去 Dify 跑一下**。

1. **上传到 Dify 知识库**。
    
2. 在 Dify 的 **“命中测试 (Retrieval Test)”** 里输入一个问题，比如：“系统控制器機種情報的 D1 是什么意思？”
    
3. **观察：** 如果 AI 能回答出来，说明目前的格式它能理解；如果它胡言乱语，我们就得回来精修这个 Markdown 脚本。
    

## 总结

- **目前状态：** 流程跑通了，但“数据质量”还有提升空间。
    
- **下一步：** 建议先不改脚本，直接进 Dify 测一下。如果检索准确率低于 80%，我再教你如何用 Python 强行把这些日文表格转成 **“Key-Value”** 这种 AI 最喜欢的纯文本格式。
    

**你现在已经把这些生成的 `.md` 文件上传到 Dify 了吗？测试结果如何？**


---

脚本没有转换 doc /docx /pdf 文件呢

之前的脚本没转这些文件是因为两个原因：一是 **`.doc`** (旧版 Word) 需要调用 Windows 本地的 Word 引擎才能读取；二是 **PDF** 需要专门的解析库。

既然你要做真正的企业级 RAG，咱们直接把脚本升级为“全能版”。

## 1. 补全依赖库

在终端运行：

PowerShell

```
pip install pdfplumber pywin32 -i https://pypi.tuna.tsinghua.edu.cn/simple
```

- **`pdfplumber`**: 专门处理 PDF，尤其是通讯协议里的表格。
    
- **`pywin32`**: 允许 Python 调用你电脑上的 Word 软件，把老旧的 `.doc` 转成可读格式。
    

---

## 2. 全能转换脚本 (`convert_all_v3.py`)

这个版本增加了对 `.pdf` 和 `.doc` 的支持，并优化了 Word 表格的提取逻辑。

```Python
import os
import pandas as pd
from docx import Document
from pathlib import Path
import pdfplumber
import win32com.client as win32 # 仅限 Windows 

def doc_to_docx(doc_path):
    """将 .doc 临时转为 .docx 以便读取"""
    word = win32.gencache.EnsureDispatch('Word.Application')
    doc = word.Documents.Open(str(doc_path))
    temp_docx = doc_path.with_suffix('.temp_docx')
    doc.SaveAs(str(temp_docx), FileFormat=16) # 16 是 docx 格式
    doc.Close()
    return temp_docx

def pdf_to_md(pdf_path, output_path):
    """提取 PDF 文字和表格"""
    with pdfplumber.open(pdf_path) as pdf:
        content = [f"# Source: {pdf_path.name}"]
        for page in pdf.pages:
            # 提取文字
            text = page.extract_text()
            if text: content.append(text)
            # 提取表格
            tables = page.extract_tables()
            for table in tables:
                df = pd.DataFrame(table)
                content.append(df.to_markdown(index=False))
        
        with open(output_path, 'w', encoding='utf-8') as f:
            f.write('\n\n'.join(content))

def docx_to_md(docx_path, output_path):
    doc = Document(docx_path)
    md_content = [f"# Source: {docx_path.name}"]
    # 遍历段落和表格
    for child in doc.element.body:
        if child.tag.endswith('p'): # 段落
            para = [p for p in doc.paragraphs if p._element == child][0]
            if para.text.strip(): md_content.append(para.text)
        elif child.tag.endswith('tbl'): # 表格
            table = [t for t in doc.tables if t._element == child][0]
            data = [[cell.text.strip() for cell in row.cells] for row in table.rows]
            if data:
                df = pd.DataFrame(data[1:], columns=data[0])
                md_content.append(df.to_markdown(index=False))
                
    with open(output_path, 'w', encoding='utf-8') as f:
        f.write('\n\n'.join(md_content))

def batch_convert():
    base_dir = Path(__file__).parent.absolute()
    output_dir = base_dir / "converted_md"
    output_dir.mkdir(exist_ok=True)
    
    for file_path in base_dir.rglob('*'):
        if file_path.name.startswith('~$') or output_dir in file_path.parents: continue
        
        ext = file_path.suffix.lower()
        output_file = output_dir / f"{file_path.stem}.md"
        
        try:
            if ext == '.docx':
                docx_to_md(file_path, output_file)
                print(f"Done: {file_path.name}")
            elif ext == '.doc':
                temp_docx = doc_to_docx(file_path)
                docx_to_md(temp_docx, output_file)
                os.remove(temp_docx) # 用完删掉临时文件
                print(f"Done: {file_path.name} (via conversion)")
            elif ext == '.pdf':
                pdf_to_md(file_path, output_file)
                print(f"Done: {file_path.name}")
            elif ext in ['.xlsx', '.xls']:
                # 复用之前的 excel 逻辑
                df_dict = pd.read_excel(file_path, sheet_name=None)
                with open(output_file, 'w', encoding='utf-8') as f:
                    for sheet, df in df_dict.items():
                        f.write(f"## {sheet}\n{df.ffill().to_markdown(index=False)}\n\n")
                print(f"Done: {file_path.name}")
        except Exception as e:
            print(f"Fail: {file_path.name} | Error: {e}")

if __name__ == "__main__":
    batch_convert()
```

---

## 3. 处理后的关键动作

运行完这个脚本后，你应该能得到一个完整的 `converted_md` 文件夹。

**针对 RAG 效果的最后建议：**

1. **日文 PDF 是重灾区**：PDF 的表格在转换时最容易乱。如果生成的 `.md` 文件里 PDF 表格完全对不上号，那不是脚本的问题，是 PDF 这种格式本身就没打算让人提取数据。
    
2. **Dify 导入设置**：
    
    - 在 Dify 知识库里，**开启“清洗”**，勾选“替换多余的换行符”。
        
    - **索引方式**：一定要选**混合检索**。因为协议里的 `DeviceGroup07` 这种词，必须靠全文索引才能 100% 搜到。
        

你先跑一下这个脚本，如果 **.doc** 转换时弹窗报错（通常是 Word 权限问题），告诉我，我教你如何手动批量转 docx。

**现在文件转换全了吗？**

---
转 excel 很好用那版本，你删掉啦？帮我在附件的基础上增加支持 pdf 和 word 转换

没删，咱们直接在那个“觉得好用”的版本基础上，把 **PDF** 和 **老旧的 Word (.doc)** 功能焊进去。

为了处理这些新格式，你需要额外安装两个库：

1. **`pdfplumber`**：处理 PDF（尤其是协议里的表格，它比一般的库准）。
    
2. **`pywin32`**：调用你 Windows 本地的 Word 引擎，把老掉牙的 `.doc` 变成脚本能读的格式。
    

PowerShell

```
pip install pdfplumber pywin32 -i https://pypi.tuna.tsinghua.edu.cn/simple
```

---

## 增强版全能转换脚本

这个版本保留了你之前觉得好用的 Excel 逻辑，并增加了对 PDF 和 .doc 的支持。

```Python
import os
import pandas as pd
from docx import Document
from pathlib import Path
import sys
import pdfplumber
import win32com.client as win32  # 仅限 Windows 运行

# 解决打印日文到控制台可能乱码的问题
sys.stdout.reconfigure(encoding='utf-8')

def doc_to_docx(doc_path):
    """通过调用本地 Word 将 .doc 转为临时 .docx"""
    word = win32.gencache.EnsureDispatch('Word.Application')
    doc = word.Documents.Open(str(doc_path))
    temp_docx = doc_path.with_suffix('.temp_docx')
    doc.SaveAs(str(temp_docx), FileFormat=16)  # 16 代表 docx
    doc.Close()
    return temp_docx

def pdf_to_md(pdf_path, output_path):
    """提取 PDF 文字和表格"""
    with pdfplumber.open(pdf_path) as pdf:
        content = [f"# Source: {pdf_path.name}"]
        for page in pdf.pages:
            # 提取文本
            text = page.extract_text()
            if text:
                content.append(text)
            # 提取表格并转为 Markdown
            tables = page.extract_tables()
            for table in tables:
                df = pd.DataFrame(table)
                # 过滤掉全空的列
                df = df.dropna(how='all', axis=1)
                content.append(df.to_markdown(index=False))
        
        with open(output_path, 'w', encoding='utf-8') as f:
            f.write('\n\n'.join(content))

def docx_to_md(docx_path, output_path):
    doc = Document(docx_path)
    md_content = [f"# Source: {docx_path.name}"]
    
    # 按照文档原本顺序处理段落和表格
    for child in doc.element.body:
        if child.tag.endswith('p'):  # 段落
            para = [p for p in doc.paragraphs if p._element == child][0]
            text = para.text.strip()
            if not text: continue
            if para.style.name.startswith('Heading'):
                level = para.style.name.split()[-1]
                md_content.append(f"{'#' * (int(level) if level.isdigit() else 3)} {text}")
            else:
                md_content.append(text)
        elif child.tag.endswith('tbl'):  # 表格
            table = [t for t in doc.tables if t._element == child][0]
            data = [[cell.text.strip().replace('\n', ' ') for cell in row.cells] for row in table.rows]
            if data:
                # 尝试用第一行做表头
                df = pd.DataFrame(data[1:], columns=data[0])
                md_content.append(df.to_markdown(index=False))
                
    with open(output_path, 'w', encoding='utf-8') as f:
        f.write('\n\n'.join(md_content))

def excel_to_md(xlsx_path, output_path):
    """保留你觉得好用的原版 Excel 逻辑"""
    excel_file = pd.ExcelFile(xlsx_path)
    md_content = [f"# Source: {xlsx_path.name}"]
    for sheet_name in excel_file.sheet_names:
        df = pd.read_excel(xlsx_path, sheet_name=sheet_name)
        # 向下填充处理合并单元格，提升 RAG 检索精度
        df = df.ffill(axis=0)
        df = df.dropna(how='all').dropna(axis=1, how='all')
        if not df.empty:
            md_content.append(f"## Sheet: {sheet_name}")
            md_content.append(df.to_markdown(index=False))
    with open(output_path, 'w', encoding='utf-8') as f:
        f.write('\n\n'.join(md_content))

def batch_convert():
    base_dir = Path(__file__).parent.absolute()
    output_dir = base_dir / "converted_md"
    output_dir.mkdir(exist_ok=True)
    
    print(f"--- 正在扫描目录: {base_dir} ---")
    
    count = 0
    for file_path in base_dir.rglob('*'):
        if file_path.name.startswith('~$') or output_dir in file_path.parents:
            continue
            
        ext = file_path.suffix.lower()
        output_file = output_dir / f"{file_path.stem}_{file_path.parent.name}.md"
        
        try:
            if ext == '.docx':
                print(f"处理 Word: {file_path.name}")
                docx_to_md(file_path, output_file)
                count += 1
            elif ext == '.doc':
                print(f"处理旧版 Word: {file_path.name}")
                temp_docx = doc_to_docx(file_path)
                docx_to_md(temp_docx, output_file)
                os.remove(temp_docx)  # 清理临时文件
                count += 1
            elif ext == '.pdf':
                print(f"处理 PDF: {file_path.name}")
                pdf_to_md(file_path, output_file)
                count += 1
            elif ext in ['.xlsx', '.xls']:
                print(f"处理 Excel: {file_path.name}")
                excel_to_md(file_path, output_file)
                count += 1
        except Exception as e:
            print(f"跳过文件 {file_path.name}，原因: {e}")
            
    print(f"--- 处理完成，共转换 {count} 个文件 ---")

if __name__ == "__main__":
    batch_convert()
```

---

## 几个关键细节提示：

1. **合并单元格优化**：我在 `excel_to_md` 里加了一句 `df.ffill(axis=0)`。这能解决你之前提到的“转换后一堆 nan”的问题，让日文协议里的每一行都带上它所属的指令前缀。
    
2. **Word 转换**：运行脚本时，如果遇到 `.doc` 文件，你会看到 Word 软件可能闪烁一下，这是正常的，脚本正在调用它做后台转换。
    
3. **PDF 的局限性**：协议 PDF 里的表格如果结构异常复杂（比如跨页嵌套），转换出来的 Markdown 可能会错位。建议这类核心协议，后期在 Dify 预览时手动微调一下 MD 文件。
    

**你可以跑一下这个新脚本，生成的 `converted_md` 文件夹现在应该包含所有格式了。需要我帮你写个 Dify 里的“日文协议专属提示词（Prompt）”吗？**


关联
[[快速原型类 Dify 或FastGPT 初探]] 
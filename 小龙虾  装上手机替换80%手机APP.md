
利用如下api，想象空间无限，对比PC版本有无与伦比的优势  
一、基础服务
- termux-api-start ：启动 API 服务  
- termux-api-stop ：停止 API 服务  
- termux-job-scheduler ：定时/周期执行脚本（Android N+ 最短15分钟）  
- termux-toast ：弹出系统提示  
- termux-notification ：发送系统通知  
- termux-notification-remove ：移除指定 ID 通知  
- termux-dialog ：弹出交互对话框（文本/单选/多选/日期/时间/语音等）  
二、设备信息  
-  termux-battery-status ：电池状态（电量/温度/充电/健康）  
-  termux-brightness ：设置屏幕亮度（0–255 或 auto）  
-  termux-device-info ：设备型号/厂商/Android 版本等  
-  termux-telephony-deviceinfo ：电话设备信息  
-  termux-telephony-cellinfo ：基站/蜂窝信息  
-  termux-wifi-connectioninfo ：当前 Wi‑Fi 连接信息  
-  termux-wifi-scaninfo ：Wi‑Fi 扫描结果  
-  termux-audio-info ：音频输出/蓝牙/耳机状态  
  
三、传感器  
-  termux-sensor ：获取加速度/陀螺仪/光线/距离/心率等传感器数据  
-  termux-location ：获取 GPS/网络 定位（一次/持续）  
  
四、相机与媒体  
- termux-camera-info ：列出摄像头（前后/分辨率/能力）  
​- termux-camera-photo ：调用摄像头拍照  
​- termux-microphone-record ：麦克风录音  
​- termux-media-player ：播放/暂停/停止音频  
​- termux-media-scan ：媒体库扫描（让新文件显示在相册）  
​- termux-tts-speak ：文本转语音朗读

- https://mp.weixin.qq.com/s/8TqHQr6xpK36LElpbctQOg
- 
五、通信与联系人
-  termux-call-log ：通话记录
-  termux-telephony-call ：拨打电话
-  termux-sms-list ：读取短信
-  termux-sms-send ：发送短信
-  termux-contact-list ：读取所有联系人

六、剪贴板与文件
-  termux-clipboard-get ：读取系统剪贴板
-  termux-clipboard-set ：写入系统剪贴板
-  termux-download ：调用系统下载管理器
-  termux-open ：用系统默认应用打开文件/链接
-  termux-share ：分享文本/文件到其他应用
-  termux-saf-* ：SAF 存储访问（创建/读/写/删/列目录）

七、红外与生物识别
-  termux-infrared-frequencies ：查询红外支持频率
-  termux-infrared-transmit ：发送红外信号
-  termux-fingerprint ：指纹验证

八、其他
-  termux-vibrate ：设备震动（时长/模式）
-  termux-volume ：调节各音量通道（音乐/通知/闹钟等）
-  termux-wallpaper ：设置壁纸
-  termux-nfc ：NFC 标签读写（部分设备）



# 在 Android 手机上运行 OpenClaw 完整教程

## 🧪 实验环境

| 项目              | 配置            |
| --------------- | ------------- |
| **手机型号**        | Redmi K50 Pro |
| **运行内存**        | 8GB           |
| **OpenClaw 版本** | v2.6-3（最新版）   |

---

## 🧪 对接飞书效果

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/RybDvPzFqH1NYq21QuODicnq5AY08vIQhGSXWazqicic4qGMPItDTBO5yggiaqRNWtaADnHJxEDGqiarUDHPTUDdgjTbViatHUqDHeoPfpQrgZa7E/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

## 🧪 安装完成效果

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/RybDvPzFqH3Us1ib6NFu2crUTAeScct5pgOpTZUhYxu49OlR8odvZMh9Oo6zlq1fCnGBZCwDia05BWh7CeY98HeoaYiaVE82UBlS2DBO5HKp7E/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

## 📦 准备工作

### 需要安装的应用

1. **Termux**      - 安卓端的 Linux 终端环境
2. **Termux:API**     - 用于控制安卓硬件的扩展插件, 如 termux-battery-status 查看电池状态

### 🔽 下载方式

推荐通过 **F-Droid** 应用商店安装：
1. 先下载安装 F-Droid
2. 在 F-Droid 中搜索并安装上述两款应用

> 💡 **提示**：F-Droid 是开源应用商店，比直接下载 APK 更安全可靠

---

## 🚀 安装步骤详解

### 步骤一：配置 Termux 远程访问

在手机上打开 Termux，依次执行以下命令：
```json
# 安装身份验证工具pkg install termux-auth
# 设置登录密码（会提示输入两次）passwd
# 启动 SSH 服务sshd
```

✅ **完成后**：手机已开启 SSH 服务，端口为 `8022`

---
### 步骤二：电脑端连接手机

**推荐方案**：电脑开启热点，手机连接，确保在同一局域网
```json
# 连接命令（IP 地址请替换为你手机的实际 IP）ssh -p 8022 192.168.137.219
# 输入步骤一中设置的密码即可登录
```

### 步骤三：环境初始化

连接成功后，在 Termux 中执行：
```json
# 申请存储权限（会弹出授权窗口）termux-setup-storage
# 更换软件源（选择清华/中科大镜像，速度更快）termux-change-repo
# 安装必要依赖pkg install vim git nodejs-lts python make clang build-essential openssh termux-api -y
```

### 步骤四：安装 OpenClaw
```json
npm install -g openclaw --verbose --no-optional
```

### 步骤五：修复路径问题（关键步骤）

⚠️ **直接运行会报错**，因为 Termux 不支持系统 `/tmp` 目录，需要执行以下修复脚本：
```json
# ========== 第一步：创建合法的临时目录 ==========
mkdir -p $PREFIX/tmp/openclaw
# ========== 第二步：批量替换源码中的非法路径 ==========
sed -i "s|/tmp/openclaw|$PREFIX/tmp/openclaw|g" $(npm root -g)/openclaw/dist
# ========== 第三步：永久配置环境变量 ==========
# 设置 Node.js 临时目录为 Termux 合法路径
echo "export TMPDIR=\$PREFIX/tmp" >> ~/.bashrc
# 强制 Node.js 使用 IPv4 解析（解决 DNS 报错）
echo "export NODE_OPTIONS='--dns-result-order=ipv4first'" >> ~/.bashrc
# ========== 第四步：用 Node.js 脚本深度修复路径 ==========
node -e "
const fs = require('fs');
const path = require('path');
const openclawDir = path.join(process.env.PREFIX, 'lib/node_modules/openclaw/dist');

fs.readdirSync(openclawDir).forEach(file => {
  if (file.endsWith('.js')) {
    const filePath = path.join(openclawDir, file);
    let content = fs.readFileSync(filePath, 'utf8');
    content = content.replace(/os\\.tmpdir\\(\\)/g, \"'\" + process.env.PREFIX + \"/tmp'\");
    content = content.replace(/\\/tmp\\/openclaw/g, process.env.PREFIX + \"/tmp/openclaw\");
    fs.writeFileSync(filePath, content, 'utf8');
    console.log('✓ 修复完成：', filePath);
  }
});
console.log('\\n✅ OpenClaw 路径修复全部完成！');
# ========== 第五步：让环境变量立刻生效 ==========
source ~/.bashrc
echo -e "\\n🎉 所有修复已完成！现在可以正常使用 openclaw 命令了。"
# 安装守护进程
openclaw onboard --install-daemon
```
### 步骤六：获取 Token 并启动服务

```json
# 获取访问 token（会显示 URL 和 token）openclaw dashboard --no-open# 启动网关服务（保持运行）openclaw gateway --force
```
### 步骤七：配置 API 密钥

#### 方式 A：使用文件管理器（推荐）
1. 下载配置文件（后台回复「json」自动获取）
2. 用安卓自带文件管理器替换：
    ```json
    /data/data/com.termux/files/home/.openclaw/openclaw.json
    ```
#### 方式 B：使用 SFTP 传输

```json
# 通过 SFTP 连接（使用 xftp 等工具）
sftp://192.168.137.219:8022
# 上传路径：/data/data/com.termux/files/home/.openclaw/openclaw.json
```
---
## ✅ 配置完成！

替换 JSON 配置文件后，OpenClaw 就可以正常运行了！

---
## ⚠️ 重要注意事项

| 注意点      | 说明                                                       |
| -------- | -------------------------------------------------------- |
| **插件问题** | v2.6-3 已内置飞书插件，无需再执行 `npm install` 安装插件                  |
| **网关服务** | 不支持 `gateway install` 命令，使用 `gateway --force` 即可，不影响正常使用 |
| **保持运行** | 启动 `gateway` 后不要关闭 Termux，否则服务会停止                        |
| **后台保活** | 建议在手机设置中将 Termux 设为「允许后台运行」和「电池优化白名单」                    |

---
## 🎯 快速检查清单
-  已安装 Termux 和 Termux:API
-  已完成 SSH 配置并测试连接
-  已执行路径修复脚本且无报错
-  已获取 token 并记录
-  已替换 openclaw.json 配置文件
-  网关服务已正常启动

---

_如有问题欢迎在评论区留言交流，json替换文件粉丝，后台私信留言json获取！_ 🚀
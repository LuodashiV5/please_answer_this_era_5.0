

算法篇：如何用QT实现FFT变换
![[Pasted image 20260510153031.png]]

FFT，也就是快速傅里叶变换，它能把常见的时域信号转换成频域，换个角度再进行分析。比如主动降噪耳机，就是靠FFT分析环境噪音频率，发出反向声波抵消杂音，这样传到我们耳朵里的就只有纯净音乐了。
最近闲来无事，试着用QT的方式模拟了一下FFT变换。
大概的思路是这样的：
1. FFT算法
自己写起来肯定很麻烦，找了一个现成的FFTW 库，可以通过后面这个link下载相应的库文件http://www.fftw.org/download.html
使用方法如下：

    输入输出数据分配内存

    填入实数信号到 in

    创建FFT计划：p=fftw_plan_dft_1d();
    执行FFT：fftw_execute(p);
    释放：fftw_destroy_plan(); fftw_free();

2. 模拟数据

    输入信号：因为现在没有实际数据，所以做了1个15KHz频率的模拟电流数据来作为输入信号，其实也就是一个特定频率的正弦波信号。qSin(2*M_PI*15000*t)，实际使用中，为了增加一点动态效果，我有往输入信号中加了点随机数生成的低频信号，就当作噪声吧。
    频率轴范围：0~30KHz
    采样率：根据奈奎斯特采样定理，要完整还原30KHz以内的信号，采样率需要是最高值的2倍。所以就定为60000吧。
    采样点数：一般是2的整数幂，就选8192吧。因为实时显示的时候，每一帧所需时间T=8192/60000=0.136s，大约每秒8~9帧的数据，比较顺畅了。

3. 控件显示
QCustomPlot，可以说它是QT专用的2D绘图神器啦。它适配性比较强，普遍适用QT的各种高低版本。波形图、频谱图、各类曲线图，都是它的对口专业。它的官网下载地址：https://www.qcustomplot.com/
使用方法:

    清除旧数据：customPlot->clearGraphs();
    添加数据：
    customPlot->addGraph();     
    customPlot->graph(0)->setData(freqX, fftResult, N); 
    设置颜色/样式     
    customPlot->graph(0)->setPen(QPen(Qt::blue));     
    customPlot->graph(0)->setBrush(QBrush(QColor(0,0,255,20)));// 
    坐标轴设置     
    customPlot->xAxis->setLabel("频率 (Hz)");     
    customPlot->yAxis->setLabel("幅度");
    customPlot->xAxis->setRange(0,30000); 
    customPlot->yAxis->setRange(0,1);
    重绘：customPlot->replot();

4. 动态效果
利用QT的信号槽概念，做了一个定时器。每500ms就定时去调用一下FFT处理（FFTW 库），就是把掺进了噪声的正弦时域信号转化成频域信号；然后利用专业的图形控件QCustomPlot，把频域信号显示出来。
5. 结尾
到这里，FFT 快速傅里叶变换的核心思路就讲完了。从信号频率、采样率、采样点数的选择，到利用FFTW进行时域-频域转换，再用 QCustomPlot 画出清晰的频谱图，每一步都是工程里最实用的内容。无论是做信号分析、音频处理、振动检测，还是 Qt+FFTW 的实际项目，掌握这些就可以快速上手。 FFT 看似复杂，其实拆解开就是几个关键公式和配置逻辑。现在写出来，给自己做个总结，也给大家分享一下。




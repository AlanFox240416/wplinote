## 在这篇文章中记录一下模型训练和声音克隆流程
## 前言
项目开源地址：https://github.com/RVC-Boss/GPT-SoVITS
官方中文教程文档：https://www.yuque.com/baicaigongchang1145haoyuangong/ib3g1e
准备：**wav格式**的音频，训练需要 **不少于6G**显存，推理需要 **不少于4G**显存。
整合包：https://www.123pan.com/s/5tIqVv-GVRcv.html

> 更新：下载[main代码](https://github.com/RVC-Boss/GPT-SoVITS/archive/refs/heads/main.zip)，用GPT-SoVITS-main里的文件替换整合包中的文件；

## 人声伴奏分离
有**背景音**的音频先进行人声伴奏分离
下载[uvr5客户端](https://github.com/Anjok07/ultimatevocalremovergui/releases)，安装并运行。
### 提取人声
<details><summary>在urv5操作界面中选择输入音频路径、输出文件夹路径、格式勾选wav、处理算法MDX-Net、模型Bs-Roformer-Viperx-1297、勾选GPU Conversion和Vocals Only，其他保持默认。
</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/4dc05227-b2ad-4e43-9b3f-ad0d7a802d87)

</p>
</details> 
设置好后，点击Process Progress，进行人声提取。

如果有报错：An Error Occurred: **ZeroDivisionError**
原因：输入的多个音频中有几个**音频的时长太短。**
**提取的人声比较干净就跳过接下来的去混响和降噪。**

### 去混响
将提取的人声再输入去混响
<details><summary>选择提取的人声作为输入音频、选择输出文件夹路径、处理算法改为VR Architecture、模型改为UVR-De-Echo-Normal（轻度混响）、取消勾选Vocals Only，其他保持不变。
</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/17d39155-2ec4-47d8-9a73-296856628b46)

</p>
</details> 
设置好后，点击Process Progress，进行去混响。

注意：去混响时，处理1个音频，会输出2个音频。一个是Echo（混响），一个是No Echo（没有混响）。

### 降躁（对提取的人声比较干净可以跳过）
<details><summary>选择No Echo的音频作为输入音频、选择输出文件夹路径、模型改为UVR-DeNoise，其他保持不变。
</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/9f4870f8-7940-4cfd-91f0-5b8026f86bc7)

</p>
</details> 
设置好后，点击Process Progress，进行降噪。

降噪，处理1个音频，也会输出2个音频。一个是Noise（噪音），一个是No Echo（没有噪音）。

## 音频切割
使用GPT-SoVITS进行模型训练，输入的样本（音频）有长度限制，所以要进行音频切割，切分成短音频。

### 切割前预处理
使用Pr，选中音频，右键，点击音频增益。
<details><summary>音频增益，圈中的峰值振幅是最大音频的分贝，调整增益值使峰值在-9dB到-6dB之间。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/59e46378-73f8-413a-aad3-9c1ab8916b08)


</p>
</details> 

## 标注音频

### 自动打标

### 人工校对


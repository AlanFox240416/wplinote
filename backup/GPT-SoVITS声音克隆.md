## 在这篇文章中记录一下模型训练和声音克隆流程
## 前言
项目开源地址：https://github.com/RVC-Boss/GPT-SoVITS
官方中文教程文档：https://www.yuque.com/baicaigongchang1145haoyuangong/ib3g1e
准备：**wav格式**的音频，训练需要 **6G 以上**显存（笔记本配置不够，使用[AutoDL算力云](https://www.autodl.com/home)），推理需要 **4G 以上**显存。
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
报错：An Error Occurred: ZeroDivisionError
解决

### 去混响
将提取的人声再输入去混响



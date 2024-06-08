## 在这篇文章中记录一下模型训练和声音克隆流程
## 前言
项目开源地址：https://github.com/RVC-Boss/GPT-SoVITS
官方中文教程文档：https://www.yuque.com/baicaigongchang1145haoyuangong/ib3g1e
准备：**wav格式**的音频，训练需要 **6G 以上**显存（笔记本配置不够，使用[AutoDL算力云](https://www.autodl.com/home)），推理需要 **4G 以上**显存。
整合包：https://www.123pan.com/s/5tIqVv-GVRcv.html
手动更新：https://github.com/RVC-Boss/GPT-SoVITS/archive/refs/heads/main.zip，用GPT-SoVITS-main里的文件替换整合包中的文件；
自动更新：见文档
算力互联镜像
AutoDL算力云镜像
## 人声伴奏分离
有**背景音**的音频先进行人声伴奏分离



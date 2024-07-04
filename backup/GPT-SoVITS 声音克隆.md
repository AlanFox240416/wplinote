## 在这篇文章中记录一下模型训练和声音克隆流程
## 1 前言

1. 项目开源地址：https://github.com/RVC-Boss/GPT-SoVITS
2. 官方中文教程文档：https://www.yuque.com/baicaigongchang1145haoyuangong/ib3g1e
3. 准备：**wav 格式**的音频，训练需要 **不少于 6G** 显存，推理需要 **不少于 4G** 显存。
4. 整合包：https://www.123pan.com/s/5tIqVv-GVRcv.html

> 更新：下载 [main 代码](https://github.com/RVC-Boss/GPT-SoVITS/archive/refs/heads/main.zip)，用 GPT-SoVITS-main 里的文件替换整合包中的文件；

## 2 人声伴奏分离

- 这一步是在准备素材，**不好的素材练不出好模型！**，**不应低于2分钟**
- 有**背景音**的音频先进行人声伴奏分离。下载 [uvr5 客户端](https://github.com/Anjok07/ultimatevocalremovergui/releases)，安装并运行。

### 2.1 提取人声
<details><summary>在 urv5 操作界面中选择输入音频路径、输出文件夹路径、格式勾选 wav、处理算法 MDX-Net、模型 Bs-Roformer-Viperx-1297、勾选 GPU Conversion和 Vocals Only，其他保持默认。设置好后，点击 Process Progress，进行人声提取。
</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/4dc05227-b2ad-4e43-9b3f-ad0d7a802d87)

</p>
</details> 

<details><summary>如果有报错：An Error Occurred: ZeroDivisionError</summary>
<p>

原因：输入的多个音频中有几个**音频的时长太短。**

</p>
</details> 


- **提取的人声比较干净就跳过接下来的去混响和降噪。**

### 2.2 去混响

- 将提取的人声再输入去混响

    <details><summary>选择提取的人声作为输入音频、选择输出文件夹路径、处理算法改为 VR Architecture、模型改为 
 UVR-De-Echo-Normal（轻度混响）、勾选 No Echo Only（只保留没有混响的），其他保持不变。
    </summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/17d39155-2ec4-47d8-9a73-296856628b46)
    
    </p>
    </details> 

- 设置好后，点击 Process Progress，进行去混响。

### 2.3 降躁

<details><summary>选择 No Echo 的音频作为输入音频、选择输出文件夹路径、模型改为 UVR-DeNoise，取消勾选 Noise only，其他保持不变。
</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/9f4870f8-7940-4cfd-91f0-5b8026f86bc7)

</p>
</details> 

- 设置好后，点击 Process Progress，进行降噪。

### 2.4 音频调峰

- 使用Pr，选中音频，右键，点击音频增益。

<details><summary>音频增益，圈中的峰值振幅是最大音频的分贝，调整增益值使峰值在 -9dB到-6dB 之间。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/59e46378-73f8-413a-aad3-9c1ab8916b08)

</p>
</details> 

## 3 算力互联

- **云端训练，本地训练直接看“4 音频切割”**

### 3.1 创建实例
<details><summary>点击社区镜像-圈中输入“GPT-SoVITS”，搜索到冷鸟鸟的镜像，点击进入创建实例界面。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/33f6fd04-f2f4-45df-ae2c-c9e1d8ed0e9e)

<details><summary>实例名称：gsv，选择 N-3090-24（24G 显存）和 1卡，勾选同意《服务端口使用承诺书》，其他保持默认。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/e83ed594-f70e-408c-8e30-cbd57381f074)

</p>
</details> 
</p>
</details> 

### 3.2 运行实例
<details><summary>等待实例部署完成，点击 Jupyter，进入笔记本界面。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/903dc899-3262-45fe-b942-4365320992ad)

</p>
</details> 

## 4 音频切割（制作数据集）

- 使用GPT-SoVITS进行模型训练，输入的样本（音频）的时长有限制（比如 16G 的显存不能超过 16s），所以要进行音频切割，切分成短音频。

### 4.1 上传音频

- 双击**GPT-SoVITS（使用）.ipynb**；

  <details><summary>接着点击第一个代码段（移动数据），点击运行按钮，运行完成后打印“移动成功”；</summary>
  <p>
  
  ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/8c38009b-67f7-4ee9-a695-6eb07cf74f1c)
  
  </p>
  </details>

- 然后打开`/fssd/workdir/GPT-SoVITS/input`文件夹，上传原音频（上传时间因网速和音频大小而异，**耐心等待，不要重复上传**）。


### 4.2 启动Web-UI

- 运行第二段代码，启动Web-UI。
- 注意：Web-UI 有两个 URL，一个是 `loads URL`，一个是 `public URL`，选择公网链接 `public URL`（余 Web-UI 同）。

### 4.2 切割

- 切割，将上传的音频（素材）划分为短音频（数据集）。

  <details><summary>保持默认，点击开启语音切割</summary>
  <p>
  
  ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/554628f0-2e10-4a21-9014-c82aa28c8240)
  
  </p>
  </details> 

## 4 标注音频（给数据集打标）

### 4.1 自动打标
<details><summary>保持默认不变，点击开启离线批量 ASR</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/fb04c3c1-0a18-4e27-8489-c750cc33230f)

</p>
</details> 

**先等待 ASR 完成（ASR 进程结束），再勾选下面的开启打标 WebUI。**

### 4.2 人工校对

- 每进行一次修改后点击保持修改，在**翻页前点击保存修改**，全部修改结束后点击**保存文件**。

## 5 训练模型

### 5.1 数据集格式化
<details><summary>点击 1-GPT-SoVITS-TTS，输入模型名称，点击 1A-训练集格式化工具，保持默认设置不变，点击开启一键三连。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/28a0006c-a2a5-44b6-ae4a-05703b2285cf)

</p>
</details> 

### 5.2 微调训练
<details><summary>先点击 SoVITS 训练，等 SoVITS 训练结束后再点击 GPT 训练。</summary>
<p>

!![image](https://github.com/AlanFox240416/wplinote/assets/167155570/20597111-01d0-4b84-9098-874ea2052900)

</p>
</details> 

- 关于**圈中的batch-size（批次大小）**，24G 显存设置为6（每次输入6个数据集）；
- 关于**圈中的训练轮数**，SoVITS 可以高一点，GPT 不要超过10。

### 5.3 下载模型

1. 算力互联需要，本地直接推理即可；
2. 训练完成后点击中断按钮，中断 Web-UI；
3. 运行打包模型的代码，运行完成后显示“打包完成”；
4. 进入`/fssd/workdir/GPT-SoVITS/`文件夹路径下，找到 `GPT_ SoVITS_ pack.zip`，右键下载。

    <details><summary>流程示意图如下</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/4c7b47ef-be5c-4f43-a3b9-166d0492f577)
    
    </p>
    </details> 

5. 下载结束后，回到算力互联，点击删除实例。

## 6 本地推理

1. 将GPT模型（ckpt 后缀）放入 GPT_weights 文件夹，SoVITS 模型（pth 后缀）放入 SoVITS_weights文件夹；
2. 双击整合包中的 `go-webui.bat`，**等待** Web-UI 自动打开；
3. 点击 `1-GPT-SoVITS-TTS`，再点击 `1C-推理`，刷新模型路径，选择对应模型；
4. 勾选开启 TTS 推理 WebUI，**等待**打开推理界面。
    <details><summary>选择 GPT 模型和 SoVITS 模型，上传参考音频（小于10s，没有会报错），输入参考音频的文本，输入需要合成的文本，点击合成语音。</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/6ac494ef-fb5d-4ad0-91f8-d8fe9c2ff167)
    
    </p>
    </details> 

5. 合成语音时间过长，注意一下终端，是否有进行推理，或者报错。
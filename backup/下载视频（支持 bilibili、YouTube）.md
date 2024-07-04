# 在本文中介绍 B站、YouTube 视频的下载方式

# 1 yt-dlp
项目开源地址：https://github.com/yt-dlp/yt-dlp
## 1.1 FFmpeg 配置
配合FFmpeg可以进行视频转码、添加字幕等后期处理工作，项目作者强烈推荐。
1. 下载预编译版本的 [ffmpeg-master-latest-win64-gpl.zip](https://github.com/BtbN/FFmpeg-Builds/releases)，解压到自定义目录。
2. 编辑环境变量的 Path，新建一条记录到解压目录的 bin 文件下。（用户变量的Path只对当前用户有效，系统变量的Path对当前系统的所有用户都有效）

## 1.2 yt-dlp 安装

1. 下载预编译包 [yt-dlp.exe](https://github.com/yt-dlp/yt-dlp/releases/download/2024.05.27/yt-dlp.exe)
2. 编辑环境变量的 Path，新建一条记录到存放 yt-dlp.exe 的文件夹。（全局调用）

## 1.3 yt-dlp使用
使用 yt-dlp 默认下载到当前目录。直接打开到存放目录，在路径栏输入 `cmd`，回车（在存放目录打开终端）。

### 1.3.1 下载视频
`yt-dlp 网址`

### 1.3.2 下载音频
`yt-dlp -x --audio-format wav 网址`

# 2 acghelper

1. [acghelper 官网](https://acghelper.com/)
2.  可以下载B站1080P视频
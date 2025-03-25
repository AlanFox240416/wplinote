## 详细的配置文件见[Gmeek快速上手](https://blog.meekdai.com/post/Gmeek-kuai-su-shang-shou.html)，下面是一些补充说明。
## tips
问题：

1. 修改配置文件config.json无效；
2. 或者Action自动部署报错。

解决方法：**手动**部署一遍。

<details><summary>如图所示，点击Action-bulid Gmeek-Run workflows</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/e4d6ec5f-69ea-493a-bd25-c415c6d03243)

</p>
</details> 


## style

- postBody的字体大小设置，默认是20，我改为18
`"style":"<style>#postBody{font-size:18px}</style>"`

## script

- 用于自定义文章页全局JavaScript，**添加后文章标签不显示颜色**
`"script":"<script async src='//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js'></script>"`

## urlMode

- 用于定义文章链接生成模式，目前支持pinyin、issue、ru_translit

- [x]  pinyin：中文文章标题写成拼音的形式，如`https://note.wrb.me/post/she-zhi-Xshell-de-wai-guan.html`
- [x]  issue：用issue的顺序，1、2、3来代替不同文章的标题，如`https://note.wrb.me/post/2.html`
- [x]  ru_translit：专门用于俄文内容。将俄文标题通过转写转换成拉丁字母，如“привет мир”将被转写为 “privet mir”，如`https://note.wrb.me/post/privet mir.html`



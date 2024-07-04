## 详细说明见[Github文档中关于Markdown语法的说明](https://docs.github.com/zh/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)，本文是常用语法的摘要
# 一、标题与分隔线
```markdown
# 我是一级标题
## 我是二级标题
### 我是三级标题
```
- 一级标题和二级标题下面有**淡灰色横线**，三级标题下面没有淡灰色横线，如下所示
# 我是一级标题
## 我是二级标题
### 我是三级标题

# 二、issue按钮
## 按钮的说明
| 英文名 | 中文名 | 快捷键 | 用途 |
|--------|--------|--------|--------|
| Heading | 标题 | 无 | 添加三级标题 |
| Bold | 粗体 | Ctrl+B | 加粗选中的文字 |
| italic | 斜体 | Ctrl+i | 选中文字变为斜体 | 
| Quote | 引用 | 无 | 为当前行添加引用 | 
| Code | 代码 | Ctrl+E | 选中文字变为代码块 | 
| Link | 链接 | 无 | 为选中的文字添加链接或者为选中的链接添加文字说明 | 
| Numbered list | 有序列表 | 无 | 为选中的行添加有序列表 | 
| Unordered list | 无序列表 | 无 | 为选中的行添加无序列表 | 
| Task list | 任务列表 | 无 | 为选中的行添加任务列表，在方括号里加x，表示该项已经完成 | 

# 三、常用语法举例

## 命令键`/`
<details><summary>输入/，加载框如隐藏内容所示，这些命令大大提升文章的可读性</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/212a3e96-b3b0-44c2-a30b-9d10dd68f4c4)

</p>
</details> 

### 红框命令说明
| 命令 | 用途 |
|--------|--------|
| Code block | 插入针对所选语法格式化的代码块 |
| Details | 添加详细信息的标题以将内容隐藏在可见标题后面 |
| Table | 添加一个m列（column），n行（row）的表格 | 

### 示例
<details><summary>示例的内容隐藏在这里</summary>
<p>

1. 不同语言的代码块

```python
 import os
 print("hello，我是python代码块")
``` 

```c
#include <stdio.h>

int main() {
    printf("Hello, 我是C语言!\n");
    return 0;
}
``` 


2. 隐藏详细内容，保留标题

    <details><summary>这里有隐藏内容</summary>
    <p>
    
    你看到了隐藏内容
    
    </p>
    </details> 

3. 表格

| Header |
|--------|
| Cell | 

</p>
</details> 

## 标题
```markdown
### 3个#号加空格就是三级标题
```

## 粗体
- 两边各加两个星就是**粗体**
```markdown
两边各加两个星就是**粗体**
````

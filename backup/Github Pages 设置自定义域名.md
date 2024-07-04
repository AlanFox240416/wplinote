## Github Pages 设置自定义域名+仓库名是个坑
## 一、复现过程

1. 在 Github 创建名为“**AlanFox240416.github.io**”的仓库。
2. 添加自定义域名
- 需要在先在 [Github 账户的 Pages 页面](https://github.com/settings/pages)验证域名；
  <details>
  <summary><b>点击添加域名，将 TXT 记录添加都 CF</b></summary>
  
  ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/cf5ff34f-0877-44be-8bbe-7c74b32dceb6)
  
  </details>
- 项目的 Pages 页面，添加自定义域名。
  <details>
  <summary><b>输入自定义域名，在 CF 添加CNAME记录</b></summary>
  
  ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/fda6af81-37d6-4f03-9ab6-a11305a0300e)
  
  </details>

  <details>
  <summary><b>添加 CNAME 记录的模板，注意 Target (required)的值</b></summary>
  
  ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/268a1b15-ec35-4c00-beae-4597afb2be7f)
  
  </details>

## 二、bug

1. actions 别的仓库自动分配的域名会变成**note.example.cc/仓库名**


## 三、原因

- 存在名为**AlanFox240416.github.io**的GitHub仓库

## 四、解决方法

- 将名为**AlanFox240416.github.io**的仓库重命名为“任意名”，比如“wplinote”
  <details>
  <summary><b>先修改红圈中的仓库名，再点击 Rename</b></summary>
  
  ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/255c6747-281b-4b29-854d-d8ef05485687)
  
  </details>

- 重新运行工作流程 workflow（手动）
  <details>
  <summary><b>Actions->build xxx->Run workflow->绿色按钮</b></summary>
  
  ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/913165de-28b0-4faf-88e0-2c20b491d2ef)
  
  </details>


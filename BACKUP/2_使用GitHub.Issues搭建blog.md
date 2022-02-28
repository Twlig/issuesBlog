# [使用GitHub Issues搭建blog](https://github.com/Twlig/issuesBlog/issues/2)

- ### 下载脚本程序
  [https://github.com/yihong0618/gitblog](https://github.com/yihong0618/gitblog)
  ```
  git clone git@github.com:yihong0618/gitblog.git
  ```
- ### 删除和修改文件
  - 留下脚本文件，删除多余的文件，效果如下图：
  
  ![image](https://user-images.githubusercontent.com/22440467/141636354-273de557-716f-456b-a218-353a3f6c8efc.png)
  - 修改generate_readme.yml文件内容：
  
  ![image](https://user-images.githubusercontent.com/22440467/141638391-7f923e62-2ffa-43ab-ac6d-4633853690da.png)
  
  ![image](https://user-images.githubusercontent.com/22440467/141640832-e9fc9cde-ce33-49b8-972a-00437b38dccc.png)
- ### 新建GitHub远程仓库
  和脚本的设置保持一致，新仓库license选择MIT：
  ![image](https://user-images.githubusercontent.com/22440467/141642146-9a3f9fd2-a161-498b-97a4-5a674c81c17b.png)
-  ### 连接远程仓库，上传本地文件
    在本地目录下git bash，输入以下命令:
    ```
    git remote add origin git@github.com:Twlig/issuesBlog.git(刚刚新建的远程仓库的ssh地址)
    git pull origin main(默认分支是master，但是启用license监听之后，远程分支切换到了main，远程有license文件需要拉取，不能直接push，会产生冲突，所以先pull)
    git add .
    git commit -m"上传脚本文件"
    git push origin main
    ```
- ### 获取token
    - 打开链接：[https://github.com/settings/tokens](https://github.com/settings/tokens)
    - 选择个人token，勾选所有复选框，生成token.
    - 复制生成的token.
- ### 配置仓库的token
    - 打开刚刚新建的远程仓库，找到**Settings >> Secrets >> New repository secret**.
    - Name 填入 G_T与之前的 generate_readme.yml 文件中定义的变量名一致，当然也可以自行修改
    - Value 就填入刚刚的 Token 值.
- ### 使用Issues发布博客
  点击仓库顶部的 Issues 选项卡，接着点击右侧的 New issue. 文档使用markdown语法编辑就行.
  每次更新issues，git actions会执行.github\workflows下的脚本文件，自动更新readme.md
  至此大功告成！



# [MVC和MVVM的区别](https://github.com/Twlig/issuesBlog/issues/65)

## MVC

- 视图（View）：用户界面。
- 控制器（Controller）：业务逻辑
- 模型（Model）：数据保存

三者间的通信：

1. View 传送指令到 Controller
2. Controller 完成业务逻辑后，要求 Model 改变状态
3. Model 将新的数据发送到 View，用户得到反馈



## MVVM

- View是视图层，也就是用户界面。
- Model是指数据模型，泛指后端进行的各种业务逻辑处理和数据操控，主要围绕数据库系统展开。
- ViewModel由前端开发人员组织生成和维护的视图数据层。在这一层，前端开发者从后端获取得到Model数据进行转换出来，做二次封装，以生成符合View层使用预期的视图数据模型。视图状态和行为都封装在ViewModel里。这样的封装使得ViewModel可以完整地去描述View层。

![image](https://user-images.githubusercontent.com/22440467/159253271-a885ebfd-c039-4608-8163-f9a0085955e4.png)

它采用双向绑定（data-binding）：View的变动，自动反映在 ViewModel。

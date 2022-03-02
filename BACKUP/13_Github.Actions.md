# [Github Actions](https://github.com/Twlig/issuesBlog/issues/13)

### Github Actions

​	GitHub Actions 是 GitHub 的**持续集成服务**。持续集成由很多操作组成，比如抓取代码、运行测试、登录远程服务器，发布到第三方服务等等。GitHub 把这些操作就称为 actions。开发者把每个操作写成独立的脚本文件，存放到代码[仓库](https://github.com/actions/setup-node)，使得其他开发者可以引用。

- #### **使用现成的Actions**

​	每个 action 就是一个独立脚本，因此可以做成代码仓库，使用`userName/repoName`的语法引用 action。比如，`actions/setup-node`就表示github.com/actions/setup-node这个[仓库](https://github.com/actions/setup-node)，它代表一个 action，作用是安装 Node.js。事实上，GitHub 官方的 actions 都放在 [github.com/actions](https://github.com/actions) 里面。

用法如下：

```python
steps:
- uses: actions/checkout@v2
- uses: actions/setup-node@v2
  with:
    node-version: '14'
- run: npm install
- run: npm test
```

- #### 基本概念

  GitHub Actions 有一些自己的术语。

  1. **workflow** （工作流程）：持续集成一次运行的过程，就是一个 workflow。
  2. **job** （任务）：一个 workflow 由一个或多个 jobs 构成，含义是一次持续集成的运行，可以完成多个任务。
  3. **step**（步骤）：每个 job 由多个 step 构成，一步步完成。
  4. **action** （动作）：每个 step 可以依次执行一个或多个命令（action）。

- #### workflow文件

  GitHub Actions 的配置文件叫做 workflow 文件，存放在代码仓库的`.github/workflows`目录。

  workflow 文件采用 [YAML 格式](https://www.ruanyifeng.com/blog/2016/07/yaml.html)，文件名可以任意取，但是后缀名统一为`.yml`，比如`foo.yml`。一个库可以有多个 workflow 文件。GitHub 只要发现`.github/workflows`目录里面有`.yml`文件，就会自动运行该文件。

  workflow 文件的配置字段非常多，详见[官方文档](https://help.github.com/en/articles/workflow-syntax-for-github-actions)。

- #### 示例

  脚本目标：提交代码到远程仓库，自动运行测试代码并反馈结果。

  ```python
  name: CI  
  
  on:  
    push:  
      branches: [ main ]  
    pull_request:  
      branches: [ main ]  
  
  jobs:  
  
    build:  
      name: Build  
      runs-on: ubuntu-latest  
      steps:  
  
        - name: Set up Go  
          uses: actions/setup-go@v2  
          with:  
            go-version: ^1.13  
  
        - name: Check out code into the Go module directory  
          uses: actions/checkout@v2  
  
        - name: Test  
          run: go test -v ./...
  ```

  1. ##### name

     工作流程的名字，这里我们就叫 **CI**。

  2. ##### on

     触发工作流程的 GitHub 事件的名称。配置的这个工作流程，会在 `main` 分支发生了 `push`、`pull_request` 事件时被触发。

     - `push` 就是当推送新改动到该远程仓库的 `main` 分支时发生的事件
     - `pull_request` 就是向 `main` 分支发起 pull request 时发生

  3. ##### jobs

     workflow 文件的主体是jobs字段，表示要执行的一项或多项任务。我们的所配置的工作流程，一般都包含一项或多项作业，简单来说，就是在这里写下你希望这个工作流程做些什么。

  4. ##### jobs.build

     这里的 `build`，千万不要误会，它不是 GitHub Actions 的关键字/语法，它只是一个 **id**，官方管它叫 **job id**，你可以设置成其他的值。除了这个 `build`，你还可以添加更多的作业，但该工作流程只有 `build` 这一个作业。

  5. ##### jobs.<job_id>.runs-on

     运行作业的机器类型。机器可以是 GitHub 托管的运行器或自托管的运行器。我们将会使用 GitHub 提供的最新版 Ubuntu 系统的机器运行该工作流程。

  6. ##### jobs.<job_id>.steps

     `steps` 的配置才是重点，这里写的每一个步骤就是工作流程在运行时需要执行的内容，我们这个流程共三个步骤。

     - 设置 Go 运行环境，通过官方提供的 **actions/setup-go** action，可以很方便的让运行工作流程的机器支持 Go 运行环境。官方还提供了很多其他的 action 给我们使用，例如你可以很方便的支持 Node.js、Python 等环境；
     - 检出代码，我们使用官方提供的 **actions/checkout** action，可以很方便的在检出仓库代码，在这个上下文环境下，我们将会检出 `main` 分支或是将要合并到 `main` 分支的代码；
     - 这一步是用 Go 自己 test 命令来运行代码库的所有测试代码，如果测试不通过，这一步骤将会运行失败，这将会导致 Action 执行失败，后面我们再说失败了会有什么样的后果。

### CI/CD

- **CI 持续集成（Continuous Integration）**

  ​	持续集成可以帮助我们频繁的将代码变更自动合并到**主干或共享分支**。一旦变更被合并，负责集成的系统就会运行自动化测试（一般是单元测试，集成测试）来验证这些变更，确保这些变更不会对应用造成破坏，这也说明了测试环节尤为重要。若没有通过自动化测试或是代码有冲突，那么整个集成操作会失败，系统会给出反馈，此时相关的开发人员介入并进行修正，之后再开启新的一轮集成，直到这些变更被成功集成到主干或共享分支。

- **CD 持续交付（Continuous Delivery）**

  ​	持续交付可以让我们拥有一个可以随时部署的代码库。在持续集成的流程之后，引入更为复杂测试流程（如功能测试），即部署到测试服务器上，测试人员介入，进行测试环节，过程中测试人员会进行反馈，如果测试不通过则开发人员介入并进行修正。

  在大部分项目中，除了测试环境与生产环境，还会有一个预演环境，预演环境尽可能模拟生产环境，在通过了测试环境的测试后，还需要通过预演环境的测试，这样，新增的变更才会被认为在生产环境下可用。

  在流程的最后，我们得到了新的可用于部署到生产环境的代码库，这也就是用于交付的内容，其实这么说并不那么准确，因为交付的内容一般跟项目、编程语言等有关，例如现在项目是一个 Go 写的  API 服务，那么我们交付的内容很可能就是构建好的可执行文件。（**测试环境**）

- **CD 持续部署（Continuous Deployment）**

  持续部署是持续交付的延伸，在持续交付的流程后加入自动部署的环节，也就是说，如果你完整的实现了 CI/CD，你的代码变更一般会在数分钟之内被部署到**生产环境**。

### Git分支

- #### 主干

​	一般为 master 分支，可以理解为稳定的，可发布的，面向生产环境的分支。

​	我们的项目一般都是面向生产环境的，也就是说我们开发的功能最终都会被部署到生产环境，如果说我们的主干不是稳定的不是面向生产环境，那这棵“树”就会越长越歪，离我们期望的走向越来越远，与生产环境渐行渐远。

- #### 共享分支

​	团队协作流程以及 CI/CD 流程的需求，还会有专门的共享分支来接受开发人员的提交。

- #### 分支工作流程

  假设master分支，是稳定的可发布生产环境分支，dep分支，是共享分支。我目前接手了一个新功能需求，那么我就要在本地新建分支，用于开发新功能（不直接在master和dep分支写代码是防止新功能没做好影响其他分支的运行）。假设dep-my分支是我要用于开发模块功能的分支。

  1. 首先，新建dep-my分支，并切换到dep-my分支

     ```git
     git checkout -b dep-my
     ```

  2. 编写程序，添加到编好的程序到暂存区

     ```
     git add .
     ```

  3. 提交到仓库区

     ```
     git commit -m"over"
     ```

  4. 推送到远程仓库的dep-my分支

     ```
     git push origin dep-my
     ```

  5. 新功能编写完成，和dep分支合并

     ```
     git checkout dep   //切换到dep分支
     git pull origin dep //拉取最新的远程dep分支内容
     git merge dep-my   //将dep-my分支合并到dep分支
     //如果有冲突
     修改冲突内容
     git add.
     git commit -m"修改冲突，并合并"
     git push origin dep
     ```

  6. 在dep上完成测试，又可以和master合并

![image](https://user-images.githubusercontent.com/22440467/156321070-1ab0f967-bd2e-4df2-b118-33fc6806ed46.png)

参考文章：

- [[GitHub Actions 入门教程 - 阮一峰的网络日志 (ruanyifeng.com)](https://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)](https://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)
- [[GitHub Actions x 持续集成 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/337640127)](https://zhuanlan.zhihu.com/p/337640127)
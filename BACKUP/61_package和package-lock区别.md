# [package和package-lock区别](https://github.com/Twlig/issuesBlog/issues/61)

## package和package-lock区别

**package.json**: 主要用来定义项目中需要依赖的包

**package-lock.json**： 在 `npm install`时候生成一份文件，用以记录当前状态下实际安装的各个npm package的**具体来源和版本号**。

**'^'** : 放在版本号之前，表示向后兼容依赖，说白了就是在大版本号不变的情况下，下载最新版的包

项目中引入的包版本号之前经常会加^号，每次在执行npm install之后，下载的包都会发生变化，为了系统的稳定性考虑，每次执行完npm install之后会对应生成package-lock文件，该文件记录了上一次安装的具体的版本号，相当于是提供了一个参考，在出现版本兼容性问题的时候，就可以参考这个文件来修改版本号即可。
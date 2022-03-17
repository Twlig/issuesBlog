# [Vue的渲染流程（二）：patch算法](https://github.com/Twlig/issuesBlog/issues/44)

## Vue的渲染流程

![image](https://user-images.githubusercontent.com/22440467/158583912-cbfbabeb-efa2-43df-a62a-c13fce8e6e1c.png)



## patch算法

patch算法用于渲染DOM，不是暴力覆盖原有的DOM，而是通过新旧VNode对比，找出需要更新的DOM节点。

patch对DOM做的三件事：

- 创建新节点

  - oldVnode不存在，而vnode存在。一般是首次渲染时。
  - oldVnode和vnode完全不是同一个节点时。

- 删除节点

  - 节点只在oldVnode中存在，在vnode中不存在。

- 更新节点

  oldVnode和vnode是同一个节点。但是内部的东西不一样，比如子节点和文本。



1. ### 创建新节点

   只有三种类型的节点会被创建并插入到DOM中：元素节点、注释节点、文本节点。

   判断vnode节点类型：元素节点有tag属性；isComment为true则是注释节点；上述都不满足则是文本节点。

   - 元素节点

     因为存在子节点，因此是一个递归的过程。createElement创建当前节点，并通过appendChildren插入父节点中，遍历当前节点vnode的children属性创建子节点，并插入子节点。

   - 注释节点

     createComment创建注释节点，并通过appendChildren插入父节点中。

   - 文本节点

     通过createTextNode创建文本节点，并插入父节点。

2. ### 删除节点

   通过nodeOps封装的节点操作，removeChildren移除节点。nodeOps封装主要是为了让框架渲染机制和DOM解耦，实现跨平台渲染，在不同平台下调用相同的操作。（主要是浏览器对DOM的操作比如删除，会有不同的实现方法，而在nodeOps中封装了各种浏览器的操作方式，用的时候只需要调用nodeOps封装的操作，nodeOps会在内部判断该浏览器的需要用的是哪种实现方法。这样就不需要在开发的时候对浏览器进行判断）

3. ### 更新节点

   - #### 静态节点

     如果是静态节点则跳过更新环节。

   - #### vnode有文本属性

     文本节点。如果oldVnode和vnode的文本相同则不更新，如果文本不同则以vnode的文本为准更新textContent。

   - #### vnode无文本属性

     元素节点，考虑子节点。

     1. ##### 有children

        - oldVnode有无children属性。旧节点有文本则，清空文本节点，插入vnode的children中的新子节点。
        - oldVnode有children属性。对比vnode和oldVnode的children属性。**更新子节点详见后续。**

     2. ##### 无children

        说明当前vnode是空节点。如果oldVnode中有children就删除子节点。



### 更新子节点



#### 更新策略

更新子节点大致分为4种操作：更新节点、新增节点、删除节点、移动节点

新旧子节点列表vnode通过循环进行对比更新。有children和oldChildren两个子列表。循环children对比oldChildren中是否有对应的子节点，进行更新、新增和移动操作。children都处理完成，对最后oldChildren剩下未处理的节点进行删除操作。边对比vnode边执行DOM操作。

```javascript
for(const item of children) {//item按照节点顺序依次遍历
    for(const itemOld of oldChildren) {
        item in oldChildren:
        //更新或者移动，并插入真实DOM中，对应的itemOld和item打上已处理标签
        item not in oldChildren:
        //创建节点，并插入真实DOM中，item打上已处理标签
    }
    //剩余未处理的itemOld，说明新的vnode中不存在这些节点，执行删除DOM操作
    for(const itemOld of oldChildren) {
        delete itemOld.DOM  //删除itemOld对应的DOM节点
    }
}
```

注意：每次创建或者移动节点，把节点插入真实DOM中，该节点是**插入所有未处理的节点前面**，而不是已处理的节点后面。这里的**未处理节点指的是旧虚拟节点的未处理节点**。我们在循环的时候只能以旧虚拟节点为基础，不能直接和真实DOM做对比。

使用虚拟节点对比，不是真实的DOM对比，是在旧虚拟节点的基础上进行插入。

比如旧节点为1256，新节点是12346空格前表示已处理后表示未处理：

##### 错误示范：

新12 （3） 46，旧12 56。在旧12后面插入3。 ——》 DOM 12356

新123（4）6，旧12 56。在旧12后面插入4。——》DOM 124356

可以看到出错了，12是已处理节点，插到12后面DOM就不对了。应该是插入56前面就对了。

##### 正确示范：

新12 （3） 46，旧12 56。在旧56前面插入3。 ——》 DOM 12356

新123（4）6，旧12 56。在旧56前面插入4。——》DOM 123456

新123（6），旧12 5（6）。移动，把6移到旧vnode未处理节点前，即5前面。——》DOM 123465

新vnode循环完毕，旧**vnode**还剩下5，删除5。——》DOM 12346



#### 优化子节点更新策略

前面的更新策略要循环整个oldChildren来判断当前children的item和oldChildren的item中的关系。再决定是创建、更新、移动或删除。

Vue中有个快速查找策略，当快速查找策略没有找到节点，再采用前面的循环查找。



快速查找涉及的概念：

**新前**：newChildren中所有未处理的第一个节点

**新后**：newChildren中所有未处理的最后一个节点

**旧前**：oldChildren中所有未处理的第一个节点

**旧后**：oldChildren中所有未处理的最后一个节点



快速查找方式：

- ##### 新前与旧前

  尝试使用新前节点和旧前节点对比，看这两个节点是不是同一个节点。如果是同一个节点，由于新前和旧后位置相同，所以**不需要执行移动操作，直接更新节点即可**。四个方法挨个试一遍，都不是相同节点则采用循环查找节点。

- ##### 新后与旧后

  新后和旧后对比。如果是同一个节点，**不需要执行移动操作，直接更新节点即可**。

- ##### 新后与旧前

  新后和旧前对比。如果是同一个节点，位置不同则需要执行移动操作。把新后节点**移动到oldChildren所有未处理节点的后面**。并更新节点。

- ##### 新前与旧后

  新前和旧后对比。如果是同一个节点，位置不同则需要执行移动操作。把新前节点**移动到oldChildren所有未处理节点的前面**。并更新节点。



> **注意**：快速查找方式主要是为了优化移动和更新节点两个操作。对于创建新节点是没有用的，新节点在oldChildren中找不到，执行完快速查询没找到，还得执行一遍循环查找，才能断定oldChildren中没有该节点，得创建节点。


# [CDN](https://github.com/Twlig/issuesBlog/issues/75)

内容分发网络（Content Delivery Network，简称CDN）是建立并覆盖在承载网之上，由分布在不同区域的边缘节点服务器群组成的分布式网络。CDN应用广泛，支持多种行业、多种场景内容加速，例如：图片小文件、大文件下载、视音频点播、直播流媒体、全站加速、安全加速。

![image](https://user-images.githubusercontent.com/22440467/160281574-d18e2541-ecbe-49f3-9e79-8e51a2b1b05f.png)

（1）CDN的加速资源是跟域名绑定的。（2）通过域名访问资源，首先是通过DNS分查找离用户最近的[CDN节点](https://www.zhihu.com/search?q=CDN%E8%8A%82%E7%82%B9&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1604554133%7D)（边缘服务器）的IP（3）通过IP访问实际资源时，如果CDN上并没有缓存资源，则会到源站请求资源，并缓存到CDN节点上，这样，用户下一次访问时，该CDN节点就会有对应资源的缓存了。

参考文章：
- [CDN是什么？](https://www.zhihu.com/question/36514327/answer/1604554133)

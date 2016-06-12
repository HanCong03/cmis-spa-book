# 页面内置对象

在 ```页面主文件``` 中，为了能够访问某些“资源”，包含了以下几个 ```内置对象```：

1. $appState：当前页面的 ```应用状态```；
2. $uiState：当前页面的 ```UI 状态```；
3. $controller：当前页面定义的 ```控制器函数```；
4. $getResource(resourcePathName)： 获取当前页面包含的资源URL的函数；
5. $getAppResource(resourcePathName)：获取整个应用包含的全局资源URL的函数。


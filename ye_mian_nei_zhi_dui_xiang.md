# 页面内置对象

在 ```页面主文件``` 中，为了能够访问某些“资源”，包含了以下几个 ```内置对象```：

1. $appState：当前页面的 ```应用状态```；
2. $uiState：当前页面的 ```UI 状态```；
3. $controller：当前页面定义的 ```控制器函数```；
4. $getResource(resourcePathName)： 获取当前页面包含的资源URL的帮助函数；
5. $getAppResource(resourcePathName)：获取整个应用包含的全局资源URL的帮助函数。

## 使用方式

在 ```页面主文件``` 中我们可以通过添加 ```动态内容``` 的方式来使用 ```页面内置对象```。

以下是一个使用 ```页面内置对象``` 的示例，模拟登录界面：

```html
<div>
  <!-- 使用 $appState 来访问在 state.js 文件中定义的 today 数据项 -->
  <h1>你好，今天是 {$appState.today}</h1>

  <!-- 使用 $controller 来访问 controler.js 文件中定义的控制器函数 -->
  <form onsubmit={$controller.loginSubmit}>
    <input type="text" name="username"/>
    <br/>
    <input type="password" name="password"/>
    <br/>
    <button type="submit">登录</button>
  </form>
</div>
```
# 更新页面状态

通过前面文档的介绍，我们已经了解到了在CMIS中如何 [显示动态内容]('#显示动态内容) 和 [响应用户操作](#响应用户操作)。 在本节中，我们将了解到如何在**响应用户操作时更新页面的内容**。

## 页面上下文

在 [响应用户操作](#响应用户操作) 这一节中，我们在介绍 ```控制器函数``` 时提到了**每一个 ```控制器函数``` 的第一个参数都是 ```页面上下文对象```**。其存在的目的是为了给：正在执行的 ```控制器``` **提供当前页面信息和一些辅助函数**。

通常情况下，```页面上下文对象``` 就是包含以下几个属性的一个**只读对象**：

```javascript
{
  // 访问当前页面的“App Action”入口
  appAction: Object,
  // 访问当前页面的“UI Action”入口
  uiAction: Object,
                appState: state.app,
                uiState: state.ui,
                service: MX_COMPONENT_ASSETS.service,
                getResource,
                getAppResource
}
```

## 修改状态

下面是在 [显示动态内容](#显示动态内容) 一节中 ```页面主文件``` 的源码：

```html
<%@route path="/"%>

<h1>{$uiState.welcomeText}</h1>
```

我们在页面插入了 ```$uiState``` 中的 ```welcomeText``` 的值，如果你
# 示例：用户登录


## 场景模拟

我们假设有下面的一个场景：

要编写一个Welcome页面，如果当前用户已经登录，则显示：Welcome + 用户的名字。如果用户未登录，则显示登录对话框。为了保持简单，用户的登录过程不会发送网络请求，而是在本地直接判断。


## 编写页面

```html
<!-- 别忘了定义路由 -->
<%@route path="/"%>

<!-- 以下是页面要显示的内容 -->
<div>
  <!-- switch 标签是CMIS提供的内置页面元素，用来根据某种条件进行选择性地渲染页面视图 -->
  <Switch test={!!$appState.user}>
    <Case value={true}>
      <h1>Welcome {$appState.user.name}</h1>
    </Case>
    
    <Default>
      <div>{$appState.loginErrorMessage}</div>
      
      <form onsubmit={$controller.login} action="/login">
        <div>
          <input type="text" name="username">
        </div>
        
        <div>
          <input type="password" name="password">
        </div>
        
        <div>
          <button type="submit">登录</button>
        </div>
      </form>
    </Default>
  </Switch>
</div>
```
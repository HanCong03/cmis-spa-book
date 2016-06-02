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
      <!--
        定义登录表单，并且监听表单的提交事件
      -->
      <form onsubmit={$controller.login} action="/login">
        <!-- 登录错误提示 -->
        <div>{$appState.loginErrorMessage}</div>
        
        <div>
          <input disabled={$uiState.isSubmitting} type="text" name="username"/>
        </div>
        
        <div>
          <input disabled={$uiState.isSubmitting} type="password" name="password"/>
        </div>
        
        <div>
          <button disabled={$uiState.isSubmitting} type="submit">登录</button>
        </div>
      </form>
    </Default>
  </Switch>
</div>
```

> Switch标签是CMIS环境提供的 **内置元素**。其行为是：查找其下的 ```Case``` 元素，如果 ```Case``` 元素的 ```value``` attribute 值与 ```Switch``` 的 ```test``` attribute 值一致，则渲染该 ```Case``` 元素中的内容；如果所有 ```Case``` 都不匹配，则渲染 ```Default``` 元素的内容；如果不存在任何匹配的 ```Case```元素，且不存在 ```Default``` 元素，则什么都不渲染。

为了让用户能在登录失败时了解错误的原因，我们在表单顶部提供一个提示框。同时，为了防止用户在登录过程中修改输入的用户名和密码以及防止重复提交，我们为表单元素定义了 ```disabled``` attribute，并把表单是否禁用交由 ```$uiState``` 中的 ```isSubmitting``` 项来控制。
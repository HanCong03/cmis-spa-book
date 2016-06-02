# 示例：用户登录


## 场景模拟

我们假设有下面的一个场景：

要编写一个Welcome页面，如果当前用户已经登录，则显示：Welcome + 用户的名字。如果用户未登录，则显示登录对话框。为了保持简单，用户的登录过程不会发送网络请求，而是在本地直接判断。


## 创建页面

1. 进入项目根路径。
2. 执行初始化：```cmis init```。
3. 创建页面：```cmis createpage src/pages/index```。

## 编写页面主文件（视图）

打开文件 ```src/pages/index/index.html```，修改内容为：

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

## 定义状态（State）

打开文件```src/pages/_assets/reducer.js```，修改内容为：

```javascript
'use strict'

// 应用状态定义
export const app = (state, action) => {
  // 如果不存在状态，则对其进行初始化
  if (!state) {
    state = {
      // 初始化用户为空
      user: null,
      // 初始化登录错误为空
      loginErrorMessage: null
    };
  }

  // 列出所有变更的可能
  switch (action.type) {
    // 只关心登录完成的动作，其他动作不用关心
    case 'LOGIN_COMPLETE':
      // 登录过程发生错误
      if (aciotn.error) {
        // 从payload中提取出错误信息，注意此处由于修改了状态值，所以我们基于原有的State，生成了全新的State对象，而不是在原有的State对象上进行修改。
        state = {
          ...state,
          loginErrorMessage: action.payload.message
        };
        
      // 登录成功，记住用户信息
      } else {
        state = {
          ...state,
          user: action.payload
        };
      }
  }
  
  // 记得返回state
  return state;
};

// UI状态定义
export const ui = (state, aciton) => {
  if (!state) {
    state = {
      // 是否处于提交阶段
      isSubmitting: false
    };
  }
  
  // 处理Action
  switch (action.type) {
    // 开始登录
    case 'LOGIN':
      state = {
        isSubmitting: true
      };
      break;
      
    // 登录完成，不论失败与否，都重置提交状态
    case 'LOGIN_COMPLETE':
      state = {
        isSubmitting: false
      };
  }
  
  // 记得返回state
  return state;
};

```

## 定义行为（Action）

打开文件```src/pages/_assets/action.js```，修改内容为：

```javascript
'use strict'

// 登录动作
export funciton login() {
  return {
    type: 'LOGIN'
  };
}

// 登录成功
export function loginSuccess(uid, name) {
  return {
    type: 'LOGIN_COMPLETE'
    payload: {
      uid,
      name
    }
  };
}

// 登录失败
export function loginFail(reason) {
  return {
    type: 'LOGIN_COMPLETE',
    payload: new Error(reason),
    error: true
  };
}
```

## 编写控制器，关联用户行为

打开文件```src/pages/_assets/controller.js```，修改内容为：

```javascript
'use strict'

// 定义登录控制器
export function login($context, evt){
  // 获取表单数据
  //（为了简单，这里是通过这种方式获取表单数据的，不建议使用这种方式，在后续的高级内容中会提到更好的方式来获取表单数据）
  let {username, password} = evt.target.elements;
  
}
```
# 更新状态

通过前面文档的介绍，我们已经了解到了在CMIS中如何 [显示动态内容]('#显示动态内容) 和 [响应用户操作](#响应用户操作)。 在本节中，我们将了解到如何在**响应用户操作时更新页面的内容**。

## 页面上下文

在 [响应用户操作](#响应用户操作) 这一节中，我们在介绍 ```控制器函数``` 时提到了**每一个 ```控制器函数``` 的第一个参数都是 ```页面上下文对象```**。其存在的目的是为了给：正在执行的 ```控制器``` **提供当前页面信息和一些辅助函数**。

通常情况下，```页面上下文对象``` 就是包含以下几个属性的一个**只读对象**：

```javascript
{
  // 访问当前所定义的“Action Trigger”的入口
  action: Object,
  // 访问当前页面“App State”的入口
  appState: Object,
  // 访问当前页面“UI State”的入口
  uiState: Object,
  // 访问当前页面“服务层”的入口
  service: Object,
  // 获取当前页面资源URL的帮助函数
  getResource: Function,
  // 获取应用全局资源URL的帮助函数
  getAppResource: Function
}
```

## 修改状态

下面是在 [显示动态内容](#显示动态内容) 一节中 ```页面模板``` 的源码：

```html
<%@route path="/"%>

<h1>{$uiState.welcomeText}</h1>
```

从源码中可以非常直观地看出页面的效果就是显示一段文案，文案的具体内容则来自于当前页面中定义的```UI State``` 中的 ```welcomeText```。

既然如此，我们是否可以通过直接修改 ```$uiState.welcomeText``` 的值来改变页面中显示的内容呢？

答案是**不能**。首先，CMIS提供的 ```$uiState``` 是一个**只读**对象。其次，就算我们通过直接赋值的方式改变了 ```$uiState``` 中的某个项，也并不能引起页面的变化。

在CMIS环境下，**只有状态的“拥有者”才能改变状态**，而这个“拥有者”就是我们在 [显示动态内容](#显示动态内容) 一节中提到的```_assets/reducer.js```。该文件具有两个职责：

  1. 定义状态并初始化。
  2. 修改状态。

让我们回顾一下我们在 [显示动态内容](#显示动态内容) 中定义的 ```_assets/reducer.js``` 文件的内容：

```javascript
'use strict';

// 导出ui状态的定义函数
export const ui = (state = {
    // UI状态的初始化值
    welcomeText: 'Good morning，李雷'
}, action) => {
    // 不做任何处理，直接返回状态
    return state;
};
```

在这个文件中，我们已经定义并初始化了页面的UI状态，要为当前的状态添加“更新操作”，只需要在该 ```funciton``` 的函数体添加对应的行为即可。而不管添加多少行为，我们必须保证**函数执行完毕后必须返回一个 ```State```，如果当前的行为修改了 ```State```，我们还必须保证返回的新的 ```State``` 必须是一个全新的对象**。

修改后的**伪代码**如下：

```javascript
'use strict';

// 导出ui状态的定义函数
export const ui = (state = {
    // UI状态的初始化值
    welcomeText: 'Good morning，李雷'
}, action) => {
  
    // 在这里可以针对state做任何操作
    ...

    // 保证函数执行完毕后一定会返回一个有效的State
    return state;
};
```

## 状态改变的条件

在CMIS环境中，我们的代码无法直接获取到 ```_assets/reducer.js``` 中定义的函数，这也就导致我们**不能直接触发状态发生变化**。

为了改变状态，我们必须**发起一个 ```动作```，只有通过 ```动作``` 才能导致页面中的状态发生改变**。

```动作``` 简单来说就是一个 ```消息对象```，它携带了标识信息，用以帮助众多的 ```消费者``` 判断其类型，同时，它还可以携带具体的内容来对当前 ```动作``` 做出补充。根据产生 ```动作``` 的上下文不同，```动作``` 还可以携带一个指示**是否发生错误**的标识，如果产生了错误，则该 ```动作``` 所携带的内容是对错误本身的具体说明。

```动作``` 在技术上是一个 ```普通对象```，如下所示：

```javascript
// 动作对象
{
  // 必须的
  type: String,
  // 可选的
  payload: any,
  // 可选的
  error: Boolean
}
```

一个 ```动作``` 产生后，会被交给 ```_assets/reducer.js``` 处理，我们所要做的，就是在 ```_assets/reducer.js``` 中编写当不同类型的 ```动作``` 产生时，```页面状态``` 所应该有的变化。

在加入了对 ```动作``` 的处理之后，```_assets/reducer.js``` 文件的内容看起来就像是这样的：

```javascript
'use strict';

// 导出ui状态的定义函数
/** 
* @param state 当前页面的“状态对象”，如果还未初始化，则该参数为 undefined。
* @param action 触发当前状态变化的“动作对象”。
*/
export const ui = (state = {
    // UI状态的初始化值
    welcomeText: 'Good morning，李雷'
}, action) => {
  
    // 处理不同类型的“动作”
    switch (action.type) {
      case '动作1':
        // 对应于“动作1”时state应该是什么样的
        state = ...;
        break;
        
      case '动作2':
        // 对应于“动作2”时state应该是什么样的
        state = ....;
        break;
        
      // 更多类型的“动作”的处理
      case ...
        ...
    }

    // 最终需要返回的State
    return state;
};
```

> ```_assets/reducer.js``` 中的内容，实际上就是列举出当前页面中会导致 ```状态``` 发生变化的各种可能。需要再次提醒的是：**任何情况下，都要返回一个State**，哪怕当前的 ```action``` 并不是我们所关心的，我们也要保证这一点。

## 触发动作

当所有的工作都已经准备完毕之后，我们需要做的就是在适当的时候 ```触发动作``` 来改变 ```状态```，以达到 ```更新页面``` 的效果。

在触发 ```动作``` 之前，我们需要为每一个 ```动作``` 定义一个 ```触发器```，通过调用 ```触发器```，将会来生成某一具体的 ```动作```，然后**立即触发该 ```动作```**。

在 ```_assets/action.js``` 文件中，我们可以通过如下方式定义 ```动作触发器（Action Trigger）```：

```javascript
'use strict'

// 动作触发器 - 登录成功Action，携带了当前用户的 uid 和 name
export function loginSuccess(uid, name) {
  // 返回一个aciton对象
    return {
      // action标识，类型是String
      type: 'login',
      // action 携带的数据
      payload: {
        uid,
        name
      }
    };
}

// 动作触发器2
export function actionTrigger2() {
  ...
}
```

在定义好了 ```Action Trigger``` 后，在 ```_assets/controller.js``` 中定义的 ```控制器函数``` 中我们可以通过 ```页面上下文对象``` 调用它们，并触发对应的 ```动作```。代码类似下面这样：

```javascript
/**
* @file  _assets/controller.js
*/

export function login($context, username, password) {
  // 其他操作
  ...
  
  // 触发登录成功的动作，并传递相关数据
  $context.action.loginSuccess(123, '测试用户');
  
  // 触发其他动作
  $context.action.actionTrigger2();
}
```
---
title: redux复习
date: 2018-02-06 23:23:32
tags: [redux, 前端]
---

整体的流程: 

> dispatcher( ActionCreator --> Action) --> middleware 1 ---> middleware 2 ---> reducer --> subscribe --> 更新ui



1. createStore, 创建一个redux实例---store

    ```js
    import { createStore } from 'redux'
    var store_ = creatStore(reducer_)
    ```

    在初始化应用 store 的时候，Redux会自动dispatch一个初始化的 `action ({ type: '@@redux/INIT' })`
    
2. reducer

    会接收两个参数: state, action
    
    ```js
    import { combineReducers } from 'redux'
    var reducer_1 = function (state = {}, action) {
        switch (action.type) {
            case 'SAY_SOMETHING':
                return {
                    ...state,
                    message: action.value
                }
            default:
                return state;
        }
    }
    var reducer = combineReducers({
        reducer_1: reducer_1,
        reducer_2: reducer_2
    })
    ```

1. `store_.getState()`, 得到此时的state

2. `store_.dispatch(actionCreator)`, 分发action

3. actionCreator: 

    两种: 同步action, 异步action
    
    同步action
    
    ```js
    var syncActionCreator = function (name) {
        return {
            type: 'SET_NAME',
            name: name
        }
    }
    ```
    异步action
    
    ```js
    
    // 模拟一个异步action
    var asyncSayActionCreator_1 = function (message) {
        return function (dispatch) {
            setTimeout(function () {
                dispatch({
                    type: 'SAY',
                    message
                })
            }, 2000)
        }
    }
    // 调用的时候,用
    asyncSayActionCreator_1('Hi')(store_0.dispatch)
    
    这样是会报错的: 
    
    // 输出：
    //     ...
    //     /Users/classtar/Codes/redux-tutorial/node_modules/redux/node_modules/invariant/invariant.js:51
    //         throw error;
    //               ^
    //     Error: Invariant Violation: Actions must be plain objects. Use custom middleware for async actions.
    //     ...
    // Redux 给出了温馨提示：使用自定义中间件（middleware）来支持异步 action。
    ```

4. 中间件middleware

    现在的dispatch把action分发到各个reducer里去处理, 这个分发的过程是可以拦截的, 这就是中间件的作用, 加工,处理要分发的action 最后传给reducer
    每当一个 action（或者其他诸如异步 action creator 中的某个函数）被分发(dispatch)时，我们的中间件都会被调用
    
    `action ---> dispatcher ---> middleware 1 ---> middleware 2 ---> reducers`
    
    格式: 
    
    ```js
    var anyMiddleware = function ({ dispatch, getState }) {
        return function(next) {
            return function (action) {
                // 你的中间件业务相关代码
            }
        }
    }
    ```
    处理异步action的中间件是thunk
    
    ```js
    var thunkMiddleware = function ({ dispatch, getState }) {
        // console.log('Enter thunkMiddleware');
        return function(next) {
            // console.log('Function "next" provided:', next);
            return function (action) {
                // console.log('Handling action:', action);
                return typeof action === 'function' ?
                    action(dispatch, getState) :
                    next(action)
            }
        }
    }
    ```
    大概功能就是判断过来的action是不是一个函数(同步action是一个对象),是的话视为一个异步action, 把dispatch和getState(有可能用到state中的数据)当参数传给这个函数,然后调用; 不是的话就是一个正常的action, 不需要thunk这个中间件处理, next(action)传给下一个中间件处理就可以了
    
    现在项目中用到的比较正规的异步action写法: 
    
    ```js
    acFetchGroupInfo: function (actionParams) {
        return {
            SERVER_API: {
                types: [
                    ActionType.GROUP_INFO_REQUEST,  
                    ActionType.GROUP_INFO_SUCCESS,  
                    ActionType.GROUP_INFO_FAILURE
                ],
                url: config.apiDomain + '/group/get_one_group_info',
                param: JSON.stringify({
                  "token": actionParams.token,
                  "gid": actionParams.gid,
                }),
                method: 'POST',
                normalizeFunc: function (json) {
                  return json;
                },
                successCallback: actionParams.success,
                failCallback: actionParams.fail,
                complete: actionParams.complete
            },
            gid: actionParams.gid
        };
    },
    ```
    这种写法就需要专门的中间件去处理, 
    
    ```js
    function serverApi (store) {
      return function (next) {
        return function (action) {
          var serverAPI = action.SERVER_API;
          if (typeof serverAPI === 'undefined') {
            return next(action);
          }
          var types = serverAPI.types;
          var url = serverAPI.url;
          var param = serverAPI.param;
          var method = serverAPI.method;
          var normalizeFunc = serverAPI.normalizeFunc;
          var successCallback = serverAPI.successCallback;
          var failCallback = serverAPI.failCallback;
          var completeCallback = serverAPI.completeCallback;
          var logInfo = serverAPI.logInfo;
    
          if (typeof url !== 'string') {
            throw new Error('Specify a string url.');
          }
          if (typeof param !== 'string') {
            throw new Error('Specify a string param.');
          }
          if (!Array.isArray(types) || types.length !== 3) {
            throw new Error('Expected an array of three action types.');
          }
          if (!types.every(function (type) {
            return typeof type === 'string';
          })) {
            throw new Error('Expected action types to be strings.');
          }
          function actionWith(data) {
            var finalAction = objectAssign({}, action, data);
            delete finalAction.SERVER_API;
            return finalAction;
          }
    
          var requestType = types[0];
          var successType = types[1];
          var failureType = types[2];
    
          next(actionWith({ type: requestType }));
          // 先发request的ACTION
          
          
          
          
          // 下面在发起实际的请求
          return new Promise(function(resolve, reject) {
            callServerApi(url, param, method, normalizeFunc, logInfo || {}, completeCallback)
            .then(function (response) {
            
              //发出成功的ACTION
              next(actionWith({
                response: response,
                type: successType
              }));
              successCallback && successCallback(response.data);
              completeCallback && completeCallback(response.data);
              resolve(response.data);
            })
            .catch(function (response) {
              var msg = response.msg || response.message || '网络错误，请稍后再试';
              
              //发出失败的ACTION
              next(actionWith({
                type: failureType,
                response: response,
                msg: msg
              }));
              failCallback && failCallback(msg,response.ret);
              completeCallback && completeCallback(msg,response.ret);
              reject(msg);
            });
          });
        };
      };
    }
    
    module.exports = serverApi;

    ```
    多middleware的情况, 使用applyMiddleware, 实际是一个store增强生成器(增强createStore的功能):

    ```js
    import { createStore, combineReducers, applyMiddleware } from 'redux'
    const finalCreateStore = applyMiddleware(thunkMiddleware, xxx, ...)(createStore)
    const store_0 = finalCreateStore(reducer)

    ```
    还有一种用法, 把函数执行结果(合并后的中间件) 作为参数传给createStore

    ```js
    createStore(
      rootReducer,
      initialState,
      applyMiddleware(thunk, serverApiPRO, logger)
    );
    ```

5. subscribe(订阅)
    store改变更新ui
    只要store改变, 就会触发绑定的监听函数
    
    ```js
    store.subscribe(function() {
        console.log(store.getState());
    })
    ```


    
    
    

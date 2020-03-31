<!-- Tutorial 08 - dispatch-async-action-2.js -->
# Tutorial 08 - dispatch async action 2

<!-- Let's try to run the first async action creator that we wrote in dispatch-async-action-1.js.

import { createStore, combineReducers } from 'redux'

var reducer = combineReducers({
    speaker: function (state = {}, action) {
        console.log('speaker was called with state', state, 'and action', action)

        switch (action.type) {
            case 'SAY':
                return {
                    ...state,
                    message: action.message
                }
            default:
                return state;
        }
    }
})
var store_0 = createStore(reducer)

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

console.log("\n", 'Running our async action creator:', "\n")
store_0.dispatch(asyncSayActionCreator_1('Hi'))

// Output:
//     ...
//     /Users/classtar/Codes/redux-tutorial/node_modules/redux/node_modules/invariant/invariant.js:51
//         throw error;
//               ^
//     Error: Invariant Violation: Actions must be plain objects. Use custom middleware for async actions.
//     ... -->

먼저 dispatch-async-action-1에서 했던 비동기 action creator를 실행해보자.

```js
import { createStore, combineReducers } from 'redux'

var reducer = combineReducers({
    speaker: function (state = {}, action) {
        console.log('speaker 가 전달받은 상태', state, '와 액션', action)

        switch (action.type) {
            case 'SAY':
                return {
                    ...state,
                    message: action.message
                }
            default:
                return state;
        }
    }
})
var store_0 = createStore(reducer)

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

console.log("\n", 'Running our async action creator:', "\n")
store_0.dispatch(asyncSayActionCreator_1('Hi'))

// Output:
//     ...
//     /Users/classtar/Codes/redux-tutorial/node_modules/redux/node_modules/invariant/invariant.js:51
//         throw error;
//               ^
//     Error: Invariant Violation: Actions must be plain objects. Use custom middleware for async actions.
//     ...
```

<!-- It seems that our function didn't even reach our reducers. But Redux has been kind enough to give us a tip: "Use custom middleware for async actions.". It looks like we're on the right path but what is this "middleware" thing? -->

우리가 만든 함수는 reducer 근처에도 못갔다. 그렇지만 친절한 Redux가 알려주는 팁을 보면: "비동기 액션에는 커스텀한 미들웨어를 써라.". 이대로 하면 될 것 같은데 "미들웨어"가 뭐지?

<!-- Just to reassure you, our action creator asyncSayActionCreator_1 is well-written and will work as expected as soon as we've figured out what middleware is and how to use it. -->

음 의심을 좀 덜어주자면, 우리가 만든 action creator `asyncSayActionCreator_1` 는 잘 짜여졌고 미들웨어가 뭔지 이해하고 어떻게 쓰는지 알게되면 곧 우리가 예상했던대로 동작하게된다.

<!-- Go to next tutorial: 09_middleware.js -->
다음: [09_middleware.md](./09_middleware.md)

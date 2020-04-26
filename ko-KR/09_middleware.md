<!-- Tutorial 09 - middleware.js -->
# Tutorial 09 - middleware

<!-- We left dispatch-async-action-2.js with a new concept: "middleware". Somehow middleware should help us to solve async action handling. So what exactly is middleware? -->

우리는 이전에 새로운 개념 "미들웨어"를 과제로 남겨놨었다. 미들웨어가 어떻게 비동기 액션을 다룰 수 있게 도와줄 수 있는걸까. 그래서 대체 미들웨워가 무엇일까?

<!-- Generally speaking middleware is something that goes between parts A and B of an application to transform what A sends before passing it to B. So instead of having:
A -----\> B
we end up having
A ---\> middleware 1 ---\> middleware 2 ---\> middleware 3 --\> ... ---\> B -->

일반적으로 미들웨어는 애플리케이션의 A와 B 사이 일부에서 A가 B에게 전달하기 전에 변형시키게 하기위한 것이다.
그래서 A -----> B 대신, A ---> middleware 1 ---> middleware 2 ---> middleware 3 --> ... ---> B 이런 식으로...

<!-- How could middleware help us in the Redux context? Well it seems that the function that we are returning from our async action creator cannot be handled natively by Redux but if we had a middleware between our action creator and our reducers, we could transform this function into something that suits Redux: -->

리덕스에서 미들웨어는 우리를 어떻게 도와줄까? 음 우리가 만들었던 비동기 액션 creator는 그냥 리덕스로는 다룰 수 없었지만 미들웨어가 액션 creator와 reducer 사이에 있다면, 이 함수를 리덕스에 맞는 어떤것으로 바꿀 수 있다:

<!-- action ---\> dispatcher ---\> middleware 1 ---\> middleware 2 ---\> reducers -->

action ---> dispatcher ---> middleware 1 ---> middleware 2 ---> reducers

<!-- Our middleware will be called each time an action (or whatever else, like a function in our async action creator case) is dispatched and it should be able to help our action creator dispatch the real action when it wants to (or do nothing - this is a totally valid and
sometimes desired behavior). -->

각 액션마다 (혹은 우리가 만들었던 비동기 액션 creator 같은 경우의 함수처럼 어떤것이든) 미들웨어를 호출할 수 있고 dispatch 된다. 이것이 우리가 원할 때(아니면 아무것도 하지 않도록 하거나 - 이것은 때때로 필요한 행동임) 액션 creator가 실제 액션을 dispatch 할 수 있게 도와준다.

<!-- In Redux, middleware are functions that must conform to a very specific signature and follow a strict structure:
/*
    var anyMiddleware = function ({ dispatch, getState }) {
        return function(next) {
            return function (action) {
                // your middleware-specific code goes here
            }
        }
    }
*/ -->

리덕스에서 미들웨어는 매우 구체적인 특징과 엄격한 구조를 따라야하는 함수이다.
```js
var anyMiddleware = function ({ dispatch, getState }) {
    return function(next) {
        return function (action) {
            // 구체적인 코드는 이곳에
        }
    }
}
```

<!-- As you can see above, a middleware is made of 3 nested functions (that will get called sequentially):
1) The first level provides the dispatch function and a getState function (if your middleware or your action creator needs to read data from state) to the 2 other levels
2) The second level provides the next function that will allow you to explicitly hand over your transformed input to the next middleware or to Redux (so that Redux can finally call all reducers).
3) the third level provides the action received from the previous middleware or from your dispatch and can either trigger the next middleware (to let the action continue to flow) or process the action in any appropriate way. -->

위에서 보았다시피, 미들웨어는 3개의 중첩된 함수로 구성되어있다(연속적으로 호출될 것임):
1. 첫번째 함수는 `dispatch`와 `getState` 함수를(만약 미들웨어나 action creator 가 상태에서 데이터를 읽어야 한다면) 남은 2개의 함수에 제공한다.
2. 두번째는 `next` 함수를 제공하는데 변형된 인풋을 다음 미들웨어 혹은 리덕스에 명확하게 전달해줄 수 있게 해준다(리덕스가 모든 리듀서들을 호출할 수 있게하기 위함).
3. 세번째는 이전 미들웨어에서 혹은 dispatch에서 받은 액션을 제공하고 다음 미들웨어를 실행시킬 수 있다(액션이 계속해서 흘러가기 위함). 또는 다른 적절한 방법으로 액션을 진행시킨다.

<!-- Those of you who are trained to functional programming may have recognized above an opportunity to apply a functional pattern: currying (if you aren't, don't worry, skipping the next 10 lines won't affect your Redux understanding). Using currying, you could simplify the above function like that:
/*
    // "curry" may come from any functional programming library (lodash, ramda, etc.)
    var thunkMiddleware = curry(
        ({dispatch, getState}, next, action) => (
            // your middleware-specific code goes here
        )
    );
*/ -->

만약 함수형 프로그래밍에 대해 학습을 해본사람이라면 위에서 함수형 패턴을 적용할 수 있는 부분을 발견했을 것이다: 커링을 이용하면 간단하게 위 함수를 이런식으로 할 수 있다. 몰라도 괜찮다. 이 부분을 건너뛰어도 리덕스를 이해하는데 아무런 지장이 없다
```js
// 함수형 프로그래밍 라이브러리에서 가져온 `curry`(lodash, ramda 등등)
var thunkMiddleware = curry(
    ({dispatch, getState}, next, action) => (
        // 구체적인 코드는 이곳에
    )
);
```

<!-- The middleware we have to build for our async action creator is called a thunk middleware and its code is provided here: https://github.com/gaearon/redux-thunk.
Here is what it looks like (with function body translated to es5 for readability):

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
} -->

비동기 액션 creator 를 만들기위한 미들웨어를 thunk 미들웨어라고 부르고 코드는 [여기에서](https://github.com/gaearon/redux-thunk) 가져왔는데 이런식이다(가독성을 높이기 위해 es5문법으로 함수 본문을 수정함)

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

<!-- To tell Redux that we have one or more middlewares, we must use one of Redux's helper functions: applyMiddleware. -->

하나 또는 여러개의 미들웨어를 쓰려면 리덕스의 helper 함수를 사용해야한다: applyMiddleware.

<!-- "applyMiddleware" takes all your middlewares as parameters and returns a function to be called
with Redux createStore. When this last function is invoked, it will produce "a higher-order
store that applies middleware to a store's dispatch".
(from https://github.com/reactjs/redux/blob/master/src/applyMiddleware.js) -->

"applyMiddleware"는 미들웨어들을 파라미터로 갖고있고 createStore와 함께 호출되는 함수를 리턴한다. 마지막 함수가 실행되면, "store의 dispatch에 미들웨어가 추가된 고차 store"가 만들어진다.(참고 https://github.com/reactjs/redux/blob/master/src/applyMiddleware.js)

<!-- Here is how you would integrate a middleware into your Redux store: -->

여기 리덕스 store에 미들웨어를 통합하는 방법이 있다.

<!-- import { createStore, combineReducers, applyMiddleware } from 'redux'

const finalCreateStore = applyMiddleware(thunkMiddleware)(createStore)
// For multiple middlewares, write: applyMiddleware(middleware1, middleware2, ...)(createStore)

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
                return state
        }
    }
})

const store_0 = finalCreateStore(reducer)
// Output:
//     speaker was called with state {} and action { type: '@@redux/INIT' }
//     speaker was called with state {} and action { type: '@@redux/PROBE_UNKNOWN_ACTION_s.b.4.z.a.x.a.j.o.r' }
//     speaker was called with state {} and action { type: '@@redux/INIT' } -->

```js
import { createStore, combineReducers, applyMiddleware } from 'redux'

const finalCreateStore = applyMiddleware(thunkMiddleware)(createStore)
// 미들웨어를 여러개 쓸땐: applyMiddleware(middleware1, middleware2, ...)(createStore)

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
                return state
        }
    }
})

const store_0 = finalCreateStore(reducer)
// Output:
//     speaker 가 전달받은 상태 {} 와 액션 { type: '@@redux/INIT' }
//     speaker 가 전달받은 상태 {} 와 액션 { type: 'type: '@@redux/PROBE_UNKNOWN_ACTION_s.b.4.z.a.x.a.j.o.r' }
//     speaker 가 전달받은 상태 {} 와 액션 { type: 'type: @@redux/INIT' }

```

<!-- Now that we have our middleware-ready store instance, let's try again to dispatch our async action: -->

<!-- var asyncSayActionCreator_1 = function (message) {
    return function (dispatch) {
        setTimeout(function () {
            console.log(new Date(), 'Dispatch action now:')
            dispatch({
                type: 'SAY',
                message
            })
        }, 2000)
    }
}

console.log("\n", new Date(), 'Running our async action creator:', "\n")

store_0.dispatch(asyncSayActionCreator_1('Hi'))
// Output:
//     Mon Aug 03 2015 00:01:20 GMT+0200 (CEST) Running our async action creator:
//     Mon Aug 03 2015 00:01:22 GMT+0200 (CEST) 'Dispatch action now:'
//     speaker was called with state {} and action { type: 'SAY', message: 'Hi' }

// Our action is correctly dispatched 2 seconds after our call the async action creator!

// Just for your curiosity, here is how a middleware to log all actions that are dispatched, would
// look like:

function logMiddleware ({ dispatch, getState }) {
    return function(next) {
        return function (action) {
            console.log('logMiddleware action received:', action)
            return next(action)
        }
    }
} -->

이제 middleware로 다시 비동기액션을 dispatch 해보자:

```js
var asyncSayActionCreator_1 = function (message) {
    return function (dispatch) {
        setTimeout(function () {
            console.log(new Date(), '액션 Dispatch')
            dispatch({
                type: 'SAY',
                message
            })
        }, 2000)
    }
}

console.log("\n", new Date(), '비동기 action creator 호출:', "\n")

store_0.dispatch(asyncSayActionCreator_1('Hi'))
// Output:
//     Mon Aug 03 2015 00:01:20 GMT+0200 (CEST) 비동기 action creator 호출:
//     Mon Aug 03 2015 00:01:22 GMT+0200 (CEST) '액션 Dispatch'
//     speaker 가 전달받은 상태 {} 와 액션 { type: 'SAY', message: 'Hi' }

// 비동기 action creator 호출로 정확히 2초 뒤에 dispatch 됨!

// dispatch 된 액션을 로그로 찍어보는 방법:

function logMiddleware ({ dispatch, getState }) {
    return function(next) {
        return function (action) {
            console.log('logMiddleware가 받은 액션:', action)
            return next(action)
        }
    }
}
```

<!-- Same below for a middleware to discard all actions that are dispatched (not very useful as is but with a bit of more logic it could selectively discard a few actions while passing others to next middleware or Redux):
function discardMiddleware ({ dispatch, getState }) {
    return function(next) {
        return function (action) {
            console.log('discardMiddleware action received:', action)
        }
    }
} -->

dispatch된 액션들을 건너뛰는 방법(이대로는 쓰기보다는 로직을 좀 더 추가해서 일부 액션들만 선택해서 다음 미들웨어나 리덕스에 전달되지 않게 할 수 있다):
```js
function discardMiddleware ({ dispatch, getState }) {
    return function(next) {
        return function (action) {
            console.log('discardMiddleware가 받은 액션:', action)
        }
    }
}
```

<!-- Try to modify finalCreateStore call above by using the logMiddleware and / or the discardMiddleware
and see what happens...
For example, using:
    const finalCreateStore = applyMiddleware(discardMiddleware, thunkMiddleware)(createStore)
should make your actions never reach your thunkMiddleware and even less your reducers. -->

finalCreateStore 에서 위에 logMiddleware 나 discardMiddleware 를 호출해보면...
예를 들어, 이렇게 쓰고:
```js
const finalCreateStore = applyMiddleware(discardMiddleware, thunkMiddleware)(createStore)
```
액션이 thunkMiddleware 와 리듀서에 전달되지 않아야한다.

<!-- See http://redux.js.org/docs/introduction/Ecosystem.html#middleware, section Middleware, to see other middleware examples. -->

http://redux.js.org/docs/introduction/Ecosystem.html#middleware 여기에서 다른 미들웨어 예제들을 더 확인할 수 있다.

<!-- Let's sum up what we've learned so far:
1) We know how to write actions and action creators
2) We know how to dispatch our actions
3) We know how to handle custom actions like asynchronous actions thanks to middlewares -->

지금까지 배운 것을 종합해보면:
1. 액션과 액션 creator 쓰는 방법
2. 액션을 dispatch 하는 방법
3. 미들웨어로 비동기액션 같은 커스텀한 액션을 다루는 방법

<!-- The only missing piece to close the loop of Flux application is to be notified about state updates in order to react to them (by re-rendering our components for example).

So how do we subscribe to our Redux store updates? -->

Flux 애플리케이션의 플로우에서 남은 부분은 상태가 갱신됨을 알리는 부분인데 어떻게 리덕스 store 갱신을 구독할 수 있는걸까?

<!-- Go to next tutorial: 10_state-subscriber.js -->
다음: [10_state-subscriber.md](./10_state-subscriber.md)

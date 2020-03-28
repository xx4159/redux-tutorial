<!-- Tutorial 06 - dispatch-action.js -->
# Tutorial 06 - dispatch action

<!-- So far we've focused on building our reducer(s) and we haven't dispatched any of our own actions.
We'll keep the same reducers from our previous tutorial and handle a few actions: -->

우리는 지금까지 리듀서를 만들어 보았고 아직 액션을 dispatch 해보지 않았다.
이전 튜토리얼에서 썼던 같은 리듀서로 몇가지 액션들을 다루어보자.

<!-- var userReducer = function (state = {}, action) {
    console.log('userReducer was called with state', state, 'and action', action)

    switch (action.type) {
        case 'SET_NAME':
            return {
                ...state,
                name: action.name
            }
        default:
            return state;
    }
}
var itemsReducer = function (state = [], action) {
    console.log('itemsReducer was called with state', state, 'and action', action)

    switch (action.type) {
        case 'ADD_ITEM':
            return [
                ...state,
                action.item
            ]
        default:
            return state;
    }
}

import { createStore, combineReducers } from 'redux'

var reducer = combineReducers({
    user: userReducer,
    items: itemsReducer
})
var store_0 = createStore(reducer)


console.log("\n", '### It starts here')
console.log('store_0 state after initialization:', store_0.getState())
// Output:
// store_0 state after initialization: { user: {}, items: [] } -->

```js
var userReducer = function (state = {}, action) {
    console.log('userReducer 이 전달받은 상태', state, '와 액션', action)

    switch (action.type) {
        case 'SET_NAME':
            return {
                ...state,
                name: action.name
            }
        default:
            return state;
    }
}
var itemsReducer = function (state = [], action) {
    console.log('itemsReducer 이 전달받은 상태', state, '와 액션', action)

    switch (action.type) {
        case 'ADD_ITEM':
            return [
                ...state,
                action.item
            ]
        default:
            return state;
    }
}
```

```js
import { createStore, combineReducers } from 'redux'

var reducer = combineReducers({
    user: userReducer,
    items: itemsReducer
})
var store_0 = createStore(reducer)


console.log("\n", '### It starts here')
console.log('초기화 이후 store_0 의 상태:', store_0.getState())
// Output:
// 초기화 이후 store_0 의 상태: { user: {}, items: [] }
```

<!-- Let's dispatch our first action... Remember in 'simple-action-creator.js' we said: "To dispatch an action we need... a dispatch function." Captain obvious -->

첫번째 액션을 dispatch 해보자... [01_simple-action-creator.md](01_simple-action-creator.md) 에서 말했던대로 "action을 dispatch 하기위해서는... dispatch 함수가 필요하다."

<!-- The dispatch function we're looking for is provided by Redux and will propagate our action to all of our reducers! The dispatch function is accessible through the Redux instance property "dispatch" -->

우리가 찾고있는 dispatch 함수는 Redux가 제공하고 모든 reducer들에 액션을 보낼거다! dispatch 함수는 Redux 인스턴트 속성 "dispatch"로 접근가능하다.

<!-- To dispatch an action, simply call:

store_0.dispatch({
    type: 'AN_ACTION'
})
// Output:
// userReducer was called with state {} and action { type: 'AN_ACTION' }
// itemsReducer was called with state [] and action { type: 'AN_ACTION' } -->

액션을 dispatch 하기위해선, 간단히 호출하면된다

```js
store_0.dispatch({
    type: 'AN_ACTION'
})
// Output:
// userReducer 이 전달받은 상태 {} 와 액션 { type: 'AN_ACTION' }
// itemsReducer 이 전달받은 상태 [] 와 액션 { type: 'AN_ACTION' }
```

<!-- Each reducer is effectively called but since none of our reducers care about this action type, the state is left unchanged:

console.log('store_0 state after action AN_ACTION:', store_0.getState())
// Output: store_0 state after action AN_ACTION: { user: {}, items: [] } -->

각 리듀서가 효율적으로 호촐되었지만 이 액션 타입을 받는 리듀서는 없기때문에 상태는 변경되지 않았다.

```js
console.log('AN_ACTION 액션 이후 store_0 상태:', store_0.getState())
// Output: AN_ACTION 액션 이후 store_0 상태: { user: {}, items: [] }
```

<!-- But, wait a minute! Aren't we supposed to use an action creator to send an action? We could indeed use an actionCreator but since all it does is return an action it would not bring anything more to
this example. But for the sake of future difficulties let's do it the right way according to flux theory. And let's make this action creator send an action we actually care about: -->

그런데 잠깐! 액션을 보내기 위해 action creator를 사용한다고 했었다. actionCreator를 사용할 수 있지만 그것은 액션을 리턴하는게 전부이기 때문에 이 예제에 다른 무언가를 더 해주진못한다. 그렇지만 나중에있을 어려움을 덜기위해 flux 이론에 따른 올바른 방법으로 해보자. 그리고 이 action creator를 만들어서 실제로 액션을 보내보자.

<!-- var setNameActionCreator = function (name) {
    return {
        type: 'SET_NAME',
        name: name
    }
}

store_0.dispatch(setNameActionCreator('bob'))
// Output:
// userReducer was called with state {} and action { type: 'SET_NAME', name: 'bob' }
// itemsReducer was called with state [] and action { type: 'SET_NAME', name: 'bob' }

console.log('store_0 state after action SET_NAME:', store_0.getState())
// Output:
// store_0 state after action SET_NAME: { user: { name: 'bob' }, items: [] } -->

```js
var setNameActionCreator = function (name) {
    return {
        type: 'SET_NAME',
        name: name
    }
}

store_0.dispatch(setNameActionCreator('bob'))
// Output:
// userReducer 이 전달받은 상태 {} 와 액션 { type: 'SET_NAME', name: 'bob' }
// itemsReducer 이 전달받은 상태 [] 와 액션 { type: 'SET_NAME', name: 'bob' }

console.log('SET_NAME 액션 이후 store_0 상태:', store_0.getState())
// Output:
// SET_NAME 액션 이후 store_0 상태: { user: { name: 'bob' }, items: [] }
```

<!-- We just handled our first action and it changed the state of our application! -->

우리는 첫번째 액션을 다루어 보았고 우리 어플리케이션의 상태를 변경시켰다.

<!-- But this seems too simple and not close enough to a real use-case. For example, what if we'd like do some async work in our action creator before dispatching the action? We'll talk about that in the next tutorial "dispatch-async-action.js" -->

그치만 이건 너무 간단하고 실제 사용예제로 충분하지 않다. 예를 들어, 액션을 dispatch 하기 전에 action creator 에서 비동기 작업을 한다면? 이것을 다음 튜토리얼에서 얘기해 볼 것이다.

<!-- // So far here is the flow of our application
// ActionCreator -> Action -> dispatcher -> reducer -->

여기까지가 우리 어플리케이션의 흐름이다.
ActionCreator -> Action -> dispatcher -> reducer

<!-- // Go to next tutorial: 07_dispatch-async-action-1.js -->
다음: [07_dispatch-async-action-1.md](./07_dispatch-async-action-1.md)

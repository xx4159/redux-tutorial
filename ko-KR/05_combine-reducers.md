<!-- Tutorial 05 - combine-reducers.js -->
# Tutorial 05 - combine reducers

<!-- We're now starting to get a grasp of what a reducer is...
var reducer_0 = function (state = {}, action) {
    console.log('reducer_0 was called with state', state, 'and action', action)

    switch (action.type) {
        case 'SAY_SOMETHING':
            return {
                ...state,
                message: action.value
            }
        default:
            return state;
    }
} -->

reducer 가 뭔지 이해하기 시작했다...

```js
var reducer_0 = function (state = {}, action) {
    console.log('reducer_0 이 전달받은 상태', state, '와 액션', action)

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
```

<!-- ... but before going further, we should start wondering what our reducer will look like when we'll have tens of actions:

var reducer_1 = function (state = {}, action) {
    console.log('reducer_1 was called with state', state, 'and action', action)

    switch (action.type) {
        case 'SAY_SOMETHING':
            return {
                ...state,
                message: action.value
            }
        case 'DO_SOMETHING':
            // ...
        case 'LEARN_SOMETHING':
            // ...
        case 'HEAR_SOMETHING':
            // ...
        case 'GO_SOMEWHERE':
            // ...
        // etc.
        default:
            return state;
    }
} -->


... 그런데 더 나아가기전에, 만약 엄청 많은 액션들이 있다면 reducer들이 어떻게 받을 지 고민해봐야한다:

```js
var reducer_1 = function (state = {}, action) {
    console.log('reducer_0 이 전달받은 상태', state, '와 액션', action)

    switch (action.type) {
        case 'SAY_SOMETHING':
            return {
                ...state,
                message: action.value
            }
        case 'DO_SOMETHING':
            // ...
        case 'LEARN_SOMETHING':
            // ...
        case 'HEAR_SOMETHING':
            // ...
        case 'GO_SOMEWHERE':
            // ...
        // etc.
        default:
            return state;
    }
}
```

<!-- It becomes quite evident that a single reducer function cannot hold all our application's actions handling (well it could hold it, but it wouldn't be very maintainable...). -->

이렇게는 단 하나의 reducer 함수가 애플리케이션의 모든 액션들을 다룰 수 없다 (할 수는 있겠지만, 이대로 유지할 순 없다...).

<!-- Luckily for us, Redux doesn't care if we have one reducer or a dozen and it will even help us to combine them if we have many! -->

다행히 Redux는 reducer 가 하나인지 여러개인지 신경쓰지않고, 많을 땐 그것들을 합칠 수 있게 도와준다.

<!-- Let's declare 2 reducers

var userReducer = function (state = {}, action) {
    console.log('userReducer was called with state', state, 'and action', action)

    switch (action.type) {
        // etc.
        default:
            return state;
    }
}
var itemsReducer = function (state = [], action) {
    console.log('itemsReducer was called with state', state, 'and action', action)

    switch (action.type) {
        // etc.
        default:
            return state;
    }
} -->

reducer 2개를 선언해보자.
```js
var userReducer = function (state = {}, action) {
    console.log('userReducer 이 전달받은 상태', state, '와 액션', action)

    switch (action.type) {
        // etc.
        default:
            return state;
    }
}
var itemsReducer = function (state = [], action) {
    console.log('itemsReducer 이 전달받은 상태', state, '와 액션', action)

    switch (action.type) {
        // etc.
        default:
            return state;
    }
}
```

<!-- I'd like you to pay special attention to the initial state that was actually given to each reducer: userReducer got an initial state in the form of a literal object ({}) while itemsReducer got an initial state in the form of an array ([]). This is just to make clear that a reducer can actually handle any type of data structure. It's really up to you to decide which data structure suits your needs (an object literal, an array,
a boolean, a string, an immutable structure, ...). -->

각 리듀서에 주입된 초기 상태값을 잘 보면: `userReducer`는 객체리터럴(`{}`)의 형태인 반면 `itemsReducer` 는 초기 상태값으로 배열(`[]`) 을 받았다. 이것은 단순히 리듀서는 어떤 타입의 데이터 구조도 받을 수 있다는 것을 보여주기 위함이다. 어떤 데이터 구조를 선택할 지는 그때 그때 다르다 (객체, 배열, boolean, string, immutable structre, ...).

<!-- With this new multiple reducer approach, we will end up having each reducer handle only a slice of our application state. -->

이런식으로 여러개의 redcuer 접근 방식으로 각 reducer가 애플리케이션 상태의 일부만을 다루게된다.

<!-- But as we already know, createStore expects just one reducer function. -->

하지만 알다시피, `createStore`는 오직 하나의 reducer 함수를 받는다.

<!-- So how do we combine our reducers? And how do we tell Redux that each reducer will only handle a slice of our state?
It's fairly simple. We use Redux combineReducers helper function. combineReducers takes a hash and
returns a function that, when invoked, will call all our reducers, retrieve the new slice of state and
reunite them in a state object (a simple hash {}) that Redux is holding.
Long story short, here is how you create a Redux instance with multiple reducers: -->

어떻게 reducer 들을 합칠 수 있을까? 그리고 어떻게하면 Redux가 각 reducer들을 상태의 일부만 다루게 할 수 있을까?
간단하다. Redux의 `combineReducers` 함수를 사용한다. `combineReducers`는 해시를 가지고 함수를 리턴하고, 실행할 때 모든 reducer들을 호출하고, 새로운 상태 조각들을 Redux가 갖고있는 하나의 상태 객체(단순한 해시 {})로 조합한다.
얘기가 길었는데 이런식으로 reducer 가 여러개일 때 Redux 인스턴스를 만든다:

<!-- import { createStore, combineReducers } from 'redux'

var reducer = combineReducers({
    user: userReducer,
    items: itemsReducer
})
// Output:
// userReducer was called with state {} and action { type: '@@redux/INIT' }
// userReducer was called with state {} and action { type: '@@redux/PROBE_UNKNOWN_ACTION_9.r.k.r.i.c.n.m.i' }
// itemsReducer was called with state [] and action { type: '@@redux/INIT' }
// itemsReducer was called with state [] and action { type: '@@redux/PROBE_UNKNOWN_ACTION_4.f.i.z.l.3.7.s.y.v.i' }
var store_0 = createStore(reducer)
// Output:
// userReducer was called with state {} and action { type: '@@redux/INIT' }
// itemsReducer was called with state [] and action { type: '@@redux/INIT' } -->

```js
import { createStore, combineReducers } from 'redux'

var reducer = combineReducers({
    user: userReducer,
    items: itemsReducer
})
// Output:
// userReducer 이 전달받은 상태 {} 와 액션 { type: '@@redux/INIT' }
// userReducer 이 전달받은 상태 {} 와 액션 { type: '@@redux/PROBE_UNKNOWN_ACTION_9.r.k.r.i.c.n.m.i' }
// itemsReducer 이 전달받은 상태 [] 와 액션 { type: '@@redux/INIT' }
// itemsReducer 이 전달받은 상태 [] 와 액션 { type: '@@redux/PROBE_UNKNOWN_ACTION_4.f.i.z.l.3.7.s.y.v.i' }
var store_0 = createStore(reducer)
// Output:
// userReducer 이 전달받은 상태 {} 와 액션 { type: '@@redux/INIT' }
// itemsReducer 이 전달받은 상태 [] 와 액션 { type: '@@redux/INIT' }
```

<!-- As you can see in the output, each reducer is correctly called with the init action @@redux/INIT.
But what is this other action? This is a sanity check implemented in combineReducers to assure that a reducer will always return a state != 'undefined'.
Please note also that the first invocation of init actions in combineReducers share the same purpose as random actions (to do a sanity check). 

console.log('store_0 state after initialization:', store_0.getState())
// Output:
// store_0 state after initialization: { user: {}, items: [] }-->

output을 보면 알겠지만, 각 reducer는 초기 액션 `@@redux/INIT`을 정확하게 호출했다.
그런데 다른 액션은 뭐지? `combineReducers`에서 실행된 sanity check 이다. 이것은 reducer가 상태를 리턴할 때 `undefined`가 아니라는 것을 확인한다.
`combineReducers`에서 init action의 첫번째 호출 또한 같은 목적을 공유한다. (sanity check 를 하는 것)

```js
console.log('초기화 이후 store_0 의 상태:', store_0.getState())
// Output:
// 초기화 이후 store_0 의 상태: { user: {}, items: [] }
```

<!-- It's interesting to note that Redux handles our slices of state correctly, the final state is indeed a simple hash made of the userReducer's slice and the itemsReducer's slice:
{
    user: {}, // {} is the slice returned by our userReducer
    items: [] // [] is the slice returned by our itemsReducer
} -->

Redux가 각 상태들을 완벽하게 처리하고 있다. 
마지막 상태는 실제로 `userReducer`와 `itemsReduces`로 만들어진 단순한 해시다.
```js
{
    user: {}, // {} userReducer 에서 리턴된 조각
    items: [] // [] itemsReducer 에서 리턴된 조각
}
```

<!-- Since we initialized the state of each of our reducers with a specific value ({} for userReducer and [] for itemsReducer) it's no coincidence that those values are found in the final Redux state. -->

reducer들의 상태를 특정 값(`userReducer`는 `{}`, `itemsReducer`는 `[]`)으로 초기화했기때문에 마지막 상태에서 이 값이 나온 것은 당연하다.

<!-- By now we have a good idea of how reducers will work. It would be nice to have some actions being dispatched and see the impact on our Redux state. -->

이제 reducer 가 어떻게 동작하는지 알게되었으니까 액션을 dispatch했을 때 Redux 상태에 영향을 주는 것을 봐보자.

<!-- // Go to next tutorial: 06_dispatch-action.js -->
다음: [06_dispatch-action.md](./06_dispatch-action.md)

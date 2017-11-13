---
title: "Redux-esque Store in Swift"
---

If you're a web developer transitioning to iOS or macOS development, you may be familiar with [Redux](https://redux.js.org) and interested in adopting the same pattern of state management in your Swift app. Luckily you're not the first; look no further than [ReSwift](https://github.com/ReSwift/ReSwift) for your unidirectional data flow needs. It's also easy to whip together your own system if you're hip and already use a framework like [RxSwift](https://github.com/ReactiveX/RxSwift) that provides observable streams. Let me show you an approach using the latter and 25 lines of code.

*This article assumes familiarity with both Redux and reactive programming and doesn't explain the mountain of jargon they employ. For an introduction to Redux you can do no better than leafing through its [official documentation](https://redux.js.org/docs/introduction/). Reactive programming takes more effort to grok. Fortunately there are a bazillion articles on the subject; this [intro to RxSwift](https://medium.com/ios-os-x-development/learn-and-master-%EF%B8%8F-the-basics-of-rxswift-in-10-minutes-818ea6e0a05b) is a fine start.*

We'll start with the store.

```swift
class Store<StateType> {
    var state: StateType

    init(state: StateType) {
        self.state = state
    }
}
```

`state` can be any type we like. A basic structure will do.

```swift
struct State {
    let name: String
    let email: String
}
```

We need a way to change the state, with a reducer being the pattern of choice. A reducer is a pure function that takes the current state and an action and returns modified state.

```swift
typealias Reducer<StateType, ActionType> = (_ state: StateType, _ action: ActionType) -> StateType
```

`action` can also be any type. Enumerations work particularly well,
though there's no reason an action can't be a structure or a marker protocol instead.

```swift
enum Action {
    case signIn(name: String, email: String)
    case signOut
}
```

Here's a concrete reducer that returns a new state structure when the user signs in or out. Once our state grows beyond a few properties we'll want to break it into multiple sub-reducers, but we'll keep it simple here for the sake of demonstration.

```swift
func reducer(_ state: State, _ action: Action) -> State {
    switch action {
    case .signIn(let name, let email)
        return State(name: name, email: email)

    case .signOut:
        return State(name: "", email: "")
    }
}
```

Back in our `Store` class we need a method to dispatch an action. It uses the
reducer passed to the class' initializer to update the state.

```swift
class Store<StateType, ActionType> {
    let reducer: Reducer<StateType, ActionType>
    var state: StateType

    init(reducer: @escaping Reducer<StateType, ActionType>, state: StateType) {
        self.reducer = reducer
        self.state = state
    }

    func dispatch(_ action: ActionType) {
        state = reducer(state, action)
    }
}
```

Now we have all the pieces to construct our store instance.

```swift
let initialState = State(name: "", email: "")
let store = Store(reducer: reducer, state: initialState)
```

To trigger a state change, for example when the user signs out of their account, we send the appropriate action to the `dispatch(_:)` method.

```swift
store.dispatch(.signOut)
```

We're halfway there. What we're missing is a mechanism to notify interested parties when the state changes. This is where RxSwift and its `BehaviorSubject` (or an alternative, e.g. [ReactiveSwift](https://github.com/ReactiveCocoa/ReactiveSwift)) comes in handy.

```swift
import RxSwift

class Store<StateType, ActionType> {
    let reducer: Reducer<StateType, ActionType>
    let subject: BehaviorSubject<StateType>
    var state: StateType

    init(reducer: @escaping Reducer<StateType, ActionType>, state: StateType) {
        self.reducer = reducer
        self.state = state
        self.subject = BehaviorSubject(value: state)
    }

    func dispatch(_ action: ActionType) {
        state = reducer(state, action)
        subject.onNext(state)
    }

    func observe(_ keyPath: KeyPath<StateType, T>) -> Observable<T> {
        return subject.map { $0[keyPath: keyPath] }
    }
}
```

The new `observe(_:)` method accepts a key path and notifies subscribers when that
property of our state changes.  After the reducer produces a new state structure, `subject.onNext(state)` broadcasts the update. In our app's view controllers we listen to changes we care about.

```swift
store.observe(\.name).subscribe(onNext: { name in
    // Update UI
})
```

Any time we need to access the state directly we're free to do so.

```swift
print(store.state.name)
```

## Refinements

One annoyance with our current store implementation is that subscribers receive an event every time *any* state property changes, not just the one `keyPath` specifies. This is where RxSwift shines. If all properties of our state structure conform to `Equatable`, we can use the `distinctUntilChanged` operator to ensure that we only notify subscribers when the observed property changes.

```swift
func observe<T: Equatable>(_ keyPath: KeyPath<StateType, T>) -> Observable<T> {
    return subject
        .map { $0[keyPath: keyPath] }
        .distinctUntilChanged()
}
```

Lovely. RxSwift also provides a plethora of other operators useful for transforming the stream. We can map, filter, throttle, merge multiple observables together, all sorts of voodoo. This is where a store powered by observable streams as opposed to a framework like ReSwift is invaluable.

```swift
store.observe(\.name)
    .filter { !$0.isEmpty }
    .map { "Welcome, \($0)" }
    .subscribe(onNext: { self.welcomeLabel.stringValue = $0 })
```

Another helpful thing our store allows us to do is observe computed properties in the same way that we observe stored ones.

```swift
struct State {
    let name: String
    let email: String

    var isSignedIn: Bool {
        return !name.isEmpty && !email.isEmpty
    }
}

store.observe(\.isSignedIn).subscribe(onNext: { isSignedIn in
    // Update UI
})
```

Whenever we dispatch an action the computed property is recalculated, but `distinctUntilChanged` prevents the store from notifying subscribers if the result fails to change.

## Wrapping Up

This architecture works well in practice. Unidirectional data flow makes debugging a joy (or as close to joy as debugging gets) and reactive programming is a powerful paradigm to have in your toolbox. Use both in concert and you're unstoppable.

The code above is a sketch --- I omitted features you may want in larger projects, e.g. middleware --- but I hope it provides an idea of how you can manage your app's state and propagate changes to observers. For a more fleshed out `Store` class that handles array properties plus a more realistic reducer composed of sub-reducers, see [this gist](https://gist.github.com/mminer/410e9c57918cee0b191511ed3d5e8343). And don't forget to floss.

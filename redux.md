# Redux
- Core concepts of Redux:
	- App state is described by a single plain old Javascript object(POJO).
	- Dispatch an action (also a POJO) to modify state
	- Reducer is a pure function that takes in current state and action to produce a new state.
- The concepts enable apps to:
	- have their state rendered on the server, booted up on the client.
	- trace, log and backtrack changes in the whole app
	- Implement undo/redo functionality easily
- React and Redux have a lot of ideas and traits in common:
	- Functional composition paradigm: React composes views while Redux composes pure reducers. Output is predictable given the same set of input.
	...
- stores:
	- plain **JS object** that represent the current state of app
	- **reducer** to describe how incoming action modify your state, should not have any side-effects, that is, if you call a reducer with the same state and the same action, you should always get the same result. It's also good practice to encapsulate as much of your application logic as possible in reducers, because, since your reducers don't rely on side-effects or global state, they're really easy to test, debug, and refactor.
- stores has 3 common functions:
	- getState: get current state of store
	- dispatch: fire off a new action
	- subscribe: fire a callback everytime there's a new action after a reducer's action
- react-redux bindings
- connect(mapStateToProps, mapDispatchToProps)(\<Component\>)
	- mapStateToProps: map current state of store to props of component
	- mapDispatchToProps: map dispatch() of store to props of component
	

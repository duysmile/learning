# Redux
- stores:
	- plain **JS object** that represent the current state of app
	- **reducer** to describe how incoming action modify your state, should not have any side-effects, that is, if you call a reducer with the same state and the same action, you should always get the same result. It's also good practice to encapsulate as much of your application logic as possible in reducers, because, since your reducers don't rely on side-effects or global state, they're really easy to test, debug, and refactor.
- stores has 3 common functions:
	- getState: get current state of store
	- dispatch: fire off a new action
	- subscribe: fire a callback everytime there's a new action after a reducer's action
- react-redux bindings

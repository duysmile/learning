# Redux
- stores:
	- plain **JS object** that represent the current state of app
	- **reducer** to describe how incoming action modify your state, should not have any side-effects, that is, if you call a reducer with the same state and the same action, you should always get the same result.

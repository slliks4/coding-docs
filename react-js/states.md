# States handler you might need and why

## useState
For easy, ui update. Note it causes rerender on every change.

## useRef
If you don't want to show an update to your ui, or your update doesn't need to rerender the component then we use ref

### Usage
``` ini
const internal_count = useRef(0)

internal_count.current
```

## useQueryState
This stores state in the url making it persists after refresh and easy for use to share states by just sharing their url
### Usage
``` ini
const [filter, setFilter] = useQueryState("filter",{
    defaultValue: ""
})
```

## useReducer
useReducer is just a more structured version of useState, it allows you to handle complex logic and especially when the states are not fully independent.

### Setup
``` ini
const initialState = {
  isAuthenticated: false,
  role: null,
  username: '',
  userPresets: {},
};

const reducer = (state, action) => {
  switch (action.type) {
    case 'LOGIN':
      return { ...state, isAuthenticated: true, ...action.payload };
    case 'LOGOUT':
      return { ...state, isAuthenticated: false, role: null, username: '', userPresets:{} };
    case 'UPDATE_PRESETS':
      return { ...state, userPresets: { ...state.userPresets, ...action.payload } };
    default:
      return state;
  }
};
```

### Usage
``` ini
dispatch({type:'LOGING', Payload:{...data}})
```

## useImmer
The draft provided by Immer is a special type of object, called a Proxy, that “records” what you do with it. This is why you can mutate it freely as much as you like! Under the hood, Immer figures out which parts of the draft have been changed, and produces a completely new object that contains your edits.

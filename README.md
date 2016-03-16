# fluxury-redux

[![Circle CI](https://circleci.com/gh/FunctionFoundry/fluxury-redux/tree/master.svg?style=svg)](https://circleci.com/gh/FunctionFoundry/fluxury-redux/tree/master)

## Quick Start

```sh
npm install --save fluxury-redux
```

```js
import { createReducer, createMasterStore } from 'fluxury-redux'
```

## Summary

Compose Fluxury stores into a Redux reducer. This integrates the Facebook dispatcher with the single store concept in Redux.

This lets you use the Facebook style "waitFor" to enforce the order to execute the store handlers. The final result packaged into an array with an item for the state of each store.

Redux has done a lot to promote function programming and this library will enable you to use Fluxury in combination with the rich ecosystem developing around Redux.

Optionally, this library includes a `createMasterStore` which generates a Fluxury store which combines
the results of multiple Fluxury stores into a single store.

## Example

```js
var createStore = require('fluxury').createStore
var createReduxStore = require('redux').createStore
var createReducer = require('fluxury-redux').createReducer

var MessageStore = createStore(
  'Messages Store',
  [], // initial state is empty array
  {
    // type is 'receiveMessage' and data is the message.
    receiveMessage: (state, data) => state.concat(data)
  }
)

var MessageCountStore = createStore(
  'Message Count Store',
  function(state=0, action, waitFor) {
    // ensure that MessageStore reducer is executed first
    waitFor([MessageStore.dispatchToken])
    switch(action.type) {
      case 'loadMessage':
      return state+1
    }
    return state
  }
)

var store = createReduxStore( createReducer(MessageStore, MessageCountStore) )
```

## Create Master store

Combine multiple stores into a single store.

```js
import { createMasterStore } from 'fluxury-redux'
import MessageStore from './MessageStore'
import MessageCountStore from './MessageCountStore'

var store = createMasterStore({
  messages: MessageStore,
  count: MessageCountStore
})
```

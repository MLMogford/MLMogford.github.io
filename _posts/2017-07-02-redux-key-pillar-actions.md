---
layout: post
title: "Redux pillars to keep in mind - Actions"
excerpt: "Things to keep in mind while developing redux application"
tags: [redux, foundation, keystone, cornerstone, pillar]
comments: true
---

## Actions Creators

Instead of creating **action objects** inline in the places where you dispatch the actions, you would create **functions generating** them.

```
// bad practice
dispatch({
  type: 'ADD_TODO',
  text: 'Use Redux'
})
```

```
import { addTodo } from './actionCreators'

// somewhere in an event handler
dispatch(addTodo('Use Redux'))
```

`addTodo` action creator behavior is invisible to the calling code. 
No need to worry about looking at each place where `todos` are being added, to make sure they have this check. 

> **Punchline:** Action creators let you decouple additional logic around dispatching an action, from the actual components emitting those actions.

Action creators generation could be automatized, but there is no clear need. So before doing it, a good reason need to be presented.
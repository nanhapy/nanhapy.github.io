---
layout: post
title:  "Redux Inroduction"
date:   2016-08-23 11:25:49 +0800
categories: programming javascript react
---

# Motivation #
For sinle-page-applications:

**States become more and more,** which can include:
+   serer responses
+   cached data
+   locally created data has not yet been persisted to the server

UI states:
+   active route
+   selected tab
+   pagination controls

**States are ever-changing, and lost control**

**New requiements** are expected:
+   handle optimistic updates
+   server-side rendering
+   fetching data before performing route transitions 

At last,there are two concepts: **Mutation and Asynchronicity**.To fix them, React remove both asynchrony and direct DOM manipulation in view layer.
And Redux attempts to make state mutations predictable by controlling how and when updates happen.These restrictions are reflected in the three principles of Redux.

# Three principles #

## Single source of truth

### The state of your whole application is stored in an object tree with a single store
Single : easy serialized, easy debug or introspect, _persist app's state in development?

```javascript
console.log(store.getState())

/* Prints
{
visibilityFilter: 'SHOW_ALL',
todos: [
    {
    text: 'Consider using Redux',
    completed: true,
    },
    {
    text: 'Keep all state in a single tree',
    completed: false
    }
]
}
*/
```

## State is read-only ##
### The only way to change the state is to emit an action, **an object describing what happened.** ###
no one can change state directly. Instead, they express an intent to transform the state
## Changes are made with pure functions 
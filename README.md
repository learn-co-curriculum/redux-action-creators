# Review and Dive into Actions

In this lesson, we will discuss:
* The properties of an action
* How to use action creators to create an action.

## Introduction

Actions are just **Plain Old JavaScript Objects** (POJOs), but that doesn't mean
we should ignore them. In this section, we'll discuss the properties of actions,
and how to use functions to create them.

## Purpose of Actions

So as you know, we've been dispatching actions to our store to indicate what
changes to make to our state. In this way, actions almost feel like the request 
object or parameters hash used in Ruby on Rails. 

In __Rails__, a user clicking on a link kicks off a request, and that request is
ultimately passed to the controller, which is responsible for changing the
database. In __Redux__, a user may click on a button which dispatches an action,
and the reducer would take information from that action to change the state. You
saw in the last section that simply by placing a __console.log__ in our reducer,
we could see a history of every action that was passed to the reducer, making
our debugging job easier.

## Structuring Actions

To review, let's recall that an action is simply a POJO that has a property of 
type. When the action is dispatched, the reducer uses the type property to determine 
how it should update state. Here is an example of a valid action:

```javascript
const increaseCount = { type: 'INCREASE_COUNT' }
```

Recall also that the store has a `dispatch` method which we can use to dispatch the 
action so it will be handled by the reducer. In the past few lessons, we have been
dispatching actions by accessing the `dispatch` method from props: 
`this.props.dispatch`. We can do this because the `dispatch` method is automatically
passed from the store into props by the `connect` method we get from React Redux. As 
we have discussed, the advantage of accessing the `dispatch` method through props is 
that it keeps the React part of our app separate from the Redux part, leading to better 
separation of concerns. While there is nothing to stop us from accessing the `dispatch` method directly from the store by calling `store.dispatch` and passing in our action,
the preferred approach is to access `dispatch` through props:

```javascript
this.props.dispatch(increaseCount)
```

Or equivalently:

```javascript
this.props.dispatch({ type: 'INCREASE_COUNT' })
```

The dispatch method passes the action to the reducer, and the reducer's switch
statement then executes the appropriate code:

```javascript

function dispatch(action) {
  reducer(state, action)
}

function reducer(state = {
  count: 0,
}, action) {
  switch (action.type) {

    case 'INCREASE_COUNT':
      return { count: state.count + 1 };

    default:
      return state;

  }
}
```

## Action Creators

In the examples above, we saw that we can dispatch our action object by directly 
passing it as an argument (`this.props.dispatch({ type: 'INCREASE_COUNT' })`) or 
by storing it in a variable and then passing that as the argument 
(`this.props.dispatch(increaseCount)`). But there is yet another way we can do 
this:

```javascript

function increaseCount() {
  return { type: 'INCREASE_COUNT' };
}

this.props.dispatch(increaseCount());
```

Ok, so in the above lines of code we define a function called`increaseCount()` whose 
job it is to return an action. We then execute the `increaseCount()` function and 
dispatch the action returned by the function to the store.   

But why would we want to use a function rather than just passing the object itself 
to `dispatch`? Well, in many cases our actions will include a payload as well as a 
type. For example, we can imagine an action object to add an item to a todo list:

```javascript
{ 
  type: 'ADD_TODO',
  todo: 'buy groceries'
}
```

In this example, the type will be the same for each action we create to add a todo,
but the payload can vary:

```javascript
{ 
  type: 'ADD_TODO',
  todo: 'watch netflix'
}
```

If we instead use a function to create our action, it will look something like this:

```javascript
function addTodo(todo) {
  return {
    type: 'ADD_TODO',
    todo: todo
  }
}
```

With this in place, creating an action is simply a matter of calling the `addTodo` 
action creator and passing the todo (the payload) as an argument:

```javascript
addTodo('buy groceries');
// -> { type: 'ADD_TODO', todo: 'buy groceries' }

addTodo('watch netflix');
// -> { type: 'ADD_TODO', todo: 'watch netflix' }
```

In this way, we are able to easily keep some of the action properties the same, 
like type, while changing others, like todo -- the action creator does the work for
us.  We would dispatch the action in the following way:

```javascript
this.props.dispatch(addTodo('buy groceries'));
```

Here we are calling the `dispatch` method, passing in the result of calling our action creator as an argument. The `dispatch` function dispatches the action to the reducer 
which then uses the action to update the state.
---
title: "React Native Basics"
date: 2023-08-08T19:53:29+02:00
summary: Primitive elements, Stack Navigator basics, Reducer and useReducer.
draft: false
tags: [react native]
---

## Primitive Elements

### FlatList

FlatList is a React Native component that we can use to make lists. This component improve performance, therefore is nice to make it your default choice.

This component has a few interesting props we might want to keep in mind:
* `data`: an array of items to render
* `renderItem`: takes an item from data and renders it into the list. It provides an additional index, so make sure you extract it from the argument like so:
```
  renderItem={({item, idex}) => {...}}
```
* `keyExtractor`: this prop extract a key from each item. For example:
```
  keyExtractor={ friend => { friend.name } }
```

### Button

A simple button element. It is used as a self closing tag. Instead of passing the text as children, we use the property `title`. Instead of an `onClick` property, we use the `onPress` prop to run something when the button is pressed.

### TouchableOpacity

Highly customizable component that can detect a press on just about any kind of element. This component is more practical than `Button` because it's not styled and it takes children.

### TextInput

The `TextInput` component comes with a couple of common issues we need to be aware of:
* I has no style whatsoever, so every time we use it, we're gonna have to provide some kind of style.
* iOS and some versions of Android either capitalize or autocorrect words as they are being typed. To prevent this we can use a couple of properties: `autoCapitalize="none"` and `autoCorrect={false}`.

Another thing to keep in mind is that instead of the `password` tag we have to use `secureTextEntry={true}`.

## Stack Navigator

It's an object that allows us to manipulate navigation. It renders components with navigation props. Inside this navigation props, we have the `navigate` property, which is used to change the content that is visible in the screen of our device. For example, if I want to navigate to a profile page, I can do:
```
const HomeScreen = ({ navigation }) => {
  return (
    <View>
      <TouchableOpacity onPress={() => navigation.navigate('Profile')}>
        <Text>Profile</Text>
      </TouchableOpacity>
    </View>
  )
}
```
Notice that once this navigation is added and we go to the profile screen, a back button is going to be automagically added to the header.

## Reducer

A reducer is a function that manages changes to an object. It gets called with two object as arguments. The first argument is the one containing our state. The second argument the update we want to make to our state. When using a reducer, we must always follow these two rules:
* We never change Argument#1 directly.
* We always return a value to be used as Argument#1

## useReducer

The `useReducer` hook allows us to manage the state using a reducer function. It takes three arguments, but we're just gonna look at the first two: `reducer` and `initialArg`.

`reducer` must be a pure function. It should take the state and action as arguments, and should return the next state.
`initialArg` is the initial state.

`useReducer` returns an array with two values: the current state and the `dispatch` function, which lets you update the state to a different value and trigger a re-render.

### Reducer and useReducer example

```
const reducer = (state, action) => {
  // 2. We can handle several action types. We could have another one to change the name for example.
  switch(action.type) {
    case 'incremented_age':
      return {...state, age: state.age + 1} // 3. We never modify the state directly
    default:
      return state // 4. Remember the second reducer rule. We always return the state
  }
}

export default function Counter() {
  // 1. It takes the reducer function that we're going to declare above and the initial state as arguments:
  const [state, dispatch] = useReducer(reducer, { name: 'Fran', age: 42 });

  return (
    <>
      <button onClick={() => {
        // 5. We pass an action as the only argument to the dispatch function. This is how we call our reducer.
        dispatch({ type: 'incremented_age' })
      }}>
        Increment age
      </button>
      // 5. We use the state provided by the hook. We could also destructure this
      <p>Hello! You are {state.age}.</p>
    </>
  );
}```

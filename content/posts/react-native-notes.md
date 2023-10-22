---
title: "React Native - Notes"
date: 2023-08-22T09:00:00+02:00
summary:
draft: false
tags: [react native]
---

## The flow of creating an app

### Navigation
When creating an app, it's always better to start with the navigation flow and decide forehand which kinds of navigators you are going to use. Start drawing a diagram of the navigation flow. Then create all the screen components and apply the navigation logic to them. This will make it easier because you don't have to think about navigation anymore.

#### Empty Screen While Resolving Auth
Usually you would check for the auth token inside a useEffect of the first screen that shows up (like the signup screen for example), and if the token is there then you redirect the user to the home page (or wherever). This is reasonable, but then if the user is already logged in, she'll se the signup screen for like a split second, and we don't want that.

In order to avoid this we can create another screen that would be rendered at the very top of our navigation object. This screen just returns `null`. We'll use it to call the "check auth token" function without showing anything. This is how it looks like:
```
const ResolveAuthScreen = () => {
  const { tryLocalSignin } = useContext(AuthContext)

  useEffect(() => {
    tryLocalSignin()
  }, [])

  return null
}

export default ResolveAuthScreen
```

### React Native Elements
Don't let styling get in the way when you're trying to make your app work. Use the `react-native-elements` library and get the elements from there. Tey have almost the same names as `react-native` components, so it's pretty intuitive. https://reactnativeelements.com/docs/

### Setup Authentication
Write a signup function that:
* makes a request to sign up with email and password
* if we're signed in, modify our state and say the we're authenticated
* if signing up fails, send an error message

Write a login function that:
* tries to sign in
* handle success by changing the state
* handle error by showing a message

And write a function to logout.

## Misc.

* Use the `<SafeAreaView forceInset={{ top: 'always' }} >` component to avoid rendering your content behind the very top of the screen.
* Every time we reference `props`, `context` or `state` in a useEffect, we should add those references in the dependency value array.

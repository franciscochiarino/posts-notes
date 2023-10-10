---
title: "React Native - Navigation and more"
date: 2023-08-09T15:00:00+02:00
summary: In here we'll have a look at how to create an app that uses the StackNavigator. There are also stylesheet tips, icons handling and more
draft: false
tags: [react native]
---

## Creating an app

To create an up using Expo run ` npx create-expo-app`

## React Navigation

### StackNavigator

It takes the user to another screen, updates the header and shows a back button. So the header is automatically managed by the StackNavigator object.

#### Install

To use `react-navigation` run `npm i react-navigation`. It's better to have this set up at the very beginning of your project.

You might also need these dependencies:
```
npx expo install react-native-gesture-handler react-native-reanimated react-native-screens react-native-safe-area-context @react-native-community/masked-view -- --legacy-peer-deps
```

And the navigation stack:
```
npm install react-navigation-stack @react-native-community/masked-view --legacy-peer-deps
```

Finally find the _babel.config_ file and add the following line to the return object:
```
plugins: ["react-native-reanimated/plugin"],
```

#### Setup

Import the necessary components from `react-navigation` and `react-navigation-stack`:
```
import { createAppContainer } from "react-navigation";
import { createStackNavigator } from "react-navigation-stack";
```

Create a variable where you will store the `stackNavigator` object and then export it using `createAppContainer`:
```
const navigator = createStackNavigator({
  Search: SearchScreen
}, {
  initialRouteName: 'Search',
  defaultNavigationOptions: {
    title: 'Business Search'
  }
})

export default createAppContainer(navigator)
```
Essentially we're creating a React component that renders `SearchScreen`. The first argument takes an object where you have to eventually specify every component you want to navigate to. Kind of like routes. The second argument takes an object where you configure the default (or initial) screen and set the title. Pretty straightforward.

### BottomTabNavigator

It shows a navigation menu at the bottom of the screen where the user can navigate to other screen by tapping the icons.

### DrawerNavigator

The kind of navigation menu that slides from the side and users see a list of options to go to.

## Rules Order

A good rule of thumb is to apply the Box Object Model rules first (padding, border and margin). Then evaluate whether `position: 'absolute` is applied.

If `position: 'absolute` **is not applied**:
* Apply all flexbox rules, considering siblings.
* Place elements inside parent.
* Apply `top`, `left`, `right` and `bottom`.

If `position: 'absolute` **is applied** :
* Apply some flexbox rules, ignore all siblings.
* Apply `top`, `left`, `right` and `bottom`.

## Absolute Fill Object

If we have a list of elements and we want one to take up the entire screen, we can set its position to absolute and its position properties to `0`, like so:
```
{
  position: 'absolute',
  top: 0,
  left: 0,
  bottom: 0,
  right: 0
}
```
In order to achieve this in a simpler way Stylesheet gives us a helper for this. So we can just do:
```
{
  ...Stylesheet.absoluteFillObject
}
```

## Using Icons

Look up icons in https://icons.expo.fyi/Index. Once you find what you want, import the icon library from `@expo/vector-icons`. Afterwards you can use the library name as a component and pass a `name` property with the name of the icon you liked. Example:
```
import { Feather } from '@expo/vector-icons'

const SearchBar = () => {
  return (
    <Feather name="search" size={30} />
  )
}
```

## Miscellaneous

We can also use the `flex` property to define how much space a container within a flex element can take. For example: if I have three elements and I set `flex: 1` to the one in the middle, this element is going to take as much space as it can.

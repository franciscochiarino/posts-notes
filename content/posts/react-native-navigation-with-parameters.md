---
title: "React Native - Navigation with parameters [outdated]"
date: 2023-08-12T15:00:00+02:00
summary: Primitive elements, Stack Navigator basics, Reducer and useReducer.
draft: false
tags: [react native]
---

## Navigation

Previously we have seen that if I want to navigate from one screen to another I can use the `navigation` prop as long as my component was listed in the `createStackNavigator` object. This is as simple as extracting the `navigation` prop and doing:
```
<Button onPress={() => navigation.navigate('ScreenName')}>To ScreenName</Button>
```

### withNavigation

But what if I want to have this button in a component that's not rendered in the `createStackNavigator` object? Sure, we could pass it as props, but that's painful and messy. This is when `withNavigation` comes to play.
If we export a component using the `withNavigation` function, then we can access the `navigation` prop. Have a look at this e
```
import { withNavigation } from 'react-navigation'

const MyLinkComponent = ({ title, screenName, navigation }) => {
  return (
    <Button onPress={() => navigation.navigate(screenName)}>
      {title}
    </Button>
  )
}

export default withNavigation(MyLinkComponent)
```

### Params

Passing params is very easy. Just send them in an object as the second argument to `navigation.navigate`:
```
<Button onPress={() => navigation.navigate('Product', id: product.id)}>
```

Now the product page is going to have the `navigation` object as well. It looks like if the page is liked with the `navigate` function, it automagically gets the `navigation` prop. So from the ProductScreen we can just grab the param using the `getParam` function like so:
```
const ProductScreen = ({ navigation }) => {
  const id = navigation.getParam('id')
}
```

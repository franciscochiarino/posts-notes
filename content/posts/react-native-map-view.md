---
title: "React Native - MapView"
date: 2023-08-22T10:00:00+02:00
summary:
draft: false
tags: [react native]
---

Install `react-native-maps` to use maps. The you can import `MapView`.
```
import Mapview from 'react-native-maps'
```

### Location Permissions
1. Our map screen appears.
2. Prompt user for permission to track their location.
3. Permission requests are `async`. If the user rejects the request, the call throws an error.
4. Show error if user denies permission. Otherwise start tracking.
5. If the user returns to the map screen some other time, we will run the permissions code, but the OS will automatically approve/reject us.

### Foreground Location Tracking
This gives us users location whenever our app is visible on the screen. For this we need to install `expo-location`:
```
npx expo install expo-location -- --legacy-peers-deps
```
Then we can `import { requestForegroundPermissionsAsync } from 'expo-location'` and call it inside a `useEffect` whenever we want. Check out the response for this function [here](https://docs.expo.dev/versions/latest/sdk/location/#permissionresponse).

### Reading Location
To read users location we're gonna use `watchPositionAsync` and `Accuracy` from `expo-location`.

After requesting permission we can use `watchPositionAsync` which is going to watch the location over time. This function takes two arguments:
```
await watchPositionAsync({
  accuracy: Accuracy.BestForNavigation,
  timeInterval: 1000 // How often we update in time
  distanceInterval: 10 // How often we update in distance
}, location => {
  // This is the location callback
})
```
As we can see above, pass an object and a callback to `watchPositionAsync`. The location callback is going to be called depending on the options set in the first object. This is a good place to update our state with the user's location (for example a `currentLocation` observer).

### Faking Users Location
On development we want to fake the users location so we don't have to go around the block just to see if our code is working. To achieve this we're going to have a _mockLocation.js_ file, where we'll store our code for the fake location.

#### Step one
Console.log your location using the `watchPositionAsync` function mentioned above. It's better if the starting point of your fake location is you actual location, so there are no map jumps between your real location and the faked location.

#### Step two
Use this template code to create your `mockLocation.js` file:
```
import * as Location from 'expo-location'

const tenMetersWithDegrees = 0.0001

const getLocation = increment => {
  return {
    times: 1000000,
    coords: {
      speed: 0,
      heading: 0,
      accuracy: 5,
      altitudeAccuracy: 5,
      altitude: 5,
      latitude: XX.XXXXXXX + increment * tenMetersWithDegrees,
      longitude: -X.XXXXXXX + increment * tenMetersWithDegrees
    }
  }
}

let counter = 0

setInterval(() => {
  Location.EventEmitter.emit('Expo.locationChanged', {
    watchId: Location._getCurrentWatchId(),
    location: getLocation(counter)
  })

  counter++
}, 1000);

```
In our `coords` object, `speed`, `heading`, `accuracy`, `altitudeAccuracy` and `altitude` are common values, so you don't need to modify those. Add your location to the `latitude` and `longitude` keys.

Then we emit the `'Expo.locationChanged'` event, which is going to tell our phone that we're moving. This is gonna run every second. By adding 1 to counter every time it runs (notice `counter++`), we're constantly incrementing `latitude` and `longitude`. So with this code we're constantly changing the location.

#### Step three
Now we have to make this available wherever we're rendering tha `Map` component by just importing the file. We also have to Update `Map` by passing the region we're on:
```
<MapView
  style={styles.map}
  initialRegion={{
    ...currentLocation.coords, // <--- Our coords from mockLocation.js
    latitudeDelta: 0.01,
    longitudeDelta: 0.01
  }}
  region={{
    ...currentLocation.coords, // <--- Our coords from mockLocation.js
    latitudeDelta: 0.01,
    longitudeDelta: 0.01
  }}
>
```

### Showing the users location
Use this template code to display a circle in the user's location:
```
<MapView
  style={styles.map}
  initialRegion={{
    ...currentLocation.coords,
    latitudeDelta: 0.01,
    longitudeDelta: 0.01
  }}
>
  <Circle
    center={currentLocation.coords}
    radius={40}
    strokeColor="rgba(158, 158, 255, 1)"
    fillColor="rgba(158, 158, 255, .5)"
  />
</MapView>
```
Notice that we have removed the `region` prop from `MapView`. This is because now that the circle is there, it's easy for the user to see where she is, so no need to center the map all the time. Also, if we had the region set as we did before, when the user attempts to move the map, it jumps back to the previous region over and over. This is really annoying.

### Disabling Location Tracking
So far we learned to watch the user's location while she's using the screen where the `MapView` is rendered, but what if the user goes to another screen? Then there is no point of watching the location the whole time. This will lead to poor performance and a waste of battery power.

#### withNavigationFocused
First we have to know when is the user moving out of the screen. The best method we can use for this case is `isFocused`. This method returns a boolean depending on whether the screen is focused or not. This is how you use it:
```
import { withNavigationFocused } from 'react-navigation'

const ExampleScreen = ({ isFocused }) => {
  ...
}

export default withNavigationFocus(ExampleScreen)
```

#### Removing watchPositionAsync
`watchPositionAsync` returns a subscriber. If we want the function to stop working, we have to do `subscriber.remove()`. For my practice app I moved all the map tracking/watching logic to a hook that looks like this:
```
import { useState, useEffect } from 'react'
import { Accuracy, requestForegroundPermissionsAsync, watchPositionAsync } from 'expo-location'

export default (shouldTrack, callback) => {
  const [isPermissionGranted, setIsPermissionGranted] = useState(false)
  const [subscriber, setSubscriber] = useState(null)

  const startWatching = async () => {
    try {
      const { granted } = await requestForegroundPermissionsAsync()
      const sub = await watchPositionAsync({
        accuracy: Accuracy.BestForNavigation,
        timeInterval: 1000,
        distanceInterval: 10
      }, location => {
        callback(location)
      })

      setIsPermissionGranted(granted)
      setSubscriber(sub)
    } catch (e) {
      console.error(e)
    }
  }

  useEffect(() => {
    if (shouldTrack) {
      startWatching()
    } else {
      subscriber.remove()
      setSubscriber(null)
    }
  }, [shouldTrack])

  return [isPermissionGranted]
}
```
We've seem some of this stuff already. The important thing here is that we're taking `shouldTrack` and `callback` as arguments. Have a look at the `useEffect`: if `shouldTrack` is `true` we're calling `startWatching`. For this argument we can use `isFocused`, so when the user is looking at the screen `shouldTrack` is `true` and otherwise it's false.

Notice that after calling `watchPositionAsync` and saving the return value in the `sub` variable, we assign it to the `subscriber` state. Finally we return `isPermissionGranted`. (It's in an array bc that's a hooks convention)

So this is how it works:
1. We call this hook in our screen like so: `const [isPermissionGranted] = useLocation(isFocused, saveLocation)` (being `saveLocation` your callback).
2. If the user is on the screen: `isFocused` is going to be `true` so `watchPositionAsync` is going to run and set `subscriber` to its return value.
3. If the user is not on the screen: the screen component is going to be re-rendered because `isFocused` changed its value, so the hook is going to be called again, but now it's going to fall in the `else` statement of `useEffect` and therefore call `subscriber.remove()` and set `subscriber` to `null` again.

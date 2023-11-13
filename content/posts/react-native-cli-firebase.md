---
title: 'React Native CLI - Firebase Setup'
date: 2023-11-13T09:00:00+02:00
summary: In this post we're gonna go through the setup of native Firebase integration with android and iOS.
draft: false
tags: [react native]
---

## iOS Setup

1. Download the GoogleService-Info.plist file when creating your project connection in Firebase.
2. Open the .xcworkspace file in xcode.
3. **Drag and drop** the GoogleService-Info.plist file in your project folder. It should be next to the AppDelegate files.
4. In your ios directory run `bundle i`
5. In your ios directory run `bundle exec pod install`

You will get this error:

```
[!] The following Swift pods cannot yet be integrated as static libraries:

The Swift pod `FirebaseCoreInternal` depends upon `GoogleUtilities`, which does not define modules. To opt into those targets generating module maps (which is necessary to import them from Swift when building as static libraries), you may set `use_modular_headers!` globally in your Podfile, or specify `:modular_headers => true` for particular dependencies.
```

You have to manually add these packages. Go to your Podfile and add these pods before your target block:

```
pod 'FirebaseCoreInternal', :modular_headers => true
pod 'GoogleUtilities', :modular_headers => true
```

In your ios directory, run `bundle exec pod install` again.

Clean all issues and clean build folder as well: In xcode, go to Product > Clean All Issues and then Product > Clean Build Folder.

```
cd ios
bundle exec pod cache clean --all
rm -rf ~/Library/Caches/CocoaPods
rm -rf Pods
rm -rf ~/Library/Developer/Xcode/DerivedData/\*
bundle exec pod deintegrate
bundle exec pod setup
bundle exec pod install --repo-update
```

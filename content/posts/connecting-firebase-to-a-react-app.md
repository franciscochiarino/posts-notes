---
title: "Connecting Firebase to a React App"
date: 2023-06-18T19:53:29+02:00
summary: Let's have a look at Firebase and how to configure it.
draft: false
tags: [firebase, react]
---

Before this article you should have a basic react app (doesn't matter if it has nothing in it) and a Firebase account. If you don't have a Firebase account, go ahead and create one in https://firebase.google.com.

## Installation
Open a new terminal inside your project folder. If you are using `yarn` run:
```
yarn add firebase
```
If you are using `npm` you should run:
```
npm i firebase
```
Move to the next point while this is installing.

## Creating a Project

Once you're logged in to Firebase, click on the "Get Started" button. Create a new project, give it a name and click continue. Select the default analytics account if you want it and click "Create Project". Once this is done, you'll be redirected to the dashboard of your Firebase project. Click on the web icon:

![firebase](/images/firebase-get-started.png)

Give it a name and uncheck hosting. After this is done, you'll be on "Add Firebase SDK" step, where you'll see some code like this:
```
import { initializeApp } from "firebase/app";

const firebaseConfig = {
  apiKey: "AIzaSyBG_9s2JJDUeBLtMenyPtIBsVLupa8vRB8",
  authDomain: "fir-tutorial-ad573.firebaseapp.com",
  projectId: "fir-tutorial-ad573",
  storageBucket: "fir-tutorial-ad573.appspot.com",
  messagingSenderId: "459866772432",
  appId: "1:459866772432:web:b029523d67a8c9f1981fd5",
  measurementId: "G-2R62T7YE0E",
};

const app = initializeApp(firebaseConfig);
```

Here we have all the configuration information **related to the project** (we don't have a database yet). Let's copy this, create a file called _firebaseConfig.js_ under our _src_ directory and paste it there.

We've already established a connection. So let's create a Firestore Database.

## Creating a Database
In your Firebase dashboard, click on Firestore Database in the aside menu and then on "Create database".
* Select "Start in Production Mode" and click "Next"
* Select a Firestore location that's closest to where you are and click "Enable"

Once this is done you'll be in the Cloud Firestore page. Click on the rules tab. We're not gonna get into the details of this file. Basically it controls _who_ can do _what_ in this database, but since this is just an example, we're gonna give write access to anyone:
```
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

Your database is ready! 🎉

## Connecting Database
So far we have our project connected and our database created. Now it's time to connect the database. Go to _firebaseConfig.js_, import firestore and connect it to the app:
```
import { initializeApp } from "firebase/app";
import { getFirestore } from "@firebase/firestore";

const firebaseConfig = {
  # ...
};

const app = initializeApp(firebaseConfig);
export const db = getFirestore(app);
```

## Cleanup
You don't want to publish the whole `firebaseConfig` object for anyone in the internet to see, so let's put these variables inside an `.env` file.

In the root directory create a file called `.env`. Remember to git-ignore this file by adding it to `.gitignore`. Then add the variable names with the prefix `REACT_APP_`:
```
REACT_APP_API_KEY=AIzaSyBG_9s2JJDUeBLtMenyPtIBsVLupa8vRB8
REACT_APP_AUTH_DOMAIN=fir-tutorial-ad573.firebaseapp.com
REACT_APP_PROJECT_ID=fir-tutorial-ad573
...
```

Afterwards you can access these variables from your _firebaseConfig.js_ file like this:
```
const firebaseConfig = {
  apiKey: process.env.REACT_APP_API_KEY,
  authDomain: process.env.REACT_APP_AUTH_DOMAIN,
  ...
}
```

And you're ready to go! 🚀

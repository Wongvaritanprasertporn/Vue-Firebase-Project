Vue Firebase Web Application Project
====================================

This is a Vue.js web application project that utilizes SCSS for styling, Firebase Authentication, and Firebase Firestore for data storage. The Firebase configuration is implemented in `src/main.js`.

Project Setup
-------------

To set up the project, run the following command to install dependencies:

`npm install`

Development
-----------

To compile and hot-reload the project for development, run the following command:

`npm run serve`

Production Build
----------------

To compile and minify the project for production, run:

`npm run build`

After running this command, the compiled project will be available in the `dist` folder.

Linting
-------

To lint and fix files, run:

`npm run lint`

Firebase Configuration
----------------------

Add your Firebase configuration in the `src/main.js` file. Use the following template:

```const firebaseConfig = {   apiKey: "YOUR_API_KEY",   authDomain: "YOUR_AUTH_DOMAIN",   databaseURL: "YOUR_DATABASE_URL",   projectId: "YOUR_PROJECT_ID",   storageBucket: "YOUR_STORAGE_BUCKET",   messagingSenderId: "YOUR_MESSAGING_SENDER_ID",   appId: "YOUR_APP_ID",   measurementId: "YOUR_MEASUREMENT_ID" };```

Replace `YOUR_API_KEY`, `YOUR_AUTH_DOMAIN`, etc., with your Firebase project credentials.

Firebase Hosting
----------------

To deploy the project to Firebase Hosting, follow these steps:

1.  Build the project by running:
    
    `npm run build`
    
2.  Install the Firebase CLI if you haven't already:
    
    `npm install -g firebase-tools`
    
3.  Log in to Firebase:

    `firebase login`
    
4.  Initialize Firebase in your project directory:
    
    `firebase init`
    
    Follow the prompts to select Firebase Hosting and choose the Firebase project you want to use.
    
5.  Deploy your project to Firebase Hosting:
    
    `firebase deploy`
    
After deploying, your web application will be accessible via the provided Firebase Hosting URL.

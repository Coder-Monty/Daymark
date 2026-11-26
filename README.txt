DAYMARK — NEW, SEPARATE FIREBASE PROJECT
========================================

This package is prepared for a NEW Firebase project just for Daymark.
It does NOT use the old DSA Tracker Firebase project.

PACKAGE CONTENTS
----------------
public/index.html   Daymark website. Email/password and Google login are available.
firestore.rules     Per-user Firestore security rules.
firebase.json       Firebase Hosting + Firestore rules deployment config.
.firebaserc         Project alias placeholder. Replace the project ID before deploy.

PART A — CREATE A COMPLETELY NEW FIREBASE PROJECT
-------------------------------------------------
1. Open https://console.firebase.google.com/
2. Click Add project / Create a project.
3. Name it Daymark (or any name you prefer).
4. Choose/confirm a unique Project ID, for example daymark-xxxxx.
   Project IDs must be globally unique and cannot be changed later.
5. Google Analytics is optional for this tracker; you can turn it off.
6. Finish creating the project.

PART B — REGISTER DAYMARK AS A WEB APP
--------------------------------------
1. In the new Daymark project, open Project Overview.
2. Click the Web icon </> (Add app).
3. App nickname: Daymark Web.
4. Register the app. Hosting setup can be done later.
5. Firebase displays a firebaseConfig object. Copy its values.
6. Open public/index.html in a text editor.
7. Find `const firebaseConfig = {` near the beginning of the script.
8. Replace ALL placeholder values with the values Firebase gave you:
   - apiKey
   - authDomain
   - projectId
   - storageBucket
   - messagingSenderId
   - appId
   - measurementId only if supplied (optional)
   Copy storageBucket exactly; do not guess its suffix.
8. Save index.html.

PART C — ENABLE BOTH LOGIN METHODS
----------------------------------
1. In Firebase Console → Build → Authentication → Get started.
2. Open Sign-in method / Sign-in providers.
3. Enable Email/Password and save.
4. Enable Google.
5. For Google, select a support email if requested, then Save.

Daymark's login popup now provides:
- Create account / sign in using email + password.
- Continue with Google.
- Forgot password for email/password accounts.
- Continue as guest (data remains in this browser).

Use the same login method consistently on your devices. If the same email was first registered with one provider and you later try another, Firebase may ask for account linking. Do not create duplicate accounts intentionally.

PART D — CREATE FIRESTORE AND APPLY SECURITY RULES
--------------------------------------------------
1. In the NEW Daymark project, open Build → Firestore Database.
2. Click Create database.
3. Choose Production mode.
4. Choose a region close to most of your users. The database location is difficult to change later.
5. Create the database.
6. In Firestore → Rules, publish the contents of this package's firestore.rules:

   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{userId} {
         allow read, write: if request.auth != null && request.auth.uid == userId;
       }
     }
   }

Each signed-in user can access only their own /users/{uid} document.

PART E — PREPARE FIREBASE HOSTING
---------------------------------
1. Open Firebase Console → Build → Hosting in the NEW Daymark project.
2. Click Get started and follow the initial setup if prompted.
3. Firebase's default site URL will normally be:
   https://daymark-2026.web.app/, https://daymark-2026.firebaseapp.com/

PART F — DEPLOY FROM WINDOWS (SAME GENERAL METHOD AS YOUR OLD SITE)
--------------------------------------------------------------------
1. Install Node.js LTS: https://nodejs.org/en/download
2. Open CMD / Windows Terminal and check:
   node -v
   npm -v
3. Install Firebase CLI:
   npm install -g firebase-tools
4. Open CMD in the extracted Daymark folder (the folder containing firebase.json).
5. Log in:
   firebase login
6. Open `.firebaserc` in Notepad and replace YOUR_DAYMARK_PROJECT_ID with the NEW project's exact Project ID. Save it.
7. Deploy Firestore rules:
   firebase deploy --only firestore:rules
8. Deploy the website:
   firebase deploy --only hosting

The package's firebase.json publishes only the `public` folder and points Firestore rules to firestore.rules.

PART G — AUTHORIZE THE WEBSITE DOMAIN
-------------------------------------
After the first Hosting deploy:
1. Open Firebase Console → Authentication → Settings.
2. Find Authorized domains.
3. Add YOUR_DAYMARK_PROJECT_ID.web.app if it is not already listed.
4. Add localhost if you want to test login from a local server and it is not listed.

PART H — TEST LOGIN AND CROSS-DEVICE SYNC
-----------------------------------------
1. Open https://daymark-2026.web.app/, https://daymark-2026.firebaseapp.com/
2. Test Create account with email/password.
3. Log out and test Continue with Google.
4. Add a task/checkpoint on one device.
5. Open the same URL on another device and sign in using the SAME account.
6. Check that tasks, daily checkpoints, theme and target date sync.
7. In Firebase Console → Authentication → Users, you can see registered accounts.
8. In Firestore → Data, Daymark stores each account under users/{uid}.

IMPORTANT SEPARATION NOTES
--------------------------
- Do NOT select dsa-tracker-75ab8 during setup. Select/create the NEW Daymark project only.
- This new project has its own Authentication users, Firestore database and Hosting domain.
- It will not share DSA Tracker accounts or cloud data.
- Guest data is local to the browser. It stays after reloads, but only account login syncs across devices.
- The Firebase web config is meant to be included in browser code. Firestore security rules protect the data; never place a service-account private key in index.html.
- Until you replace the placeholder firebaseConfig, Daymark remains usable in guest mode but login and cloud sync will not work.

# Daymark

**Daily progress, made visible.**

Daymark is a lightweight daily productivity tracker for building consistency. Organize recurring daily tasks, mark checkpoints, review your progress on a calendar, and keep an eye on your streaks and target date.

🌐 **Live website:** [https://daymark-2026.web.app/](https://daymark-2026.web.app/)

## Features

### Daily planning and checkpoints
- Create recurring daily tasks from the day they are added.
- Mark tasks complete from today's checkpoint.
- Click anywhere on a task row to toggle its completion.
- A checkpoint is completed only when every active task for that day is complete.
- Remove a task from today onward without erasing its earlier checkpoint history.
- Review previous dates in read-only mode; past checkpoints cannot be edited.
- Future dates are disabled and do not display task counts before they become today.

### Calendar and consistency
- Monthly calendar showing daily completion progress.
- Incomplete days are visually distinguished from completed days.
- Calendar labels show progress such as `2/3 tasks done`, or `3 tasks done` when all tasks are complete.
- Current and best streak tracking.
- A day contributes to a streak only when all tasks active on that date are completed.
- The current streak becomes active after today's checkpoint is completed.
- The calendar updates when the device's local date changes.

### Time and goals
- Live local clock and date.
- Target-date countdown with an edit option.
- Countdown updates down to the second.
- Set, change, or clear the target date.

### Appearance and experience
- Light and dark themes.
- Responsive layout for desktop and mobile browsers.
- Minimal interface with quick task interactions.

### Accounts and data
- Continue as a guest without signing in.
- Guest progress is saved in the browser so it remains after a normal page reload.
- Create an account or sign in using email and password.
- Sign in with Google.
- Reset a forgotten password by email.
- Firebase Authentication keeps the account signed in using local browser persistence.
- Cloud Firestore stores account data and syncs it across devices.
- Changes are saved locally and queued for cloud sync when needed; routine page refreshes and tab focus do not intentionally trigger unnecessary uploads.
- Firestore updates from the account are observed so changes made on another device can appear.

## How task checkpoints work

1. Add a task on the day you want it to start appearing.
2. That task is included in daily checkpoints from its creation date onward.
3. Complete the task from today's checkpoint.
4. The calendar shows the number of completed tasks against the total active tasks for each date.
5. Once all active tasks for a date are complete, that date's checkpoint is marked completed and counts toward the streak.

Past checkpoints are for review and are read-only. Removing a task archives it from the current date onward, preserving its earlier history.

## Tech stack

- **HTML5** — application structure
- **CSS3** — styling, responsive layout, and light/dark themes
- **Vanilla JavaScript** — application logic and browser-side state
- **Firebase Authentication** — email/password and Google sign-in
- **Cloud Firestore** — account data and cross-device synchronization
- **Browser localStorage** — guest data and local account cache
- **Firebase Hosting** — deployment and hosting

The app is built as a static web application and does not require a frontend build step.

## Run locally

You can serve the static files using Python:

1. Clone or download this repository.
2. Open a terminal in the project directory.
3. Start a local HTTP server:

   ```bash
   python -m http.server 8000
   ```

4. Open [http://localhost:8000](http://localhost:8000).

You can also use the **Live Server** extension in Visual Studio Code.

> Use `localhost` or an authorized HTTPS domain for Firebase Authentication. Opening the page directly with a `file://` URL may prevent authentication from working correctly.

## Firebase configuration

To run your own deployment with Firebase:

1. Create a project in the [Firebase Console](https://console.firebase.google.com/).
2. Register a Web App and copy its Firebase web configuration.
3. Add the configuration to the `firebaseConfig` object in the application code.
4. Enable **Email/Password** and **Google** under **Authentication → Sign-in method**.
5. Create a **Cloud Firestore** database.
6. Add your local development and hosting domains under **Authentication → Settings → Authorized domains**.
7. Configure Firestore Security Rules so each signed-in user can access only their own data.

### Example Firestore security rules

The application stores each account's tracker state in a user-scoped document. If your code uses the `users/{userId}` document path, rules can be structured like this:

```javascript
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null
                         && request.auth.uid == userId;
    }
  }
}
```

Review the document path in your own code before publishing rules. Never use open read/write rules in production.

**Security note:** Do not put Firebase Admin SDK credentials or service-account private keys in frontend code. Firebase web configuration is designed to be included in client applications; protect user data with Authentication and Firestore Security Rules.

## Data notes

- Guest data is stored in the browser and is specific to that browser/device.
- Clearing browser site data can remove locally stored guest progress.
- Signing in lets the account's data sync through Firestore and be accessed on other devices.
- Use the same Daymark account on each device to access the same synced data.
- Keep a backup before making manual changes to the database or application storage format.

## Deploy to Firebase Hosting

After installing the Firebase CLI and selecting the correct Firebase project, deploy the hosting files with:

```bash
firebase deploy --only hosting
```

For setup and deployment details, see the [Firebase Hosting documentation](https://firebase.google.com/docs/hosting).

## Live demo

Try Daymark here:

**[https://daymark-2026.web.app/](https://daymark-2026.web.app/)**

---

Made for small daily actions that add up.

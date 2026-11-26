# Firebase setup for standalone Live Play

The `fix/live-play-firebase` branch replaces the Claude-only room API with Firebase Realtime Database. The published `main` branch is unchanged until this draft pull request is reviewed and merged.

## 1. Create a Firebase project

1. Open https://console.firebase.google.com/ and create a project.
2. In **Build → Authentication → Sign-in method**, enable **Anonymous**.
3. In **Build → Realtime Database**, create a database. Choose a region close to most players.
4. In **Project settings → General → Your apps**, register a Web app and copy its Firebase configuration.

## 2. Add the web configuration

In `index.html` on this branch, replace the placeholder values in `FIREBASE_CONFIG` with the exact values from the Firebase web app config. Copy the `databaseURL` exactly as shown by Firebase; regional database URLs may use the `.firebasedatabase.app` domain.

Firebase web config is intended to be included in client-side apps. Do not put service-account keys or Admin SDK credentials in this file.

## 3. Set Realtime Database rules

For an initial family-game test, use these rules so only authenticated Firebase users (the game signs players in anonymously) can access room data:

```json
{
  "rules": {
    "jeopardyRooms": {
      "$room": {
        ".read": "auth != null",
        ".write": "auth != null"
      }
    }
  }
}
```

These are starter rules, not strong per-room authorization: any signed-in app user who knows a room code can read or write that room. Use a long, unguessable room code for private testing and do not use this setup for sensitive data. A production public game should add server-enforced room membership, host/player permissions, and rate limits.

## 4. Test before publishing

1. Open the game as host in one browser/device and create a room.
2. Open the player join page on a second device/browser and enter the room code.
3. Confirm the player appears on the host screen.
4. Test buzzing, wrong-answer retry, Daily Double wager, Final Jeopardy wager/answer, scoring, refresh/reconnect, and closing a tab.
5. Check the browser console and Firebase Realtime Database data if a test fails.

## 5. Publish

The change is in a draft pull request. Do not merge until Firebase is configured and the two-device tests pass. After merging, GitHub Pages can publish the updated `main` branch using the repository's existing Pages settings.

# Backend setup (viewer counts, shared likes, admin)

The site works without this: likes then stay on the visitor's own device and viewer counts are hidden.
To share likes, show live viewer counts and unlock the admin panel, connect a free Firebase project.

1. Create a project at https://console.firebase.google.com
2. **Build > Realtime Database > Create database** (any region, start in locked mode).
3. **Build > Authentication > Get started > Email/Password > enable**. Then open the *Users* tab,
   **Add user** with the email and password you want to use as admin, and copy that user's **User UID**.
4. **Realtime Database > Rules**: paste this, replacing `ADMIN_UID` with the UID from step 3, then Publish.

```json
{
  "rules": {
    "admin": { ".read": true, ".write": "auth.uid === 'ADMIN_UID'" },
    "votes": {
      ".read": true,
      ".write": "auth.uid === 'ADMIN_UID'",
      "$ev": { "$uid": {
        ".write": "$uid.length <= 40 && (!newData.exists() || newData.val() === 1 || newData.val() === -1)"
      } }
    },
    "presence": {
      ".read": true,
      "$ev": { "$uid": {
        ".write": "$uid.length <= 40 && (!newData.exists() || newData.isNumber())"
      } }
    }
  }
}
```

5. **Project settings > General > Your apps > Web app** (register one) to get the `apiKey`. The database URL is
   shown at the top of the Realtime Database page.
6. Put both values in `config.js`, commit, push.

Admin: click **Admin** in the top bar and sign in. You can pin or hide events, reset an event's votes,
see viewers / likes / dislikes per event, and set an announcement banner shown to everyone.
Admin sign-in lasts about an hour, then sign in again.

Notes: the API key and database URL are public by design; the rules above are what protect writes.
Votes are one per browser (anonymous id kept in localStorage), so they can be cleared or faked by a determined
visitor; that is the tradeoff of having no accounts.

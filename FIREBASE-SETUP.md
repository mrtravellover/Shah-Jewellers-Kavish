# Shah Jewellers — Firebase Setup (≈15 minutes)

Right now the site works perfectly, but anything you post in the Owner Panel saves **only on the device you used**. Connecting Firebase (free) makes your slider images, gallery photos and daily updates show up for **every customer, on every phone**.

You only edit **two lines** in `index.html` (both near the top of the `<script>` section):

```js
const FIREBASE_CONFIG = null;   // ← paste your config here
const ADMIN_EMAIL = "";         // ← put your owner-login email here
```

Follow the steps below to fill those in.

---

## Step 1 — Create a Firebase project
1. Go to **https://console.firebase.google.com** and sign in with your Google account.
2. Click **Add project** → name it `shah-jewellers` → Continue.
3. Google Analytics is optional — you can turn it **off** → Create project → wait, then Continue.

## Step 2 — Add a Web App and copy the config
1. On the project home, click the **`</>` (Web)** icon.
2. App nickname: `Shah Jewellers Web` → **Register app**.
3. You'll see a `const firebaseConfig = { ... }` block. **Copy the whole `{ ... }` object.**
4. Open `index.html`, find `const FIREBASE_CONFIG = null;` and replace `null` with what you copied. It should look like:

```js
const FIREBASE_CONFIG = {
  apiKey: "AIza....",
  authDomain: "shah-jewellers.firebaseapp.com",
  projectId: "shah-jewellers",
  storageBucket: "shah-jewellers.appspot.com",
  messagingSenderId: "123...",
  appId: "1:123...:web:abc..."
};
```
Save the file.

## Step 3 — Turn on the database (Firestore)
1. Left menu → **Build → Firestore Database → Create database**.
2. Choose **Start in production mode** → Next.
3. Location: pick **asia-south1 (Mumbai)** → Enable.
4. Open the **Rules** tab, replace everything with the rules in **Step 6**, then **Publish**.

## Step 4 — Turn on photo storage (Storage)
1. Left menu → **Build → Storage → Get started**.
2. Keep production mode → Next → same location (asia-south1) → Done.
3. Open its **Rules** tab, paste the Storage rules from **Step 6**, then **Publish**.

## Step 5 — Create your owner login (Authentication)
1. Left menu → **Build → Authentication → Get started**.
2. Choose **Email/Password** → toggle it **on** → Save.
3. Go to the **Users** tab → **Add user** → enter your email + a strong password. *This is your owner login.*
4. In `index.html`, set the email line to that same email:
```js
const ADMIN_EMAIL = "youremail@gmail.com";
```
Save.

## Step 6 — Security rules (copy-paste)

**Firestore rules** (Firestore → Rules):
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /site/content {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

**Storage rules** (Storage → Rules):
```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /images/{file} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```
These let **anyone view** your site, but only **you (logged in)** can add or delete content.

## Step 7 — Deploy
- Commit/push the updated `index.html` to GitHub. Vercel redeploys automatically (about a minute).
- Open your site → footer → **Owner Login** → enter the password you set in Step 5.
- You should see the badge change from **"On this device"** to **"Live (cloud)"**.
- Add a slider image or update — then open the site on your phone. It's there. 🎉

---

## Notes
- **Add your content *after* connecting Firebase.** Anything you posted while testing locally stays on that one device and won't transfer automatically — just re-add it once you're live.
- The values in `FIREBASE_CONFIG` are **safe to put in the public file** — they're meant to be in the browser. Your data is protected by the rules above, not by hiding these keys.
- Forgot the owner password? Reset it in Firebase → Authentication → Users.
- Want a quicker test without login (less secure)? Leave `ADMIN_EMAIL = ""` and set both rules' `write` to `if true;`. Only do this briefly — it lets anyone write. Switch to the Auth version before real use.

Stuck on any step? Tell me which step number and what you see, and I'll sort it out.

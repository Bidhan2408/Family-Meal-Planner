# Our Kitchen – setup guide

Your family meal planner, hosted for free on your own accounts. Setup takes about 20 minutes, once. Nothing here costs money and no card is needed.

You will need: a Google account (for Firebase, which stores the shared plan) and an email address to sign up to Netlify (which serves the page).

---

## 1. Create the Firebase project (stores the shared data)

1. Go to **https://console.firebase.google.com** and sign in with a Google account.
2. Click **Create a project** (or **Add project**). Name it `Our Kitchen`. Turn **off** Google Analytics when asked. Click **Create project**, then **Continue**.

### 1a. Turn on the database

1. In the left menu open **Build → Firestore Database**, then click **Create database**.
2. If asked for an edition, choose **Standard**. Pick a location near you (for example `asia-south1` for India/Nepal, `europe-west2` for the UK, `australia-southeast1` for Australia). Choose **Start in production mode**. Click **Create**.
3. Open the **Rules** tab, delete everything there, paste the text below, and click **Publish**.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

This means: only people signed in to your app can read or change the plan.

### 1b. Turn on sign-in

1. In the left menu open **Build → Authentication** and click **Get started**.
2. Open the **Sign-in method** tab, click **Email/Password**, switch **Enable** on, and **Save**.
3. Open the **Users** tab, click **Add user**, and type the family email and a password. This is what everyone in the family will use to sign in, on every phone. Click **Add user**.

### 1c. Get the config for the app

1. Click the **gear icon** next to *Project Overview* (top left) → **Project settings**.
2. Scroll down to **Your apps** and click the **web icon `</>`**.
3. Nickname: `Our Kitchen`. Leave *Firebase Hosting* unticked. Click **Register app**.
4. You'll see a block of code with `const firebaseConfig = { apiKey: "...", ... }`. Keep this page open; you need those values in the next step.

---

## 2. Put the config into the app file

1. Open the folder `our-kitchen` and open `index.html` in a plain text editor:
   - Windows: right-click → **Open with → Notepad**.
   - Mac: open **TextEdit**, then *Format → Make Plain Text* before pasting, or use any code editor (VS Code is free).
2. Near the top of the file you'll see:

```
window.FIREBASE_CONFIG = {
  apiKey: "PASTE_HERE",
  authDomain: "PASTE_HERE.firebaseapp.com",
  ...
};
```

3. Replace each `PASTE_HERE` value with the matching value from the Firebase page (apiKey, authDomain, projectId, storageBucket, messagingSenderId, appId). Keep the quotation marks. It should end up looking exactly like Firebase's block.
4. Save the file. Make sure it is still called `index.html` (not `index.html.txt`).

If you open `index.html` in a browser before pasting the config, it shows a "One more step" screen instead of the app. That's how you know it needs the config.

---

## 3. Put the page online (free)

1. Go to **https://app.netlify.com/drop**.
2. Drag the whole **`our-kitchen` folder** (the one containing `index.html`, `sw.js`, the manifest and the icons) onto the page.
3. Netlify gives you a link like `https://glittering-tulip-1a2b3c.netlify.app`. Sign up for a free account when it asks, so the site stays up.
4. Optional: in Netlify, **Site configuration → Site details → Change site name** to something nicer, like `our-kitchen-sharma`. The link becomes `https://our-kitchen-sharma.netlify.app`.

Alternative if you'd rather use GitHub: create a free account, make a new public repository, upload `index.html`, then in the repository's **Settings → Pages** choose *Deploy from a branch → main → / (root)*. The link appears there after a minute.

### 3a. Tell Firebase about the link

Back in Firebase: **Authentication → Settings → Authorized domains → Add domain**, and add your Netlify (or GitHub Pages) domain, e.g. `our-kitchen-sharma.netlify.app`.

---

## 4. Install it on everyone's phone

The link is only needed once per phone, like a download link. After that it is an app: its own icon, full screen, no browser bar, works offline.

1. Open the link on the phone and sign in with the family email and password. Each phone stays signed in.
2. Install it:
   - **Android:** the app shows an **Install** banner at the top of the week; tap it (or Chrome's menu ⋮ → *Install app*). It appears in the app drawer with the other apps.
   - **iPhone:** in Safari tap the **Share** button, then **Add to Home Screen**. (Apple only allows this route; it works just as well.)
3. On the first phone: **Settings (gear) → Add the starter recipes** if you want the 26 sample dishes to start from (delete what you won't cook), or skip it and add your own dishes.

That's it. Everyone sees the same plan, live. Keep all six files together when you upload: `index.html`, `sw.js`, `manifest.webmanifest` and the three icon images.

---

## Everyday things

- **Adding a dish you know by heart:** Recipes → **+** → *Write it myself*. Type the name, keep *Staple, no steps*, Save.
- **Nutrition without typing numbers:** in the dish editor tap **Estimate via ChatGPT**, copy the prompt, paste it into the free ChatGPT, copy its reply back, tap *Fill in the numbers*.
- **New recipes from ChatGPT:** Recipes → **+** → *Get it from ChatGPT*. The prompt asks for a recipe in the app's format. You can attach a photo of a cookbook page or paste recipe text under the prompt in ChatGPT.
- **Backups:** Settings → **Download a backup** saves everything to a file. *Restore a backup* loads it again, on this or any other copy of the app.
- **Updating the app later:** when you get a new `index.html`, paste your config block into it again (copy it from the old file), then drag the folder onto Netlify again (**Deploys → drag and drop**). Installed phones pick up the new version the next time they open the app while online. Your data lives in Firebase, so nothing is lost.
- **Play Store / App Store?** Not needed for a family app; installing from the link gives the same icon and full-screen experience. If you ever want it in the Play Store anyway, the free tool at pwabuilder.com packages this exact site for a Google Play developer account (one-off $25). Apple's App Store needs a paid developer account, so on iPhone the home-screen install is the sensible route.

## If something goes wrong

- *"One more step" screen:* the config isn't pasted in, or a `PASTE_HERE` is still there.
- *"Email or password didn't match":* check the user you created in Firebase → Authentication → Users.
- *"Email sign-in isn't switched on yet":* step 1b wasn't finished.
- *"No access to the recipes":* the rules from step 1a weren't published.
- *Changes don't appear on another phone:* make sure both are signed in with the same email, and online. Edits made offline sync when the phone reconnects.

## What the free limits mean

Firebase's free plan allows 50,000 reads and 20,000 writes per day and 1 GB of storage. A family planning meals uses a tiny fraction of that. Photos are stored shrunk down, so even a few hundred dishes with photos fit easily.

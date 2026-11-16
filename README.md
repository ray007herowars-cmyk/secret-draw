# SECRET DRAW

Everyone enters a name, presses DRAW, and gets another player's name. Never yourself, never a name someone else already has.
Static site + Firebase (free Spark plan). No server to run.

## 1. Firebase setup (5 min)
1. https://console.firebase.google.com → **Add project**.
2. **Build → Firestore Database → Create database** (production mode).
3. **Build → Authentication → Get started → Sign-in method → Anonymous → Enable.**
4. **Firestore → Rules** → paste the contents of `firestore.rules` → **Publish**.
5. **Project settings → Your apps → Web (`</>`)** → register an app → copy the config into `config.js`.

## 2. Push to GitHub
```bash
git init && git add . && git commit -m "secret draw"
git branch -M main
git remote add origin https://github.com/<you>/<repo>.git
git push -u origin main
```

## 3. Deploy
- **GitHub Pages:** repo → Settings → Pages → Source: *Deploy from a branch* → `main` / root.
- Netlify, Vercel or Cloudflare Pages also work: import the repo, no build command, output dir `/`.

Then: Firebase → Authentication → Settings → **Authorized domains** → add your site domain (e.g. `<you>.github.io`).

## Playing
Share the link. Everyone joins first, then everyone draws. For a new game, change `ROUND` in `config.js` (e.g. `"r2"`), commit and push.

## How the rules are enforced (`firestore.rules`)
- Each player writes only their own player doc, once.
- `assignments/<you>` is readable only by you.
- `taken/<target>` is create-only, so a second claim on the same person is rejected. It is written in the same atomic batch as your assignment.
- The app also avoids a dead end where the last drawer would only have themselves left.

## Honest limits
- Identity is an anonymous sign-in tied to the browser. Clearing site data or switching device means that person can't see their result again (and can't re-draw).
- The list of names is readable by signed-in clients (the app needs it to pick), so someone technical could read it in dev tools. Who drew whom stays private. For strict secrecy use a Cloud Function (needs Firebase Blaze plan).
- `apiKey` in `config.js` is not a secret for Firebase web apps; the rules protect your data.

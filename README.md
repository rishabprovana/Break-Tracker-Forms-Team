[README.md](https://github.com/user-attachments/files/30459198/README.md)
# Team Break Board — GitHub Pages setup

This is a single-file app (`index.html`). It shows a team board and, for each
person, their own page (`yoursite.com/#108395`) with start/stop buttons and a
full break history — all in the same file, switching view based on the `#`
in the URL.

GitHub Pages only serves static files — there's no server behind it — so to
let everyone's break status stay in sync, this uses a free **Firebase
Realtime Database** as the shared backend. It takes about 5 minutes to set up
and requires no coding.

## 1. Create a free Firebase Realtime Database

1. Go to https://console.firebase.google.com and sign in with any Google account.
2. Click **Add project**, give it any name (e.g. `team-break-board`), and finish the wizard (you can skip Google Analytics).
3. In the left sidebar, go to **Build → Realtime Database**.
4. Click **Create Database**. Pick any region. When asked about security rules, choose **Start in test mode** for now (this makes it open to anyone with the link — fine for an internal 12-person tool, but see the note on security below).
5. Once created, you'll see a URL at the top of the database page that looks like:
   `https://team-break-board-default-rtdb.firebaseio.com`
   Copy that whole URL.

## 2. Paste the URL into `index.html`

Open `index.html` and find this line near the top of the `<script>`:

```js
const DATABASE_URL = 'PASTE_YOUR_FIREBASE_DATABASE_URL_HERE';
```

Replace the placeholder with the URL you copied, e.g.:

```js
const DATABASE_URL = 'https://team-break-board-default-rtdb.firebaseio.com';
```

Save the file.

## 3. Put it on GitHub Pages

1. Create a new GitHub repository (public or private both work with Pages on a paid plan; public repos get Pages free).
2. Upload `index.html` (and this `README.md`) to the repo.
3. Go to the repo's **Settings → Pages**.
4. Under **Build and deployment**, set **Source** to `Deploy from a branch`, branch `main`, folder `/ (root)`. Save.
5. GitHub will give you a URL like `https://yourusername.github.io/your-repo/`. That's your team's board.

Share that link — everyone opens the same URL and sees the shared board.
Each person's personal link (from the 🔗 button on the board) will look like
`https://yourusername.github.io/your-repo/#108395`.

## About security

"Test mode" Firebase rules mean anyone with your database URL can read or
write to it — there's no login. That's a reasonable trade-off for a small
internal tool with no sensitive data (just names, titles, and break
timestamps already visible on the board), but two things to know:

- Test mode rules **expire after 30 days** and revert to fully locked (nothing
  will load). When that happens, go back to **Realtime Database → Rules** in
  the Firebase console and set:

  ```json
  {
    "rules": {
      "employees": {
        ".read": true,
        ".write": true
      }
    }
  }
  ```

  This keeps it open indefinitely, scoped to just the `employees` path this
  app uses.

- If you'd rather require people to sign in before they can start/stop
  breaks, that's a bigger change (Firebase Auth + updated rules) — let me
  know if you want that added.

## Testing before Firebase is set up

If you open `index.html` before filling in `DATABASE_URL`, it still works —
it just falls back to storing data only in your own browser (via
`localStorage`), so you can click around and see the UI, but it won't sync
with anyone else until the database URL is in place. A banner on the page
tells you when this fallback is active.

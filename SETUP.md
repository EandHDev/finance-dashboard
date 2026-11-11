# Finance Dashboard: Setup

Two projects, both under `~/Dev_Projects/MERN/`, each its own repo in the **Ian Aitch Developers** organization:

| Local folder | GitHub repo | Visibility | Contains |
|---|---|---|---|
| `finance-dashboard/` | `<org>/finance-dashboard` | **Public** (GitHub Pages) | `index.html`, this guide. No numbers, ever. |
| `finance-data/` | `<org>/finance-data` | **Private** | `finances.json`, `snapshots/` |

`<org>` is the organization's GitHub handle, as it appears in `github.com/<org>`.

> ⚠️ `finances.json` lives **only** in `finance-data/`. The `.gitignore` in `finance-dashboard/` blocks JSON files as a safety net, but don't copy data there.

---

## 1. Create both repos in the organization (empty)

On GitHub, go to the **Ian Aitch Developers** organization, then **Repositories → New repository**:

1. **Name:** `finance-data` → **Private** → don't add a README or .gitignore (the folders already have them) → Create.
2. **Name:** `finance-dashboard` → **Public** → same, leave it empty → Create.

## 2. Push from VS Code

Open each folder in VS Code (**File → Open Folder**), then run these in the terminal (Ctrl+`):

```bash
# in ~/Dev_Projects/MERN/finance-data
git init -b main
git add .
git commit -m "Initial data from Oct/Nov statements"
git remote add origin https://github.com/<org>/finance-data.git
git push -u origin main
```

```bash
# in ~/Dev_Projects/MERN/finance-dashboard
git init -b main
git add .
git status          # check: no .json files listed
git commit -m "Finance dashboard v1"
git remote add origin https://github.com/<org>/finance-dashboard.git
git push -u origin main
```

## 3. Turn on GitHub Pages

`<org>/finance-dashboard` → **Settings → Pages** → Source: *Deploy from a branch* → `main`, `/ (root)` → Save.
After about a minute it's live at `https://<org>.github.io/finance-dashboard/`.

## 4. Create the access token (once)

Your avatar → **Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token**

- **Name:** `finance-dashboard`
- **Resource owner:** **Ian Aitch Developers**, not your personal account
- **Expiration:** 1 year (set a calendar reminder to renew it)
- **Repository access:** *Only select repositories* → `finance-data`
- **Permissions → Repository → Contents:** **Read and write**
- Generate, then copy the `github_pat_…` value into your password manager. GitHub only shows it once.

If the organization isn't offered as a resource owner, or the token shows **Pending**: go to org **Settings → Personal access tokens → Settings**, allow fine-grained tokens, then approve the request under **Pending requests**. You're the owner, so it's one click.

## 5. Set up each device (Mac, iPad, iPhone)

1. Open `https://<org>.github.io/finance-dashboard/`.
2. **Owner:** the org handle. **Repo:** `finance-data`. Paste the token and choose a PIN. Use a different PIN from the gas station app.
3. **iPhone/iPad:** Share → **Add to Home Screen**.

The token is encrypted with your PIN and stored only in that browser. Five wrong PINs wipe it. The app locks after 10 minutes in the background.
**Lost a device?** Delete the token on GitHub, make a new one, and set up your other devices again.

---

## Day-to-day routine

### Updating data (VS Code)
1. In `finance-data/`: **`git pull` first**. The app's snapshot button commits to this repo, so your local copy can be behind.
2. Edit `finances.json` (see the `_readme` notes at the top of the file).
3. Commit and push, then tap ↻ in the app.

**When away from the Mac:** tap **Edit JSON** in the app header to edit in GitHub's web editor. Remember to `git pull` before your next edit in VS Code.

### What to change, and when
- **New card statement:** `balance`, `minPayment`, `statementDate`, `dueDate`; set `paidSinceStatement` back to `0`; set `nextClosingDate` (and `closingDateConfirmed: true` once checked in the card's app).
- **Paid a card:** lower `balance` and add the amount to `paidSinceStatement`.
- **Affirm/Klarna payment:** lower `balance` and move `nextDueDate` forward a month. Delete the loan when it reaches $0.
- **Monthly:** add your score to `scores`, set `settings.lastUpdated`, then **History → Take snapshot** in the app.

### Changing the app
1. Edit `index.html` in `finance-dashboard/` and test locally (below).
2. Commit and push. Pages redeploys in about a minute. Your data and device setups aren't affected.

### Testing locally
From `finance-dashboard/`, serve both folders together:

```bash
cd ~/Dev_Projects/MERN
python3 -m http.server 8000
```

Then open `http://localhost:8000/finance-dashboard/index.html?dev&data=../finance-data/finances.json`.
Dev mode reads the local JSON directly and only works on localhost. It never works on GitHub Pages.

**If the app shows "Couldn't calculate",** there's a JSON typo (missing comma or quote). VS Code underlines it in red.

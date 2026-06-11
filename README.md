# Above & Beyond Group Therapy 700 — Trip Dashboard

A static, single-file trip dashboard for the Big Bear 2025 group getaway.

## Deploying to GitHub Pages

1. **Create a new GitHub repository** (public or private — Pages works on both with the right plan).

2. **Push the file:**
   ```bash
   git init
   git add index.html README.md
   git commit -m "Add trip dashboard"
   git branch -M main
   git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
   git push -u origin main
   ```

3. **Enable GitHub Pages:**
   - Go to your repo on GitHub
   - Click **Settings** → **Pages** (left sidebar)
   - Under **Source**, select **Deploy from a branch**
   - Branch: `main` · Folder: `/ (root)`
   - Click **Save**

4. **Your site will be live in ~1 minute at:**
   ```
   https://YOUR_USERNAME.github.io/YOUR_REPO/
   ```

## Updating Trip Info

All data lives in the `TRIP` object at the top of `index.html`. Edit that object and push — no build step needed.

| What to update | Where |
|---|---|
| Room assignments | `rooms[].occupants` array |
| Carpool details | `carpools` array (uncomment the example) |
| Flight numbers | `flights.arrivals[].flight` / `flights.departures[].flight` |
| Schedule changes | `schedule` array |

## Custom Domain (optional)

Add a `CNAME` file to the repo root containing your domain (e.g. `bigbear2025.example.com`), then configure a CNAME DNS record pointing to `YOUR_USERNAME.github.io`. GitHub Pages will pick it up automatically.

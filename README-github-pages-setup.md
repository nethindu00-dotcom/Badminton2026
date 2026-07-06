# Deploying with GitHub Actions (Worker URL never in your source file)

## How this works
`team_registration.html` and `individual_registration.html` both contain the
literal text `__WORKER_URL__` instead of a real URL. That placeholder stays in
your repo forever — you never hand-edit it. A GitHub Actions workflow runs on
every push, copies the files into a `dist/` build folder, swaps `__WORKER_URL__`
for your real Worker URL (pulled from a GitHub secret), and publishes only that
built copy to GitHub Pages. The substituted file is never written back into
your repository — it only exists in the deployed site.

## One-time setup

1. Create a new GitHub repository (public or private, either works with Pages
   on paid plans; public repos get Pages free).

2. Push these files to the repo, keeping this exact folder structure:
   ```
   your-repo/
   ├── team_registration.html
   ├── individual_registration.html
   └── .github/
       └── workflows/
           └── deploy.yml
   ```

3. Add your Worker URL as a repository secret:
   - Repo → Settings → Secrets and variables → Actions → New repository secret
   - Name: `WORKER_URL`
   - Value: your Cloudflare Worker URL, e.g. `https://ceyline-registration-worker.yoursubdomain.workers.dev`

4. Turn on GitHub Pages via Actions:
   - Repo → Settings → Pages → under "Build and deployment", set
     **Source: GitHub Actions**

5. Push to the `main` branch (or run the workflow manually from the Actions
   tab → "Deploy registration forms" → Run workflow).

6. Once the workflow finishes, your live links will be:
   ```
   https://YOUR-USERNAME.github.io/YOUR-REPO/team_registration.html
   https://YOUR-USERNAME.github.io/YOUR-REPO/individual_registration.html
   ```
   Find the exact URL under Settings → Pages, or in the workflow's deployment
   summary.

## Checking it worked

- Open your repo on GitHub and look at `team_registration.html` in the file
  browser — it should still show `__WORKER_URL__`, never the real URL.
- Open the live GitHub Pages link and view its page source (right-click →
  View Page Source) — that copy should show your real Worker URL, since it's
  the built output, not your tracked source.

## Updating the Worker URL later

If you ever rotate or redeploy your Cloudflare Worker and get a new URL, just
update the `WORKER_URL` secret (Settings → Secrets and variables → Actions →
edit `WORKER_URL`) and re-run the workflow (push any commit, or trigger it
manually from the Actions tab). No file edits needed.

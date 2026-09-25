# Deployment Guide: GitHub & Vercel

This guide outlines how to push **Scroll World** to your own GitHub account and deploy it to **Vercel** with continuous deployment.

---

## Step 1: Push to your GitHub Repository

### 1. Create a new repository on GitHub
1. Go to [github.com/new](https://github.com/new).
2. Enter a repository name (e.g. `scroll-world` or `my-scroll-landing-page`).
3. Choose **Public** or **Private** and click **Create repository** (do not initialize with README since we already have everything).

### 2. Connect and push your local workspace
In your local workspace terminal, run:

```bash
# Check existing status
git status

# Point origin to YOUR new GitHub repo URL
git remote set-url origin https://github.com/<YOUR_USERNAME>/<YOUR_REPO_NAME>.git

# Push the main branch
git branch -M main
git push -u origin main
```

*(If you prefer starting a fresh git history, you can delete the `.git` folder, run `git init`, `git add .`, `git commit -m "Initial commit"`, and push).*

---

## Step 2: Deploy to Vercel

### Method A: Vercel Web Dashboard (Recommended)

1. Log in to [vercel.com](https://vercel.com).
2. Click **"Add New..."** -> **"Project"**.
3. Under **Import Git Repository**, select your newly pushed repository.
4. Keep the default settings:
   - **Framework Preset**: `Other` (or auto-detected)
   - **Root Directory**: `./`
   - **Build Command**: Leave default (or `npm run build`)
   - **Output Directory**: Leave empty / `./`
5. Click **"Deploy"**.
6. Within ~15-30 seconds, your site will be live at `https://<project-name>.vercel.app`!

### Method B: Vercel CLI

If you prefer deploying directly from the terminal without linking GitHub first:

```bash
# Run Vercel CLI directly via npx
npx vercel

# Follow the interactive prompts:
# ? Set up and deploy? Yes
# ? Which scope? (Select your team or personal account)
# ? Link to existing project? No
# ? What's your project's name? scroll-world
# ? In which directory is your code located? ./

# For production deployment:
npx vercel --prod
```

### Method C: One-Click Deploy Button

You can also deploy with one click by visiting:
```
https://vercel.com/new/clone?repository-url=https://github.com/<YOUR_USERNAME>/<YOUR_REPO_NAME>
```

---

## Step 3: Verifying Configuration

This repository includes a pre-configured `vercel.json`:
- **Clean URLs**: Enables clean routing without trailing slashes.
- **Aggressive Caching**: `Cache-Control: public, max-age=31536000, immutable` for static assets (`assets/scenes/*`, `.svg`, `.mp4`, `.js`).
- **CORS Headers**: Permits seamless Blob streaming and audio decoding (`Access-Control-Allow-Origin: *`).
- **Security Headers**: Includes `X-Content-Type-Options: nosniff` and `Referrer-Policy`.

---

## Step 4: Adding AI-Generated Video Flights (Optional)

When you generate custom Seedance 2.0 or Kling 3.0 clips using the Claude Code / Codex skill (`/scroll-world`):
1. Place your generated MP4 files in `assets/vid/` (e.g. `scene1.mp4`, `conn1.mp4`, etc.).
2. In `index.html`, add the `clip` and `connectors` paths to the `mountScrollWorld` configuration:
   ```javascript
   sections: [
     {
       id: 'sanctuary',
       still: 'assets/scenes/scene1.svg',
       clip: 'assets/vid/scene1.mp4', // <--- Your generated flight
       ...
     }
   ],
   connectors: [
     'assets/vid/conn1.mp4'          // <--- Seamless seam connector
   ]
   ```
3. Commit and push:
   ```bash
   git add .
   git commit -m "Add custom video flight assets"
   git push origin main
   ```
   Vercel will automatically re-deploy your site with the new video flights.

# R365 Bank Statement Automation — Vercel Deployment Guide

This guide walks you through deploying the app to Vercel so you get a custom
URL like `r365-the-park.vercel.app` — no Z.ai branding visible to end users.

---

## What you'll need

1. **A GitHub account** (free) — https://github.com/signup
2. **A Vercel account** (free) — https://vercel.com/signup
   - Sign up using your GitHub account (1-click)

---

## Step 1: Download the project

Download these folders/files from the current project to your computer:

```
src/                          ← all source code
public/                       ← static assets (if any)
package.json
package-lock.json (or bun.lockb)
tsconfig.json
next.config.ts
postcss.config.mjs
.tailwind.config (if exists)
.gitignore
README.md (this file)
```

**Skip these** (they're not needed for Vercel):
- `node_modules/` (Vercel installs dependencies fresh)
- `.next/` (build artifacts)
- `scripts/` (Python scripts — only used in the original Z.ai environment)
- `upload/`, `download/`, `skills/` (sandbox-only folders)

---

## Step 2: Create a GitHub repository

1. Go to https://github.com/new
2. **Repository name**: `r365-bank-automation`
3. **Visibility**: Private (recommended — this is your financial tool)
4. **Don't** initialize with README (you'll push the existing one)
5. Click **Create repository**

GitHub will show you instructions like:
```
git remote add origin https://github.com/YOUR_USERNAME/r365-bank-automation.git
git push -u origin main
```

---

## Step 3: Push the code to GitHub

On your computer, open a terminal in the project folder:

```bash
# Initialize git (if not already)
git init

# Add all project files
git add .

# First commit
git commit -m "Initial commit - R365 Bank Statement Automation"

# Set the main branch
git branch -M main

# Link to your GitHub repo (replace YOUR_USERNAME)
git remote add origin https://github.com/YOUR_USERNAME/r365-bank-automation.git

# Push
git push -u origin main
```

You'll be asked for your GitHub username and password. Use a **Personal Access
Token** instead of your password (GitHub requires this):
- Go to https://github.com/settings/tokens
- Generate new token (classic) → check "repo" scope
- Copy the token and paste it as your password

---

## Step 4: Deploy to Vercel

1. Go to https://vercel.com/new
2. Click **Import Git Repository**
3. Find your `r365-bank-automation` repo and click **Import**
4. Vercel auto-detects Next.js — keep the default settings:
   - **Framework Preset**: Next.js
   - **Build Command**: `next build` (default)
   - **Output Directory**: `.next` (default)
   - **Install Command**: `npm install` (default)
5. Click **Deploy**

Vercel will build and deploy your app (takes about 1-2 minutes).

---

## Step 5: Get your custom URL

After deployment, Vercel gives you a URL like:
```
https://r365-bank-automation-abc123-your-username.vercel.app
```

To get a cleaner URL:

1. Go to your project dashboard on Vercel
2. Click **Settings** → **Domains**
3. Under "Vercel Domains", click **Edit** on the auto-generated domain
4. Change it to something like `r365-the-park` → your URL becomes:
   ```
   https://r365-the-park.vercel.app
   ```

---

## Optional: Use your own domain

If you own a custom domain (like `yourcompany.com`):

1. In Vercel → Settings → Domains → **Add Domain**
2. Enter `automation.yourcompany.com` (or whatever subdomain you want)
3. Vercel shows you DNS records to add at your domain registrar
   (Namecheap, GoDaddy, etc.)
4. Add the DNS records, wait 5-30 minutes for propagation
5. Vercel auto-provisions SSL certificate

---

## Verify deployment

1. Visit your Vercel URL
2. You should see the landing page with the calculator image
3. Click **Drop your Truist PDF here** and upload a statement
4. Within 2 seconds, you should see the classified results

---

## Updating the app

Whenever you want to update the app (e.g., add new pattern rules):

1. Edit `src/data/pattern_mapping.json` on your computer
2. Push to GitHub:
   ```bash
   git add src/data/pattern_mapping.json
   git commit -m "Add new vendor pattern"
   git push
   ```
3. Vercel auto-redeploys within ~1 minute

---

## Troubleshooting

### "Build failed" on Vercel
- Check the Vercel build logs
- Common cause: missing dependency. Make sure `unpdf` is in `package.json`
  dependencies.

### "Function timeout" when uploading large PDFs
- Vercel's free tier has a 10-second timeout on serverless functions
- The Truist statements (5-10 pages) process in 2-3 seconds, well within the limit
- If you hit this, upgrade to Vercel Pro ($20/month) for 60-second timeouts

### PDF doesn't parse correctly
- The parser is tuned for Truist statement format
- If Truist changes their statement layout, you may need to update
  `src/lib/pdfParser.ts`

### Custom domain not working
- DNS propagation can take up to 30 minutes
- Use https://dnschecker.org to verify DNS is propagating

---

## Files structure (after deployment)

```
r365-bank-automation/
├── src/
│   ├── app/
│   │   ├── api/upload/route.ts   ← PDF upload + classification endpoint
│   │   ├── layout.tsx            ← Root layout (fonts, metadata)
│   │   ├── page.tsx              ← Landing page + Dashboard UI
│   │   └── globals.css           ← Tailwind + custom styles
│   ├── data/
│   │   └── pattern_mapping.json  ← 42 classification rules (editable)
│   └── lib/
│       ├── pdfParser.ts          ← Truist PDF → transactions (uses unpdf)
│       └── classifier.ts         ← Transactions → R365 types + GL accounts
├── package.json
├── tsconfig.json
├── next.config.ts
└── README.md (this file)
```

---

## Cost

- **GitHub**: Free for private repos
- **Vercel**: Free tier includes:
  - 100 GB bandwidth/month
  - 100 GB-hours of serverless function execution
  - Unlimited deployments
  - Custom subdomain (`yourname.vercel.app`)

For a single-user monthly bank reconciliation tool, you'll never exceed the
free tier.

---

## Support

The app is self-contained — no external API keys or services needed. Once
deployed, it runs forever without any ongoing maintenance.

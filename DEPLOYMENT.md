# Deploying prt-circle-revenue-recovery-engine.html to Cloudflare Pages

This is kept as a separate internal document on purpose — deployment and DNS steps don't belong on a public sales page.

## Current state

The site is a single static HTML file with no build step, no server, and no API keys anywhere in the code. The POC form is client-side only (shows a success state on submit, doesn't send data anywhere yet).

## 1. Push to a Git repo

Cloudflare Pages deploys from GitHub or GitLab.

```
mkdir prt-circle-site && cd prt-circle-site
cp prt-circle-revenue-recovery-engine.html index.html
git init
git add index.html
git commit -m "Initial site"
git remote add origin <your-repo-url>
git push -u origin main
```

## 2. Connect Cloudflare Pages

1. Cloudflare dashboard → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**
2. Select the repo
3. Build settings:
   - **Framework preset:** None
   - **Build command:** *(leave empty — there's no build step)*
   - **Build output directory:** `/` (the folder containing `index.html`)
4. Deploy

Cloudflare gives you a `*.pages.dev` URL immediately after the first deploy.

## 3. Custom domain (prtcircle.com)

1. In the Pages project → **Custom domains** → **Set up a custom domain**
2. Enter `prtcircle.com` (and `www.prtcircle.com` if you want both)
3. If your domain's nameservers are already on Cloudflare, the DNS record is added automatically
4. If not, Cloudflare gives you a CNAME record to add at your current registrar:
   ```
   Type: CNAME
   Name: @  (or www)
   Target: <your-project>.pages.dev
   ```

## 4. HTTPS

Cloudflare Pages provisions SSL automatically once the domain is verified — usually within a few minutes, occasionally up to 24 hours on first setup. No action needed beyond adding the domain.

## 5. When you wire the POC form to a real backend

The form currently only shows a success message — it doesn't send data anywhere. When you're ready to actually collect submissions, the standard approach is a **Cloudflare Pages Function** (serverless, lives in the same repo):

```
/functions/api/poc-submit.js
```

That function is where any API key or secret goes (e.g., to forward the submission into your Make.com webhook, or straight into Notion). Set secrets under **Pages project → Settings → Environment variables** — never in the HTML/JS itself, since anything in the frontend is publicly visible.

A simple option that needs no custom backend at all: point the form directly at your existing Make.com webhook URL and let Make write the submission into Notion, the same way your lead-intake scenario already works.

## Production checklist

- [ ] Domain renewed and pointed at Cloudflare
- [ ] HTTPS shows as active (padlock in browser)
- [ ] Form actually delivers submissions somewhere (currently: nowhere)
- [ ] Real Loom/demo video embedded (currently: placeholder frame)
- [ ] Real brand logo swapped in (currently: text/ring monogram)
- [ ] Market Intelligence section either populated with a sourced, dated figure or left as the "ready for live data" placeholder
- [ ] WhatsApp floating button points at a real number if you want live chat instead of scrolling to the form

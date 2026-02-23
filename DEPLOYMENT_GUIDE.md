# Typebot Self-Hosting Deployment Guide for Polanco Advertising

This guide will walk you through deploying Typebot on Vercel (free tier) for polancoadvertising.com.

## Prerequisites

- ✅ Supabase PostgreSQL database (already set up)
- ✅ Forked typebot.io repository
- ⬜ Vercel account (free tier)
- ⬜ GitHub account (for Vercel integration)

---

## Database Configuration

Your Supabase database URL:
```
postgresql://postgres:Great25fire!!@db.ekrcctblmixdrfthugjv.supabase.co:5432/postgres
```

> ⚠️ **Important**: Keep this URL private! Never commit it to a public repository.

---

## Step 1: Deploy the Builder

The Builder is the admin interface where you create and manage your chatbots.

### 1.1 Create Vercel Project

1. Go to [vercel.com](https://vercel.com) and sign in
2. Click **"Add New..."** → **"Project"**
3. Import your forked `typebot.io` repository
4. Configure the project:

| Setting | Value |
|---------|-------|
| **Project Name** | `typebot-builder` (or your preferred name) |
| **Framework Preset** | Next.js |
| **Root Directory** | `apps/builder` |
| **Build Command** | `bunx turbo build && bunx turbo db:migrate --filter=@typebot.io/prisma` |
| **Output Directory** | (leave default) |

### 1.2 Add Environment Variables

Add these environment variables in Vercel (Settings → Environment Variables):

| Variable | Value | Description |
|----------|-------|-------------|
| `DATABASE_URL` | `postgresql://postgres:Great25fire!!@db.ekrcctblmixdrfthugjv.supabase.co:5432/postgres` | PostgreSQL connection string |
| `ENCRYPTION_SECRET` | `polanco_adv_2024_secure_key` | **Exactly 32 characters or less!** |
| `NEXTAUTH_URL` | `https://your-builder-url.vercel.app` | Builder's public URL (for auth) |
| `NEXT_PUBLIC_VIEWER_URL` | `https://your-viewer-url.vercel.app` | Viewer's public URL |
| `ADMIN_EMAIL` | `your-email@example.com` | Your email for unlimited plan access |

> 💡 **Tip**: Generate a secure `ENCRYPTION_SECRET` using: `openssl rand -base64 32`
5ZfXFmrfhUcjPDKgGSs+zRAe4SQzuI/OzbIldXWfzxo=

### 1.3 Deploy

Click **"Deploy"** and wait for the build to complete (may take 5-10 minutes).

---

## Step 2: Deploy the Viewer

The Viewer is the public-facing bot interface that your website visitors will interact with.

### 2.1 Create Vercel Project

1. Click **"Add New..."** → **"Project"**
2. Select the same forked `typebot.io` repository
3. Configure the project:

| Setting | Value |
|---------|-------|
| **Project Name** | `typebot-viewer` (or your preferred name) |
| **Framework Preset** | Next.js |
| **Root Directory** | `apps/viewer` |
| **Build Command** | `bunx turbo build && bunx turbo db:migrate --filter=@typebot.io/prisma` |
| **Output Directory** | (leave default) |

### 2.2 Add Environment Variables

These environment variables are similar to the Builder, but **do NOT include ADMIN_EMAIL**:

| Variable | Value | Notes |
|----------|-------|-------|
| `DATABASE_URL` | `postgresql://postgres:Great25fire!!@db.ekrcctblmixdrfthugjv.supabase.co:5432/postgres` | Same as builder |
| `ENCRYPTION_SECRET` | `polanco_adv_2024_secure_key` | **Must match builder exactly!** |
| `NEXTAUTH_URL` | `https://your-builder-url.vercel.app` | Points to **builder** URL (for auth callbacks) |
| `NEXT_PUBLIC_VIEWER_URL` | `https://your-viewer-url.vercel.app` | Points to **this** viewer URL |

> ⚠️ **Note**: Do NOT add `ADMIN_EMAIL` to the viewer - it's only needed on the builder.

### 2.3 Deploy

Click **"Deploy"** and wait for the build to complete.

---

## Step 3: Create Your First Bot

### 3.1 Access the Builder

1. Go to your builder URL: `https://your-builder-url.vercel.app`
2. Create an account using the email you set as `ADMIN_EMAIL`
3. You'll automatically get an unlimited plan

### 3.2 Import the Polanco Support Template

1. In the builder, click **"Create a typebot"**
2. Click **"Import a file"**
3. Upload the `polanco-support.json` file from `apps/builder/public/templates/`
4. Customize the bot content, branding, and flow as needed

### 3.3 Configure Bot Settings

1. Go to the **Settings** tab
2. Set a **Public ID** (e.g., `polanco-support`)
3. Under **General**, ensure **"Display Typebot branding"** is OFF
4. Customize theme colors to match your brand

### 3.4 Publish

1. Click **"Publish"** in the top right
2. Your bot is now live!

---

## Step 4: Embed the Widget on Your Website

### Option A: Bubble Widget (Recommended)

Add this code to your website's `<head>` or before `</body>`:

```html
<script type="module">
  import Typebot from 'https://cdn.jsdelivr.net/npm/@typebot.io/js@0.9/dist/web.js'

  Typebot.initBubble({
    typebot: 'polanco-support', // Your bot's Public ID
    apiHost: 'https://your-viewer-url.vercel.app',
    previewMessage: {
      message: '👋 Hi! Need help with advertising?',
      autoShowDelay: 3000,
      avatarUrl: 'https://polancoadvertising.com/logo.png'
    },
    theme: {
      button: {
        backgroundColor: '#YOUR_BRAND_COLOR',
        size: 'large',
        iconColor: '#FFFFFF'
      },
      previewMessage: {
        backgroundColor: '#FFFFFF',
        textColor: '#000000',
        buttonBackgroundColor: '#YOUR_BRAND_COLOR',
        buttonIconColor: '#FFFFFF'
      },
      chatWindow: {
        backgroundColor: '#FFFFFF'
      }
    }
  })
</script>
```

### Option B: Popup Widget

```html
<script type="module">
  import Typebot from 'https://cdn.jsdelivr.net/npm/@typebot.io/js@0.9/dist/web.js'

  Typebot.initPopup({
    typebot: 'polanco-support',
    apiHost: 'https://your-viewer-url.vercel.app',
    autoShowDelay: 5000, // Show after 5 seconds
    theme: {
      backgroundColor: '#FFFFFF'
    }
  })
</script>
```

### Option C: Inline Embed

```html
<typebot-standard
  style="width: 100%; height: 600px;"
  typebot="polanco-support"
  api-host="https://your-viewer-url.vercel.app"
></typebot-standard>
<script type="module">
  import '@typebot.io/js'
</script>
```

---

## Step 5: Customize for Your Brand

### Theme Colors

Update the embed code with your brand colors:

```javascript
theme: {
  button: {
    backgroundColor: '#FF6B35', // Replace with your brand color
  },
  chatWindow: {
    backgroundColor: '#FFFFFF',
  }
}
```

### Bot Avatar

1. In the builder, go to **Theme** → **Chat**
2. Enable **Host Avatar**
3. Upload your logo or set the URL

### Custom Domain (Optional)

To use a custom domain like `bot.polancoadvertising.com`:

1. Go to Vercel project settings
2. Add your custom domain
3. Update `NEXT_PUBLIC_VIEWER_URL` to the new domain

---

## Troubleshooting

### Build Fails

- Check that `DATABASE_URL` is correct
- Verify `ENCRYPTION_SECRET` is at least 32 characters
- Check Vercel build logs for specific errors

### Bot Not Loading

- Verify `NEXT_PUBLIC_VIEWER_URL` matches your viewer URL
- Check that the bot is published (not just saved)
- Ensure the Public ID in the embed matches your bot's Public ID

### Database Connection Issues

- Verify Supabase database is running
- Check that your IP is not blocked (Supabase Dashboard → Settings → Database)
- Ensure connection string has correct password

---

## Costs (All Free Tier)

| Service | Plan | Cost |
|---------|------|------|
| Supabase | Free | $0 (500MB database) |
| Vercel Builder | Hobby | $0 |
| Vercel Viewer | Hobby | $0 |
| **Total** | | **$0/month** |

---

## Next Steps

1. ✅ Deploy Builder to Vercel
2. ✅ Deploy Viewer to Vercel
3. ✅ Create account and import bot template
4. ✅ Customize bot content and branding
5. ✅ Add embed code to polancoadvertising.com
6. ✅ Test the chatbot on your website

---

## Support

- Typebot Discord: [discord.typebot.io](https://discord.typebot.io)
- Documentation: [docs.typebot.io](https://docs.typebot.io)
- GitHub Issues: [github.com/baptisteArno/typebot.io](https://github.com/baptisteArno/typebot.io)

---

## Files Modified

1. `apps/viewer/vercel.json` - Added `maxDuration: 10` for hobby plan compatibility
2. `apps/builder/public/templates/polanco-support.json` - Custom support bot template

## Quick Reference URLs

After deployment, replace these placeholders:

- `{BUILDER_URL}` → Your builder Vercel URL
- `{VIEWER_URL}` → Your viewer Vercel URL
- `{PUBLIC_ID}` → Your bot's Public ID (set in settings)
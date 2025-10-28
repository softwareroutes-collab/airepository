# AIverse - Quick Start Guide

## ✅ What's Already Complete

I've built and deployed the complete AIverse platform:

### Backend (Supabase - Fully Deployed)
- ✅ 12 database tables with Row Level Security policies
- ✅ 8 Edge Functions deployed and operational
- ✅ 3 storage buckets (tool-logos, tool-screenshots, tutorial-videos)
- ✅ Daily cron job for automated scraping (runs at 2 AM UTC)
- ✅ Seed data: 10 categories, 5 sample AI tools
- ✅ Supabase URL: https://kxcfdfsdpribtjlyupmf.supabase.co

### Frontend (Next.js 14 - Code Complete)
- ✅ All 15+ pages built (homepage, tools, admin panel, dashboard, etc.)
- ✅ Authentication system with email/OAuth support
- ✅ Admin portal with 5 management sections
- ✅ User dashboard with favorites
- ✅ Responsive design with modern UI
- ✅ Git repository initialized and committed

---

## 🚀 Next Steps - Deploy to Vercel

### Step 1: Create GitHub Repository

1. Go to https://github.com/new
2. Create a new repository named `aiverse` (or your preferred name)
3. **Do NOT initialize** with README (we already have code)
4. Copy the repository URL (e.g., `https://github.com/YOUR_USERNAME/aiverse.git`)

### Step 2: Push Code to GitHub

Run these commands in your terminal:

```bash
cd /workspace/aiverse
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/aiverse.git
git push -u origin main
```

Replace `YOUR_USERNAME` with your actual GitHub username.

### Step 3: Deploy to Vercel

1. **Sign in to Vercel**: https://vercel.com/login
2. **Import Project**: https://vercel.com/new
3. **Select your GitHub repository** (aiverse)
4. **Configure Project Settings**:
   - Framework Preset: **Next.js** (auto-detected)
   - Root Directory: `./`
   - Build Command: `next build` (default)
   - Install Command: `pnpm install`

5. **Add Environment Variables** (CRITICAL - Required for app to work):
   ```
   NEXT_PUBLIC_SUPABASE_URL=https://kxcfdfsdpribtjlyupmf.supabase.co
   NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imt4Y2ZkZnNkcHJpYnRqbHl1cG1mIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NjE2MTMyMzEsImV4cCI6MjA3NzE4OTIzMX0.PPOyU-BAu_eR1AnX6ScGgc_TF-cOX3SuF_QBFBQJAu8
   NEXT_PUBLIC_SITE_URL=https://your-project.vercel.app
   ```

6. **Click "Deploy"** and wait 2-3 minutes

---

## 🔧 Post-Deployment Configuration

### After Your Site is Live

Vercel will give you a URL like: `https://aiverse-xyz.vercel.app`

#### 1. Update Site URL
1. Go to Vercel Project → Settings → Environment Variables
2. Edit `NEXT_PUBLIC_SITE_URL` to your actual deployed URL
3. Redeploy (Vercel → Deployments → click "..." → Redeploy)

#### 2. Get API Keys

To enable full functionality, you'll need:

**A. Stripe API Keys** (for payment processing):
- Sign up at https://dashboard.stripe.com
- Go to Developers → API Keys
- Copy your **Secret Key** (starts with `sk_test_` or `sk_live_`)

**B. OpenRouter API Key** (for AI features):
- Sign up at https://openrouter.ai
- Go to Keys: https://openrouter.ai/keys
- Create a new API key

**C. Add Stripe Webhook** (for subscription handling):
- In Stripe Dashboard → Developers → Webhooks
- Click "Add endpoint"
- Endpoint URL: `https://kxcfdfsdpribtjlyupmf.supabase.co/functions/v1/stripe-webhooks`
- Events to listen to:
  - `customer.subscription.created`
  - `customer.subscription.updated`
  - `customer.subscription.deleted`
  - `checkout.session.completed`
- Copy the **Webhook Signing Secret** (starts with `whsec_`)

#### 3. Configure API Keys in Database

Once you have the keys, run this in Supabase SQL Editor (https://app.supabase.com/project/kxcfdfsdpribtjlyupmf/sql):

```sql
-- Add OpenRouter API Key
UPDATE openrouter_settings 
SET 
  api_key = 'YOUR_OPENROUTER_API_KEY_HERE',
  is_active = true,
  updated_at = NOW()
WHERE id = (SELECT id FROM openrouter_settings LIMIT 1);

-- Add Stripe Secret Key (for edge functions)
-- You'll need to add this as an edge function secret via Supabase CLI or dashboard
```

For Stripe, add the secret in Supabase Dashboard:
1. Go to Edge Functions → Secrets
2. Add secret: `STRIPE_SECRET_KEY` = your Stripe secret key
3. Redeploy affected functions

---

## 🎯 Create Your First Admin Account

1. Go to your deployed site: `https://your-project.vercel.app/signup`
2. Sign up with your email
3. In Supabase SQL Editor, promote yourself to admin:

```sql
UPDATE user_profiles 
SET role = 'admin' 
WHERE id = (SELECT id FROM auth.users WHERE email = 'your-email@example.com');
```

4. Now you can access the admin panel at: `/admin`

---

## ✨ What You Can Do Now

### Public Features (No Login Required)
- Browse AI tools at `/tools`
- Search and filter tools
- View tool details
- See categories

### User Features (After Signup)
- Save favorite tools (`/dashboard/favorites`)
- Get AI-powered recommendations
- View your dashboard (`/dashboard`)

### Admin Features (After Admin Promotion)
- Manage tools (`/admin/tools`)
- Configure OpenRouter AI (`/admin/openrouter`)
- Control scraping automation (`/admin/scraping`)
- View analytics (`/admin/analytics`)
- Admin dashboard (`/admin`)

---

## 📊 Monitoring & Management

### Supabase Dashboard
- **Database Tables**: https://app.supabase.com/project/kxcfdfsdpribtjlyupmf/editor
- **Edge Functions**: https://app.supabase.com/project/kxcfdfsdpribtjlyupmf/functions
- **Storage**: https://app.supabase.com/project/kxcfdfsdpribtjlyupmf/storage/buckets
- **Authentication**: https://app.supabase.com/project/kxcfdfsdpribtjlyupmf/auth/users

### Vercel Dashboard
- **Deployments & Logs**: Check after deployment
- **Analytics**: Built-in traffic monitoring

---

## 🆘 Need Help?

### Common Issues

**Issue**: Build fails on Vercel
- **Fix**: Ensure environment variables are set correctly
- **Fix**: Check build logs for specific errors

**Issue**: Can't log in
- **Fix**: Verify Supabase URL and Anon Key in environment variables
- **Fix**: Check browser console for errors

**Issue**: Admin panel shows "Access Denied"
- **Fix**: Run the SQL query to promote your account to admin role

**Issue**: Stripe payments not working
- **Fix**: Ensure STRIPE_SECRET_KEY is added to Edge Functions secrets
- **Fix**: Verify webhook endpoint is configured in Stripe dashboard

### Documentation
- Full deployment guide: See `DEPLOYMENT.md`
- API integration guide: See `docs/REAL_API_INTEGRATION.md`
- Project overview: See `README.md`

---

## 📋 Checklist

After deployment, verify:

- [ ] Site loads at your Vercel URL
- [ ] Homepage shows categories and sample tools
- [ ] Search works on `/tools` page
- [ ] Can create an account at `/signup`
- [ ] Can log in at `/login`
- [ ] Admin account created and can access `/admin`
- [ ] OpenRouter API key configured
- [ ] Stripe API key configured (if using payments)
- [ ] Stripe webhook endpoint added

---

## 🎉 You're All Set!

Your AIverse platform is now live and ready to discover and showcase AI tools automatically!

**Next recommended actions**:
1. Add more tools manually via admin panel
2. Configure OpenRouter for AI-powered features
3. Set up Stripe for monetization
4. Enable OAuth (Google/GitHub) for easier login
5. Monitor the daily cron job (runs at 2 AM UTC)

For detailed guides, see:
- `DEPLOYMENT.md` - Complete deployment documentation
- `README.md` - Full feature overview
- `docs/REAL_API_INTEGRATION.md` - Connect real APIs for scraping

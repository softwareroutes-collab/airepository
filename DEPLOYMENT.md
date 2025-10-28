# AIverse Deployment Guide

## Backend - Supabase (ALREADY DEPLOYED ✅)

The Supabase backend is fully configured and operational:

### Database
- **Status**: ✅ Deployed and seeded
- **Tables**: 12 tables with RLS policies
- **URL**: https://kxcfdfsdpribtjlyupmf.supabase.co
- **Data**: 10 categories, 5 sample tools loaded

### Edge Functions
All 6 functions deployed and accessible:

1. **openrouter-proxy**: `https://kxcfdfsdpribtjlyupmf.supabase.co/functions/v1/openrouter-proxy`
2. **tool-operations**: `https://kxcfdfsdpribtjlyupmf.supabase.co/functions/v1/tool-operations`
3. **search-autocomplete**: `https://kxcfdfsdpribtjlyupmf.supabase.co/functions/v1/search-autocomplete`
4. **analytics-tracker**: `https://kxcfdfsdpribtjlyupmf.supabase.co/functions/v1/analytics-tracker`
5. **get-recommendations**: `https://kxcfdfsdpribtjlyupmf.supabase.co/functions/v1/get-recommendations`
6. **scrape-tools**: `https://kxcfdfsdpribtjlyupmf.supabase.co/functions/v1/scrape-tools`

### Cron Job
- **Status**: ✅ Active
- **Schedule**: Daily at 2:00 AM UTC
- **Function**: Calls scrape-tools for automated AI tool discovery

### Storage Buckets
- **tool-logos**: Public, 5MB limit
- **tool-screenshots**: Public, 10MB limit
- **tutorial-videos**: Public, 50MB limit

---

## Frontend - Next.js (READY FOR DEPLOYMENT)

### Prerequisites
1. GitHub account
2. Vercel account (https://vercel.com)

### Step-by-Step Deployment

#### 1. Push to GitHub

```bash
cd /workspace/aiverse
git init
git add .
git commit -m "Initial commit - AIverse platform"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/aiverse.git
git push -u origin main
```

#### 2. Deploy to Vercel

1. Go to https://vercel.com/new
2. Import your GitHub repository
3. Configure project:
   - **Framework Preset**: Next.js
   - **Root Directory**: ./
   - **Build Command**: `next build`
   - **Output Directory**: .next
   - **Install Command**: `pnpm install`

4. **Add Environment Variables**:
   ```
   NEXT_PUBLIC_SUPABASE_URL=https://kxcfdfsdpribtjlyupmf.supabase.co
   NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imt4Y2ZkZnNkcHJpYnRqbHl1cG1mIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NjE2MTMyMzEsImV4cCI6MjA3NzE4OTIzMX0.PPOyU-BAu_eR1AnX6ScGgc_TF-cOX3SuF_QBFBQJAu8
   NEXT_PUBLIC_SITE_URL=https://your-project.vercel.app
   ```

5. Click "Deploy"

#### 3. Post-Deployment Configuration

After deployment, update `NEXT_PUBLIC_SITE_URL` with your actual Vercel URL:

1. Go to Vercel Project Settings → Environment Variables
2. Update `NEXT_PUBLIC_SITE_URL` with your deployed URL
3. Redeploy

---

## Admin Configuration

### 1. Create First Admin User

Option A: Sign up through UI (first user can be manually promoted)

Option B: Direct SQL (in Supabase SQL Editor):
```sql
-- Sign up a user first through the UI, then promote:
UPDATE user_profiles 
SET role = 'admin' 
WHERE id = (SELECT id FROM auth.users WHERE email = 'your-email@example.com');
```

### 2. Configure OpenRouter API

1. Sign up at https://openrouter.ai
2. Get API key
3. In Supabase SQL Editor:
   ```sql
   UPDATE openrouter_settings 
   SET api_key = 'YOUR_OPENROUTER_API_KEY_HERE', 
       is_active = true 
   WHERE id = (SELECT id FROM openrouter_settings LIMIT 1);
   ```

Or create admin UI page to manage this.

### 3. Enable OAuth Providers (Optional)

#### Google OAuth
1. Go to https://console.cloud.google.com
2. Create OAuth 2.0 credentials
3. In Supabase Dashboard → Authentication → Providers → Google:
   - Enable provider
   - Add Client ID and Secret
   - Authorized redirect URI: `https://kxcfdfsdpribtjlyupmf.supabase.co/auth/v1/callback`

#### GitHub OAuth
1. Go to https://github.com/settings/developers
2. Create new OAuth App
3. In Supabase Dashboard → Authentication → Providers → GitHub:
   - Enable provider
   - Add Client ID and Secret
   - Callback URL: `https://kxcfdfsdpribtjlyupmf.supabase.co/auth/v1/callback`

### 4. Configure Stripe (Optional)

1. Get API keys from https://stripe.com
2. Add to environment variables or admin configuration
3. Create webhook endpoint for subscription events

---

## Testing Checklist

After deployment, test:

- [ ] Homepage loads with categories and tools
- [ ] Search functionality works
- [ ] User registration/login
- [ ] Tool detail pages
- [ ] Admin login (if configured)
- [ ] Edge Functions respond correctly
- [ ] Cron job runs (check scraping_logs table next day)

---

## Monitoring

### Supabase Dashboard
- **Database**: https://app.supabase.com/project/kxcfdfsdpribtjlyupmf/editor
- **Edge Functions Logs**: https://app.supabase.com/project/kxcfdfsdpribtjlyupmf/functions
- **Auth Users**: https://app.supabase.com/project/kxcfdfsdpribtjlyupmf/auth/users
- **Storage**: https://app.supabase.com/project/kxcfdfsdpribtjlyupmf/storage/buckets

### Vercel Dashboard
- **Deployments**: https://vercel.com/YOUR_USERNAME/aiverse
- **Analytics**: Available in Vercel dashboard
- **Logs**: Real-time logs for debugging

---

## Troubleshooting

### Issue: OAuth not working
**Solution**: Verify callback URLs in both provider settings and Supabase dashboard match exactly.

### Issue: Edge Functions returning errors
**Solution**: Check logs in Supabase Dashboard → Functions → Select function → Logs

### Issue: No tools showing
**Solution**: Verify RLS policies allow public read:
```sql
SELECT * FROM tools WHERE status = 'approved';
```

### Issue: Cron job not running
**Solution**: Check pg_cron status:
```sql
SELECT * FROM cron.job;
SELECT * FROM scraping_logs ORDER BY run_at DESC;
```

---

## Custom Domain (Optional)

### Add to Vercel
1. Go to Project Settings → Domains
2. Add your domain
3. Configure DNS records as instructed

### Update Environment Variables
Update `NEXT_PUBLIC_SITE_URL` to your custom domain and redeploy.

---

## Scaling Considerations

### Database
- Supabase Free tier: 500MB database, 2GB bandwidth
- Upgrade to Pro for production: $25/month

### Edge Functions
- Free tier: 500K invocations/month
- Monitor usage in Supabase dashboard

### Vercel
- Hobby tier: Free for personal projects
- Pro tier: $20/month for commercial use

---

## Support

- **Supabase Docs**: https://supabase.com/docs
- **Next.js Docs**: https://nextjs.org/docs
- **Vercel Support**: https://vercel.com/support

---

**Note**: The application is ready for deployment. Follow these steps to go live!

# 🚀 Supabase Quick Start Guide

## What is Supabase?
Supabase is a **free** open-source alternative to Firebase. It gives you:
- ✅ PostgreSQL database (more powerful than MySQL)
- ✅ Built-in authentication
- ✅ File storage
- ✅ Real-time updates
- ✅ Auto-generated APIs
- ✅ Free 500MB database

---

## 📝 Step-by-Step Setup (5 Minutes)

### Step 1: Create Account
1. Go to: **https://supabase.com**
2. Click "Start your project"
3. Sign up with Google/GitHub (fastest)

### Step 2: Create Project
1. Click "New Project"
2. Fill in:
   - **Organization**: Create new or select existing
   - **Name**: `Freelary` (or any name)
   - **Database Password**: Choose strong password ⚠️ **SAVE THIS!**
   - **Region**: Select closest to you
   - **Plan**: Free (perfect for starting)
3. Click "Create new project"
4. ⏰ Wait 2-3 minutes while Supabase sets up your database

### Step 3: Get Your Keys
1. In your project dashboard, look at the left sidebar
2. Click ⚙️ **Settings**
3. Click **API** in the submenu
4. You'll see three important things:

```
Project URL: https://xxxxxxxxxxxxx.supabase.co
API Keys:
  - anon/public: eyJhbGci... (copy this)
  - service_role: eyJhbGci... (copy this too)
```

### Step 4: Update Your .env File
1. Open `backend/.env` in your code editor
2. Find these lines:
   ```env
   SUPABASE_URL=your_supabase_project_url_here
   SUPABASE_ANON_KEY=your_supabase_anon_key_here
   SUPABASE_SERVICE_ROLE_KEY=your_supabase_service_role_key_here
   ```
3. Replace with your actual values:
   ```env
   SUPABASE_URL=https://xxxxxxxxxxxxx.supabase.co
   SUPABASE_ANON_KEY=eyJhbGci...your_anon_key...
   SUPABASE_SERVICE_ROLE_KEY=eyJhbGci...your_service_role_key...
   ```
4. Make sure this line says:
   ```env
   DATABASE_TYPE=supabase
   ```

### Step 5: Create Database Tables
1. In Supabase dashboard, click 🗄️ **SQL Editor** (left sidebar)
2. Click "+ New query"
3. Open the file `backend/database/supabase-schema.sql` in your code
4. Copy ALL the content (Ctrl+A, Ctrl+C)
5. Paste it in the SQL Editor
6. Click "Run" (or press Ctrl+Enter)
7. ✅ You should see "Success" message

### Step 6: Create Storage Buckets (Optional - for file uploads)
1. Click 🗂️ **Storage** (left sidebar)
2. Click "New bucket"
3. Create these buckets:
   - Name: `avatars`, Public: ✅ (for profile pictures)
   - Name: `projects`, Public: ❌ (for project files)
   - Name: `uploads`, Public: ❌ (for general files)

### Step 7: Start Your Server
```bash
cd backend
npm run dev
```

You should see:
```
✅ Supabase Connected Successfully
📊 Database URL: https://xxxxx.supabase.co
🚀 Freelary Server running on port 5000
```

### Step 8: Test It!
Open browser and go to:
```
http://localhost:5000/health
```

You should see:
```json
{
  "status": "OK",
  "database": "Connected",
  "databaseType": "supabase"
}
```

---

## 🎉 Congratulations!

Your website is now connected to Supabase!

---

## 🔍 What Just Happened?

```
┌─────────────────┐
│   Your Website  │
│   (Frontend)    │
└────────┬────────┘
         │
         ↓
┌─────────────────┐
│  Node.js Server │ ← You're here
│   (Backend)     │
└────────┬────────┘
         │
         ↓
┌─────────────────┐
│    Supabase     │
│  (PostgreSQL)   │
│   + Storage     │
│   + Auth        │
└─────────────────┘
```

---

## 💡 Important URLs

| What | URL |
|------|-----|
| Supabase Dashboard | https://app.supabase.com |
| Your Project | https://app.supabase.com/project/YOUR_PROJECT_ID |
| SQL Editor | https://app.supabase.com/project/YOUR_PROJECT_ID/editor |
| Table Editor | https://app.supabase.com/project/YOUR_PROJECT_ID/editor |
| Storage | https://app.supabase.com/project/YOUR_PROJECT_ID/storage/buckets |
| API Docs | https://app.supabase.com/project/YOUR_PROJECT_ID/api |

---

## 🔐 Security Tips

⚠️ **NEVER** share your `SUPABASE_SERVICE_ROLE_KEY` publicly!
- ✅ Keep it in `.env` file only
- ✅ Add `.env` to `.gitignore`
- ❌ Don't commit it to GitHub
- ❌ Don't use it in frontend code

---

## 🆘 Common Issues

### "Connection Failed"
**Problem**: Can't connect to Supabase
**Fix**: 
- Check internet connection
- Verify SUPABASE_URL is correct (starts with `https://`)
- Make sure project is not paused (free tier pauses after 7 days inactivity)

### "Table doesn't exist"
**Problem**: Getting errors about missing tables
**Fix**:
- Run the SQL schema in SQL Editor
- Check if tables were created in Table Editor

### "Invalid API key"
**Problem**: Authentication errors
**Fix**:
- Copy keys again from Settings → API
- Make sure no extra spaces in .env file
- Restart your server after updating .env

### ".env not working"
**Problem**: Changes to .env not taking effect
**Fix**:
- Stop server (Ctrl+C)
- Start again (`npm run dev`)
- Make sure .env is in backend folder

---

## 📱 Using in Your Code

### Example: Create a user
```javascript
const SupabaseHelper = require('./utils/supabaseHelper');

const newUser = await SupabaseHelper.create('users', {
  email: 'john@example.com',
  name: 'John Doe',
  role: 'freelancer'
});
```

### Example: Get all jobs
```javascript
const jobs = await SupabaseHelper.findMany('jobs', {
  filter: { status: 'open' },
  limit: 10,
  orderBy: { column: 'created_at', ascending: false }
});
```

### Example: Upload file
```javascript
await SupabaseHelper.uploadFile(
  'avatars', 
  'user-123/avatar.png', 
  fileBuffer
);
```

---

## 🎓 Learn More

- 📚 [Full Setup Guide](./SUPABASE_SETUP_GUIDE.md) - Detailed documentation
- 📝 [Migration Summary](./SUPABASE_MIGRATION_SUMMARY.md) - What changed in your code
- 💻 [Example Controller](./backend/controllers/supabaseExampleController.js) - Code examples
- 🗄️ [Database Schema](./backend/database/supabase-schema.sql) - SQL tables

---

## ✨ What's Next?

1. ✅ Supabase is connected
2. ⏭️ Start building features
3. ⏭️ Add authentication
4. ⏭️ Upload files to storage
5. ⏭️ Use real-time features
6. ⏭️ Deploy to production

---

## 🎁 Free Tier Limits

Supabase Free Plan includes:
- 🗄️ **500 MB** database space
- 📁 **1 GB** file storage
- 🌐 **2 GB** bandwidth per month
- 👥 **50,000** monthly active users
- 📧 **Unlimited** API requests

This is perfect for development and small projects!

---

**Need help?** Check the troubleshooting section or open an issue!

Happy coding! 🚀

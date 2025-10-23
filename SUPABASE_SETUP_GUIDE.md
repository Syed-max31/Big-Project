# Supabase Integration Guide

This guide will help you connect your Freelary platform to Supabase.

## What is Supabase?

Supabase is an open-source Firebase alternative that provides:
- PostgreSQL database (more powerful than MySQL)
- Built-in authentication
- Real-time subscriptions
- Storage for files
- Auto-generated APIs
- Database backups
- Free tier available

## Step 1: Create a Supabase Account

1. Go to [https://supabase.com](https://supabase.com)
2. Click "Start your project"
3. Sign up with GitHub, Google, or Email
4. Verify your email if required

## Step 2: Create a New Project

1. Click "New Project" in the dashboard
2. Fill in the project details:
   - **Name**: Freelary (or your preferred name)
   - **Database Password**: Choose a strong password (SAVE THIS!)
   - **Region**: Select closest to your users
   - **Pricing Plan**: Start with Free tier
3. Click "Create new project"
4. Wait 2-3 minutes for project setup

## Step 3: Get Your API Credentials

1. In your Supabase project dashboard, click "Settings" (gear icon)
2. Click "API" in the sidebar
3. You'll see:
   - **Project URL**: `https://xxxxxxxxxxxxx.supabase.co`
   - **anon/public key**: `eyJhbGci...` (long string)
   - **service_role key**: `eyJhbGci...` (another long string)

⚠️ **IMPORTANT**: 
- The `anon` key is safe for client-side use
- The `service_role` key should NEVER be exposed to clients (backend only)

## Step 4: Update Your .env File

Open `backend/.env` and update these values:

```env
# Database Selection
DATABASE_TYPE=supabase

# Supabase Configuration
SUPABASE_URL=https://your-project-id.supabase.co
SUPABASE_ANON_KEY=your_anon_key_here
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key_here
```

Replace:
- `https://your-project-id.supabase.co` with your Project URL
- `your_anon_key_here` with your anon/public key
- `your_service_role_key_here` with your service_role key

## Step 5: Create Database Tables

### Option A: Using SQL Editor (Recommended)

1. In Supabase dashboard, click "SQL Editor"
2. Click "New Query"
3. Copy and paste the SQL schema from `backend/database/schema.sql`
4. Modify MySQL-specific syntax to PostgreSQL:
   - Change `INT AUTO_INCREMENT` to `SERIAL`
   - Change `DATETIME` to `TIMESTAMP`
   - Change `VARCHAR(255)` to `TEXT` (or keep VARCHAR)
   - Change `LONGTEXT` to `TEXT`
   - Change engine declarations (remove them)
5. Click "Run" to execute

### Option B: Using Table Editor (Manual)

1. Click "Table Editor" in Supabase dashboard
2. Click "New Table"
3. Manually create each table based on your models
4. Add columns, set data types, and configure relationships

### Example: Create Users Table

```sql
-- Create users table in Supabase
CREATE TABLE users (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password VARCHAR(255),
  name VARCHAR(255),
  role VARCHAR(50) DEFAULT 'user',
  is_verified BOOLEAN DEFAULT false,
  avatar_url TEXT,
  bio TEXT,
  skills TEXT[],
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Enable Row Level Security (RLS)
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

-- Create policy: Users can read their own data
CREATE POLICY "Users can read own data" ON users
  FOR SELECT USING (auth.uid() = id);

-- Create policy: Users can update their own data
CREATE POLICY "Users can update own data" ON users
  FOR UPDATE USING (auth.uid() = id);
```

## Step 6: Enable Realtime (Optional)

If you want real-time features:

1. Go to "Database" → "Replication"
2. Enable replication for tables you want real-time updates
3. Select tables like `messages`, `notifications`, etc.

## Step 7: Set Up Storage (Optional)

For file uploads (avatars, project files):

1. Go to "Storage" in Supabase dashboard
2. Click "New Bucket"
3. Create buckets:
   - `avatars` - for user profile pictures
   - `projects` - for project files
   - `uploads` - for general uploads
4. Configure bucket policies (public/private)

Example storage policy (public avatars):

```sql
-- Allow public access to avatars bucket
CREATE POLICY "Public avatars are viewable by everyone"
ON storage.objects FOR SELECT
USING (bucket_id = 'avatars');

-- Allow authenticated users to upload avatars
CREATE POLICY "Authenticated users can upload avatars"
ON storage.objects FOR INSERT
WITH CHECK (bucket_id = 'avatars' AND auth.role() = 'authenticated');
```

## Step 8: Test the Connection

1. Make sure your `.env` file is updated with Supabase credentials
2. Run the backend server:
   ```bash
   cd backend
   npm run dev
   ```
3. Check the console output for:
   ```
   ✅ Supabase Connected Successfully
   📊 Database URL: https://xxxxx.supabase.co
   ```
4. Test the health endpoint:
   ```bash
   curl http://localhost:5000/health
   ```

## Step 9: Migrate Data (If Needed)

If you have existing data in MySQL:

### Option A: Manual Export/Import

1. Export data from MySQL:
   ```bash
   mysqldump -u root -p freelary > backup.sql
   ```

2. Convert MySQL dump to PostgreSQL format:
   - Use tools like [mysql2postgres](https://github.com/maxlapshin/mysql2postgres)
   - Or manually adjust syntax

3. Import to Supabase using SQL Editor

### Option B: Use Migration Scripts

Create a migration script in `backend/scripts/migrateToSupabase.js`:

```javascript
const mysql = require('../config/mysql');
const supabase = require('../config/supabase');

async function migrate() {
  // Connect to both databases
  await mysql.connect();
  await supabase.connect();
  
  // Migrate users
  const users = await mysql.query('SELECT * FROM users');
  for (const user of users) {
    await supabase.insert('users', {
      id: user.id,
      email: user.email,
      name: user.name,
      // ... map other fields
    });
  }
  
  console.log('Migration completed!');
}

migrate();
```

## Step 10: Update Models (If Using ORM)

You may need to update your models to work with PostgreSQL:

1. Replace Sequelize with PostgreSQL-compatible queries
2. Or use Supabase's built-in query builder
3. Update data types:
   - `TINYINT` → `SMALLINT` or `BOOLEAN`
   - `LONGTEXT` → `TEXT`
   - `DATETIME` → `TIMESTAMP`

## Authentication Integration

Supabase provides built-in authentication. To use it:

```javascript
const supabase = require('../config/supabase');

// Sign up
const { data, error } = await supabase.getAuth().signUp({
  email: 'user@example.com',
  password: 'password123'
});

// Sign in
const { data, error } = await supabase.getAuth().signInWithPassword({
  email: 'user@example.com',
  password: 'password123'
});

// Get current user
const { data: { user } } = await supabase.getAuth().getUser();
```

## Storage Integration

To use Supabase Storage:

```javascript
const supabase = require('../config/supabase');

// Upload file
const { data, error } = await supabase
  .getStorage('avatars')
  .upload('user-123/avatar.png', fileBuffer, {
    contentType: 'image/png'
  });

// Get public URL
const { data } = supabase
  .getStorage('avatars')
  .getPublicUrl('user-123/avatar.png');
```

## Advantages of Supabase

✅ **PostgreSQL Power**: More features than MySQL
✅ **Built-in Auth**: No need for custom JWT implementation
✅ **Real-time**: WebSocket connections out of the box
✅ **Storage**: Integrated file storage (no need for AWS S3)
✅ **Auto APIs**: REST and GraphQL APIs auto-generated
✅ **Row Level Security**: Database-level security policies
✅ **Free Tier**: 500MB database, 1GB storage, 2GB bandwidth
✅ **Backups**: Automatic daily backups
✅ **Dashboard**: Beautiful UI for database management

## Troubleshooting

### Connection Failed

**Problem**: `Supabase Connection Failed: URL is required`
**Solution**: Make sure `SUPABASE_URL` is set in `.env`

### Authentication Error

**Problem**: `Auth error: Invalid JWT`
**Solution**: Check that `SUPABASE_SERVICE_ROLE_KEY` is correctly set

### Table Not Found

**Problem**: `Table "users" does not exist`
**Solution**: Run the SQL schema in Supabase SQL Editor

### Row Level Security Error

**Problem**: `new row violates row-level security policy`
**Solution**: Either disable RLS for testing or create proper policies

### Migration Issues

**Problem**: Data types don't match
**Solution**: Review PostgreSQL data types and adjust schema

## Security Best Practices

1. ✅ **Never expose** `SUPABASE_SERVICE_ROLE_KEY` to frontend
2. ✅ **Enable RLS** on all tables
3. ✅ **Use policies** for fine-grained access control
4. ✅ **Validate input** on both client and server
5. ✅ **Use prepared statements** to prevent SQL injection
6. ✅ **Enable email verification** for new users
7. ✅ **Set up rate limiting** in Supabase dashboard

## Production Checklist

- [ ] All tables created with proper schema
- [ ] Row Level Security enabled on sensitive tables
- [ ] Storage buckets configured with proper policies
- [ ] Authentication flows tested
- [ ] Environment variables set in production
- [ ] Database backups configured
- [ ] API rate limits configured
- [ ] SSL/TLS enabled (automatic with Supabase)
- [ ] Monitoring and alerts set up

## Support & Resources

- 📚 [Supabase Documentation](https://supabase.com/docs)
- 💬 [Supabase Discord](https://discord.supabase.com)
- 📺 [Video Tutorials](https://www.youtube.com/c/Supabase)
- 🐛 [GitHub Issues](https://github.com/supabase/supabase/issues)
- 📧 [Email Support](https://supabase.com/support) (Pro plan)

## Next Steps

1. ✅ Set up Supabase project
2. ✅ Update `.env` with credentials
3. ✅ Create database schema
4. ✅ Test connection
5. ⏭️ Migrate existing data (if applicable)
6. ⏭️ Update models/controllers to use Supabase
7. ⏭️ Enable real-time features
8. ⏭️ Configure storage buckets
9. ⏭️ Set up authentication
10. ⏭️ Deploy to production

---

**Need Help?** 
- Check the console logs for detailed error messages
- Review Supabase dashboard logs
- Test endpoints using the health check: `http://localhost:5000/health`

Good luck with your Supabase integration! 🚀

# MySQL Database Setup Guide for Freelary

## 🚀 Quick Setup

Your Freelary project has been successfully configured to use MySQL instead of MongoDB. Follow these steps to get everything running:

### Prerequisites
- ✅ XAMPP installed and running
- ✅ MySQL service started in XAMPP Control Panel
- ✅ Node.js and npm installed

### Step 1: Start XAMPP MySQL
1. Open XAMPP Control Panel
2. Start **Apache** and **MySQL** services
3. Verify MySQL is running on port 3306

### Step 2: Create Database (Optional)
The setup script will create the database automatically, but you can also create it manually:

```sql
CREATE DATABASE freelary CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### Step 3: Configure Environment
1. Copy `.env.example` to `.env` in the backend folder
2. Update MySQL credentials if needed:
```env
MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_DATABASE=freelary
MYSQL_USERNAME=root
MYSQL_PASSWORD=
```

### Step 4: Setup Database
Run the automated setup script:
```bash
cd backend
npm run setup-mysql
```

This will:
- Connect to MySQL
- Create all necessary tables
- Seed initial data including admin user

### Step 5: Start the Application
```bash
# Backend
cd backend
npm run dev

# Frontend (in another terminal)
cd frontend
npm start
```

## 📊 Database Structure

### Main Tables Created:
- **users** - User accounts (clients, freelancers, admins)
- **categories** - Service categories
- **jobs** - Job postings
- **gigs** - Freelancer services
- **job_applications** - Job applications
- **orders** - Order management
- **reviews** - User reviews
- **messages** - Internal messaging
- **payments** - Payment tracking
- **admin_logs** - Admin activity logs

## 🔐 Default Admin Account
- **Email:** admin@freelary.com
- **Password:** admin123

## 🛠 Available Scripts

```bash
# Setup complete database with seeding
npm run setup-mysql

# Only seed data (database must exist)
npm run seed-mysql

# Create database schema manually (requires mysql CLI)
npm run mysql-schema
```

## 🔧 Manual Database Setup (Alternative)

If the automated setup doesn't work, you can set up manually:

### 1. Create Database
```sql
CREATE DATABASE freelary CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE freelary;
```

### 2. Run Schema File
Import the schema file through phpMyAdmin or MySQL command line:
```bash
mysql -u root -p freelary < backend/database/schema.sql
```

### 3. Seed Data
```bash
cd backend
node database/seedMySQL.js
```

## 🚨 Troubleshooting

### MySQL Connection Failed
- ✅ Check if XAMPP MySQL service is running
- ✅ Verify port 3306 is not blocked
- ✅ Check username/password in .env file

### Database Access Denied
- ✅ Make sure MySQL root user has no password (default XAMPP setup)
- ✅ Or set correct password in .env file

### Tables Not Created
- ✅ Ensure database 'freelary' exists
- ✅ Check MySQL user permissions
- ✅ Run setup script again

## 📈 What Changed from MongoDB

### File Changes:
1. **server.js** - Updated to use MySQL instead of MongoDB
2. **config/mysql.js** - New MySQL configuration
3. **models/mysql/** - New Sequelize models
4. **database/schema.sql** - MySQL table definitions
5. **database/seedMySQL.js** - MySQL data seeding
6. **package.json** - Added MySQL setup scripts

### Key Differences:
- **ORM:** Mongoose → Sequelize
- **Database:** MongoDB → MySQL
- **Schema:** Document-based → Relational tables
- **Queries:** MongoDB queries → SQL queries

## 🔄 Migration from MongoDB (If Needed)

If you have existing MongoDB data you want to migrate:

1. Export MongoDB data
2. Transform to MySQL format
3. Import using seed scripts
4. Verify data integrity

## 🎯 Next Steps

1. ✅ Complete database setup
2. ✅ Start both backend and frontend servers
3. ✅ Login with admin credentials
4. ✅ Test admin panel functionality
5. ✅ Create test users and data

## 📞 Support

If you encounter any issues:
1. Check XAMPP MySQL service status
2. Verify .env configuration
3. Check console logs for specific errors
4. Ensure all dependencies are installed

---

**Your MySQL database setup is now complete! 🎉**

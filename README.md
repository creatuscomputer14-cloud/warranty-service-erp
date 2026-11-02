# Warranty & Service Management ERP — Online-ready bilingual MVP

বাংলা/English UI-সহ Warranty, Sales, Customer, Service Ticket, Technician, Spare Parts ও Reports ব্যবস্থাপনার full-stack starter application। এটি Render-এর persistent disk-এ deploy করার জন্য Docker ও `render.yaml` configuration-সহ প্রস্তুত।

## Local run
1. Node.js 20+ ইনস্টল করুন।
2. Project folder-এ `npm install` চালান।
3. `.env.example` কপি করে `.env` করুন। `JWT_SECRET`-এ দীর্ঘ random secret দিন।
4. `npm start` চালান এবং `http://localhost:3000` খুলুন।

Local development-এর default login:
- Email: `admin@example.com`
- Password: `Admin@123456`

Production-এ এই default credentials seed হয় না। প্রথম Super Admin-এর জন্য `BOOTSTRAP_ADMIN_EMAIL` এবং অন্তত ১২ অক্ষরের `BOOTSTRAP_ADMIN_PASSWORD` environment variable দিতে হবে।

## Online deployment (Render)
1. এই project GitHub repository-তে push করুন।
2. Render-এ **New → Blueprint** নির্বাচন করে repository connect করুন।
3. `render.yaml` থেকে Web Service ও 1GB persistent disk তৈরি হবে (Render plan অনুযায়ী disk/hosting charge প্রযোজ্য)।
4. Render environment settings-এ `BOOTSTRAP_ADMIN_EMAIL` এবং `APP_ORIGIN` সেট করুন। `BOOTSTRAP_ADMIN_PASSWORD` ও `JWT_SECRET` Render generate করে। প্রথম deploy-এর আগে email এবং app origin ঠিক করুন।
5. Deploy শেষে Render-এর দেওয়া HTTPS URL-এ প্রবেশ করুন। প্রথম Super Admin password-এর generated value Render environment settings থেকে নিয়ে নিরাপদে সংরক্ষণ করুন; প্রথম login-এর পর User Management থেকে password পরিবর্তন করুন।

`APP_ORIGIN`-এ শুধু আপনার deployed origin দিন, যেমন `https://your-app.onrender.com`। একই-origin ব্যবহারে এটি CORS allowlist হিসেবে কাজ করবে। SQLite data `/var/data/warranty_erp.sqlite`-এ persistent disk-এ থাকবে। SQLite single-instance/single-writer ধরনের deployment-এর জন্য; একাধিক application instance বা বড় concurrent workload হলে PostgreSQL-এ migration প্রয়োজন।

## Added in this release
- বাংলা/English UI toggle (browser-এ language preference সংরক্ষণ)
- User Management: Admin/Super Admin user create, Super Admin delete, password change
- Password hashing, production JWT secret enforcement, login rate limiting, Helmet headers
- Configurable CORS origin, health endpoint
- Service status history API
- Low stock, expiring warranty ও overdue service notification counters API
- Company settings API
- Render Blueprint, Dockerfile ও persistent disk configuration

## Included core modules
- Dashboard metrics, recent activity, low-stock warning
- Customer, Product, Technician, Parts CRUD
- Sales & warranty registration with automatic warranty expiry
- Service ticket creation/update and warranty eligibility check
- Parts usage and payment APIs
- Public serial-based warranty verification without customer personal data
- Sales, service, warranty, expired warranty, customer and parts report APIs; CSV export UI

## Production limitations / next required work
এটি online-deployable MVP starter, সম্পূর্ণ enterprise production ERP নয়। Public launch-এর আগে অবশ্যই:
- PostgreSQL migration, automated database backups ও restore drill
- Full role-permission matrix (এখন role checks সীমিত endpoint-এ), user edit/disable, audit log review
- QR code generation/scanner, PDF invoice/warranty/service receipt templates
- Scheduled SMS/email notifications with a configured provider
- Service status transition validation, parts stock-in/adjustment, refund/return and accounting ledger
- Settings UI, test suite, monitoring, log retention, privacy policy ও terms
- HTTPS hosting, domain, backups, provider credentials ও access policies যাচাই

## API overview
- Public: `POST /api/login`, `GET /api/warranty/:serial`, `GET /health`
- Authenticated: `/api/dashboard`, `/api/customers`, `/api/products`, `/api/technicians`, `/api/parts`, `/api/sales`, `/api/services`, `/api/reports/:type`, `/api/notifications`
- Admin: `/api/users`, `/api/settings`

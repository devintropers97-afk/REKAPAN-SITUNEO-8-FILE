# 💾 SITUNEO DIGITAL - DATABASE COMPLETE
## All 105+ Tables with SQL Schema & Relationships

**File:** 02_DATABASE_COMPLETE.md  
**Purpose:** Complete Database Schema - ALL Tables  
**Version:** 5.0 Optimized  
**Date:** 18 November 2025

---

# 📋 TABLE OF CONTENTS

1. Database Credentials (Production)
2. Core Tables (Users, Auth, Roles)
3. Business Tables (Orders, Services, Payments)
4. Partner Tables (Commission, ARPU, Referrals)
5. System Tables (Logs, Settings, Notifications)
6. Complete Relationships & Indexes

---

# 📋 SITUNEO DIGITAL - MASTER BATCH 02
## DATABASE SCHEMA & TECHNICAL STACK

**Versi:** 3.0 FINAL  
**Tanggal:** 18 November 2025  
**Status:** Production-Ready Blueprint  
**Dependency:** Master Batch 01

---

## 🗄️ DATABASE CREDENTIALS

```ini
DB_HOST=localhost
DB_USER=nrrskfvk_user_situneo_digital
DB_PASS=Devin1922$
DB_NAME=nrrskfvk_situneo_digital
DB_CHARSET=utf8mb4
DB_COLLATION=utf8mb4_unicode_ci
DB_ENGINE=InnoDB
```

---

## 📊 DATABASE STRUCTURE (85 TABLES)

### CATEGORY 1: USER MANAGEMENT (6 Tables)

#### 1.1 users (Master User Table)
```sql
CREATE TABLE users (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    role ENUM('admin', 'manager', 'spv', 'partner', 'client') NOT NULL,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    phone VARCHAR(20),
    full_name VARCHAR(100) NOT NULL,
    avatar VARCHAR(255),
    status ENUM('active', 'inactive', 'suspended', 'pending') DEFAULT 'pending',
    language_preference ENUM('id', 'en') DEFAULT 'id',
    email_verified BOOLEAN DEFAULT FALSE,
    email_verified_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    last_login TIMESTAMP NULL,
    last_login_ip VARCHAR(45),
    login_count INT UNSIGNED DEFAULT 0,
    
    INDEX idx_role (role),
    INDEX idx_status (status),
    INDEX idx_email (email),
    INDEX idx_username (username)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- NOTES:
-- Central table untuk semua users (5 roles)
-- password_hash menggunakan bcrypt (cost 12)
-- email_verified wajib TRUE untuk bisa login
-- language_preference untuk multi-language support
```

#### 1.2 user_sessions
```sql
CREATE TABLE user_sessions (
    session_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    session_token VARCHAR(255) UNIQUE NOT NULL,
    ip_address VARCHAR(45),
    user_agent TEXT,
    is_remember_me BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    last_activity TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user (user_id),
    INDEX idx_token (session_token),
    INDEX idx_expires (expires_at)
) ENGINE=InnoDB;

-- NOTES:
-- Session timeout: 2 hours (7200 seconds)
-- Remember me: 30 days
-- Auto cleanup expired sessions (cron job)
```

#### 1.3 password_resets
```sql
CREATE TABLE password_resets (
    reset_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    email VARCHAR(100) NOT NULL,
    token VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    used BOOLEAN DEFAULT FALSE,
    used_at TIMESTAMP NULL,
    ip_address VARCHAR(45),
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_token (token),
    INDEX idx_email (email),
    INDEX idx_expires (expires_at)
) ENGINE=InnoDB;

-- NOTES:
-- Token expires: 1 hour
-- Token is single-use (used = TRUE after reset)
```

#### 1.4 email_verifications
```sql
CREATE TABLE email_verifications (
    verification_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    email VARCHAR(100) NOT NULL,
    token VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    verified BOOLEAN DEFAULT FALSE,
    verified_at TIMESTAMP NULL,
    ip_address VARCHAR(45),
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_token (token),
    INDEX idx_user (user_id)
) ENGINE=InnoDB;

-- NOTES:
-- Token expires: 24 hours
-- Auto resend jika tidak verified dalam 23 jam
```

#### 1.5 user_profiles
```sql
CREATE TABLE user_profiles (
    profile_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL UNIQUE,
    bio TEXT,
    address_street VARCHAR(255),
    address_city VARCHAR(100),
    address_province VARCHAR(100),
    address_postal_code VARCHAR(10),
    whatsapp VARCHAR(20),
    telegram VARCHAR(100),
    facebook VARCHAR(255),
    instagram VARCHAR(255),
    linkedin VARCHAR(255),
    twitter VARCHAR(255),
    website VARCHAR(255),
    birth_date DATE,
    gender ENUM('male', 'female', 'other'),
    id_card_number VARCHAR(50), -- KTP
    id_card_file VARCHAR(255), -- Upload KTP
    cv_file VARCHAR(255), -- Upload CV
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user (user_id)
) ENGINE=InnoDB;

-- NOTES:
-- Extended profile untuk semua users
-- KTP & CV wajib untuk Partner/SPV/Manager registration
```

#### 1.6 user_login_history
```sql
CREATE TABLE user_login_history (
    history_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    login_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    logout_at TIMESTAMP NULL,
    ip_address VARCHAR(45),
    user_agent TEXT,
    device_type ENUM('desktop', 'mobile', 'tablet', 'other') DEFAULT 'other',
    browser VARCHAR(50),
    os VARCHAR(50),
    success BOOLEAN DEFAULT TRUE,
    failure_reason VARCHAR(255),
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user (user_id),
    INDEX idx_login_at (login_at)
) ENGINE=InnoDB;

-- NOTES:
-- Track semua login activities
-- Failed login attempts juga dicatat
-- Untuk security audit & analytics
```

---

### CATEGORY 2: CLIENT TABLES (6 Tables)

#### 2.1 clients
```sql
CREATE TABLE clients (
    client_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL UNIQUE,
    partner_id INT UNSIGNED NULL, -- Partner yang referral
    company_name VARCHAR(200),
    company_type ENUM('individual', 'cv', 'pt', 'yayasan', 'other'),
    company_npwp VARCHAR(50),
    industry VARCHAR(100),
    total_orders INT UNSIGNED DEFAULT 0,
    total_spent DECIMAL(15,2) DEFAULT 0.00,
    status ENUM('active', 'inactive', 'blacklist') DEFAULT 'active',
    vip BOOLEAN DEFAULT FALSE, -- Order >5jt = VIP
    registered_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_order_at TIMESTAMP NULL,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE SET NULL,
    INDEX idx_partner (partner_id),
    INDEX idx_status (status),
    INDEX idx_total_spent (total_spent)
) ENGINE=InnoDB;

-- NOTES:
-- Client yang order >Rp 5 juta total = VIP
-- VIP clients dapat dashboard admin khusus untuk edit website
```

#### 2.2 client_orders
```sql
CREATE TABLE client_orders (
    order_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    client_id INT UNSIGNED NOT NULL,
    partner_id INT UNSIGNED NULL,
    service_id INT UNSIGNED NULL,
    package_id INT UNSIGNED NULL,
    order_number VARCHAR(50) UNIQUE NOT NULL, -- Auto-generated: ORD-20251118-0001
    order_type ENUM('beli_putus', 'sewa') NOT NULL,
    total_pages INT UNSIGNED DEFAULT 1,
    price_per_page DECIMAL(12,2) NOT NULL,
    total_amount DECIMAL(15,2) NOT NULL,
    discount_amount DECIMAL(12,2) DEFAULT 0.00,
    final_amount DECIMAL(15,2) NOT NULL,
    status ENUM('pending', 'payment_pending', 'in_progress', 'testing', 'completed', 'cancelled', 'refunded') DEFAULT 'pending',
    payment_status ENUM('unpaid', 'partial', 'paid', 'refunded') DEFAULT 'unpaid',
    assigned_to INT UNSIGNED NULL, -- Partner yang kerjakan (dari task board)
    project_description TEXT,
    special_requirements TEXT,
    demo_url VARCHAR(255),
    final_url VARCHAR(255),
    deadline DATE,
    completed_at TIMESTAMP NULL,
    cancelled_at TIMESTAMP NULL,
    cancel_reason TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (client_id) REFERENCES clients(client_id) ON DELETE CASCADE,
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE SET NULL,
    FOREIGN KEY (service_id) REFERENCES services(service_id) ON DELETE SET NULL,
    FOREIGN KEY (package_id) REFERENCES packages(package_id) ON DELETE SET NULL,
    FOREIGN KEY (assigned_to) REFERENCES partners(partner_id) ON DELETE SET NULL,
    INDEX idx_client (client_id),
    INDEX idx_partner (partner_id),
    INDEX idx_status (status),
    INDEX idx_order_number (order_number),
    INDEX idx_created_at (created_at)
) ENGINE=InnoDB;

-- NOTES:
-- Central table untuk semua orders
-- order_number format: ORD-YYYYMMDD-XXXX
-- assigned_to: Partner dari task board (optional)
```

#### 2.3 client_payments
```sql
CREATE TABLE client_payments (
    payment_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    order_id INT UNSIGNED NOT NULL,
    client_id INT UNSIGNED NOT NULL,
    payment_number VARCHAR(50) UNIQUE NOT NULL, -- PAY-20251118-0001
    payment_method ENUM('transfer_bank', 'qris', 'cash', 'other') NOT NULL,
    amount DECIMAL(15,2) NOT NULL,
    payment_proof VARCHAR(255), -- Upload bukti transfer
    bank_name VARCHAR(100),
    account_holder VARCHAR(100),
    transfer_date DATE,
    notes TEXT,
    status ENUM('pending', 'verified', 'rejected') DEFAULT 'pending',
    verified_by INT UNSIGNED NULL, -- Admin yang verify
    verified_at TIMESTAMP NULL,
    rejection_reason TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (order_id) REFERENCES client_orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (client_id) REFERENCES clients(client_id) ON DELETE CASCADE,
    FOREIGN KEY (verified_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_order (order_id),
    INDEX idx_client (client_id),
    INDEX idx_status (status),
    INDEX idx_payment_number (payment_number)
) ENGINE=InnoDB;

-- NOTES:
-- Client upload bukti transfer
-- Admin verify manual (approve/reject)
-- Support multiple payments untuk 1 order (cicilan)
```

#### 2.4 client_invoices
```sql
CREATE TABLE client_invoices (
    invoice_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    order_id INT UNSIGNED NOT NULL,
    client_id INT UNSIGNED NOT NULL,
    invoice_number VARCHAR(50) UNIQUE NOT NULL, -- INV-20251118-0001
    invoice_date DATE NOT NULL,
    due_date DATE NOT NULL,
    subtotal DECIMAL(15,2) NOT NULL,
    tax_amount DECIMAL(12,2) DEFAULT 0.00,
    discount_amount DECIMAL(12,2) DEFAULT 0.00,
    total_amount DECIMAL(15,2) NOT NULL,
    paid_amount DECIMAL(15,2) DEFAULT 0.00,
    status ENUM('draft', 'sent', 'paid', 'overdue', 'cancelled') DEFAULT 'draft',
    notes TEXT,
    terms_conditions TEXT,
    sent_at TIMESTAMP NULL,
    paid_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (order_id) REFERENCES client_orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (client_id) REFERENCES clients(client_id) ON DELETE CASCADE,
    INDEX idx_order (order_id),
    INDEX idx_client (client_id),
    INDEX idx_invoice_number (invoice_number),
    INDEX idx_status (status)
) ENGINE=InnoDB;

-- NOTES:
-- Auto-generate dari order
-- Support download PDF
-- Auto-send email ke client
```

#### 2.5 client_subscriptions (untuk Sewa Bulanan)
```sql
CREATE TABLE client_subscriptions (
    subscription_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    order_id INT UNSIGNED NOT NULL,
    client_id INT UNSIGNED NOT NULL,
    partner_id INT UNSIGNED NULL,
    subscription_number VARCHAR(50) UNIQUE NOT NULL, -- SUB-20251118-0001
    start_date DATE NOT NULL,
    end_date DATE NULL, -- NULL = active, NOT NULL = ended
    monthly_amount DECIMAL(12,2) NOT NULL,
    billing_cycle ENUM('monthly', 'quarterly', 'yearly') DEFAULT 'monthly',
    status ENUM('active', 'paused', 'cancelled', 'completed') DEFAULT 'active',
    total_months_paid INT UNSIGNED DEFAULT 0,
    total_amount_paid DECIMAL(15,2) DEFAULT 0.00,
    next_billing_date DATE,
    auto_renew BOOLEAN DEFAULT TRUE,
    cancel_reason TEXT,
    cancelled_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (order_id) REFERENCES client_orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (client_id) REFERENCES clients(client_id) ON DELETE CASCADE,
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE SET NULL,
    INDEX idx_order (order_id),
    INDEX idx_client (client_id),
    INDEX idx_partner (partner_id),
    INDEX idx_status (status),
    INDEX idx_next_billing (next_billing_date)
) ENGINE=InnoDB;

-- NOTES:
-- Track sewa bulanan
-- Auto-generate invoice setiap bulan (cron job)
-- Jika cancel <3 bulan = partner kena tanggungan
```

#### 2.6 client_support_tickets
```sql
CREATE TABLE client_support_tickets (
    ticket_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    client_id INT UNSIGNED NOT NULL,
    order_id INT UNSIGNED NULL,
    ticket_number VARCHAR(50) UNIQUE NOT NULL, -- TICK-20251118-0001
    subject VARCHAR(255) NOT NULL,
    message TEXT NOT NULL,
    category ENUM('bug', 'feature_request', 'question', 'complaint', 'other') DEFAULT 'question',
    priority ENUM('low', 'medium', 'high', 'urgent') DEFAULT 'medium',
    status ENUM('open', 'in_progress', 'resolved', 'closed') DEFAULT 'open',
    assigned_to INT UNSIGNED NULL, -- Admin/Partner yang handle
    resolved_at TIMESTAMP NULL,
    closed_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (client_id) REFERENCES clients(client_id) ON DELETE CASCADE,
    FOREIGN KEY (order_id) REFERENCES client_orders(order_id) ON DELETE SET NULL,
    FOREIGN KEY (assigned_to) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_client (client_id),
    INDEX idx_ticket_number (ticket_number),
    INDEX idx_status (status),
    INDEX idx_priority (priority)
) ENGINE=InnoDB;

-- NOTES:
-- Support ticket system
-- Client bisa create dari dashboard
-- Admin/Partner bisa reply
```

---

### CATEGORY 3: PARTNER TABLES (10 Tables)

#### 3.1 partners
```sql
CREATE TABLE partners (
    partner_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL UNIQUE,
    spv_id INT UNSIGNED NULL, -- SPV atasan (optional)
    referral_code VARCHAR(20) UNIQUE NOT NULL,
    tier_current ENUM('1', '2', '3', 'MAX') DEFAULT '1',
    tier_highest ENUM('1', '2', '3', 'MAX') DEFAULT '1',
    total_orders_lifetime INT UNSIGNED DEFAULT 0,
    monthly_orders INT UNSIGNED DEFAULT 0,
    total_revenue_lifetime DECIMAL(15,2) DEFAULT 0.00,
    monthly_revenue DECIMAL(12,2) DEFAULT 0.00,
    commission_balance DECIMAL(15,2) DEFAULT 0.00, -- Available untuk withdrawal
    commission_pending DECIMAL(15,2) DEFAULT 0.00, -- Pending orders
    commission_paid_total DECIMAL(15,2) DEFAULT 0.00, -- Total withdrawn
    commission_rate DECIMAL(5,2), -- Current tier %
    total_clients INT UNSIGNED DEFAULT 0,
    active_clients INT UNSIGNED DEFAULT 0,
    status ENUM('active', 'inactive', 'suspended', 'blacklist') DEFAULT 'active',
    performance_score DECIMAL(3,2) DEFAULT 5.00, -- 1-5 rating
    last_order_at TIMESTAMP NULL,
    registered_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    approved_at TIMESTAMP NULL,
    suspended_at TIMESTAMP NULL,
    suspension_reason TEXT,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE SET NULL,
    INDEX idx_spv (spv_id),
    INDEX idx_tier (tier_current),
    INDEX idx_status (status),
    INDEX idx_referral (referral_code)
) ENGINE=InnoDB;

-- NOTES:
-- Central table untuk partners
-- tier_current: Tier saat ini (30-55%)
-- tier_highest: Tier tertinggi yang pernah dicapai
-- commission_balance: Bisa langsung ditarik (>=Rp 50K)
-- commission_pending: Dari order yang belum selesai
```

#### 3.2 partner_referrals
```sql
CREATE TABLE partner_referrals (
    referral_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    partner_id INT UNSIGNED NOT NULL,
    referred_user_id INT UNSIGNED NOT NULL, -- Client/Partner yang direferral
    referred_type ENUM('client', 'partner') NOT NULL,
    referral_code_used VARCHAR(20) NOT NULL,
    status ENUM('pending', 'active', 'inactive') DEFAULT 'pending',
    first_order_at TIMESTAMP NULL,
    total_orders INT UNSIGNED DEFAULT 0,
    total_revenue DECIMAL(15,2) DEFAULT 0.00,
    total_commission DECIMAL(15,2) DEFAULT 0.00,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    FOREIGN KEY (referred_user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_partner (partner_id),
    INDEX idx_referred (referred_user_id),
    INDEX idx_code (referral_code_used)
) ENGINE=InnoDB;

-- NOTES:
-- Track siapa yang direferral oleh partner
-- Support referral Partner→Client & SPV→Partner
```

#### 3.3 partner_commissions
```sql
CREATE TABLE partner_commissions (
    commission_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    partner_id INT UNSIGNED NOT NULL,
    order_id INT UNSIGNED NOT NULL,
    commission_type ENUM('base', 'bonus', 'task', 'referral', 'other') DEFAULT 'base',
    amount DECIMAL(12,2) NOT NULL,
    percentage DECIMAL(5,2), -- Tier % (30-55%)
    status ENUM('pending', 'approved', 'paid', 'cancelled') DEFAULT 'pending',
    description TEXT,
    approved_by INT UNSIGNED NULL,
    approved_at TIMESTAMP NULL,
    paid_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    FOREIGN KEY (order_id) REFERENCES client_orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (approved_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_partner (partner_id),
    INDEX idx_order (order_id),
    INDEX idx_status (status)
) ENGINE=InnoDB;

-- NOTES:
-- Detail setiap komisi partner
-- base: Komisi dari client order
-- task: Komisi dari task board admin
-- bonus: Bonus achievement
```

#### 3.4 partner_withdrawals
```sql
CREATE TABLE partner_withdrawals (
    withdrawal_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    partner_id INT UNSIGNED NOT NULL,
    withdrawal_number VARCHAR(50) UNIQUE NOT NULL, -- WD-20251118-0001
    amount DECIMAL(12,2) NOT NULL,
    fee DECIMAL(8,2) DEFAULT 0.00,
    net_amount DECIMAL(12,2) NOT NULL, -- amount - fee
    method ENUM('transfer_bank', 'qris', 'other') NOT NULL,
    bank_name VARCHAR(100),
    account_number VARCHAR(50),
    account_holder VARCHAR(100),
    qris_phone VARCHAR(20),
    status ENUM('pending', 'processing', 'completed', 'rejected', 'cancelled') DEFAULT 'pending',
    proof_file VARCHAR(255), -- Admin upload bukti transfer
    notes TEXT,
    requested_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    processed_by INT UNSIGNED NULL,
    processed_at TIMESTAMP NULL,
    completed_at TIMESTAMP NULL,
    rejection_reason TEXT,
    
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    FOREIGN KEY (processed_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_partner (partner_id),
    INDEX idx_status (status),
    INDEX idx_withdrawal_number (withdrawal_number)
) ENGINE=InnoDB;

-- NOTES:
-- Minimal withdrawal: Rp 50,000
-- Admin approve manual
-- Processing time: Instant - 3 hari kerja
```

#### 3.5 partner_tier_history
```sql
CREATE TABLE partner_tier_history (
    history_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    partner_id INT UNSIGNED NOT NULL,
    old_tier ENUM('1', '2', '3', 'MAX'),
    new_tier ENUM('1', '2', '3', 'MAX') NOT NULL,
    reason VARCHAR(255),
    total_orders_at_change INT UNSIGNED,
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    INDEX idx_partner (partner_id),
    INDEX idx_changed_at (changed_at)
) ENGINE=InnoDB;

-- NOTES:
-- Track perubahan tier partner
-- Untuk analytics & reporting
```

#### 3.6 partner_tasks
```sql
CREATE TABLE partner_tasks (
    task_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    posted_by INT UNSIGNED NOT NULL, -- Admin yang posting
    title VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    requirements TEXT,
    budget DECIMAL(12,2) NOT NULL,
    deadline DATE NOT NULL,
    max_workers INT UNSIGNED DEFAULT 1, -- Berapa partner yang bisa ambil
    category VARCHAR(100),
    difficulty ENUM('easy', 'medium', 'hard', 'expert') DEFAULT 'medium',
    status ENUM('open', 'in_progress', 'completed', 'cancelled') DEFAULT 'open',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (posted_by) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_status (status),
    INDEX idx_deadline (deadline),
    INDEX idx_posted_by (posted_by)
) ENGINE=InnoDB;

-- NOTES:
-- Task board: Admin posting jobs
-- Partner bisa apply
-- Support multiple workers untuk 1 task
```

#### 3.7 partner_task_applications
```sql
CREATE TABLE partner_task_applications (
    application_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    task_id INT UNSIGNED NOT NULL,
    partner_id INT UNSIGNED NOT NULL,
    message TEXT,
    portfolio_links TEXT,
    estimated_completion_days INT UNSIGNED,
    status ENUM('pending', 'accepted', 'rejected', 'completed') DEFAULT 'pending',
    accepted_by INT UNSIGNED NULL,
    accepted_at TIMESTAMP NULL,
    rejection_reason TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (task_id) REFERENCES partner_tasks(task_id) ON DELETE CASCADE,
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    FOREIGN KEY (accepted_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_task (task_id),
    INDEX idx_partner (partner_id),
    INDEX idx_status (status),
    UNIQUE KEY unique_application (task_id, partner_id)
) ENGINE=InnoDB;

-- NOTES:
-- Partner apply untuk task
-- Admin pilih partner yang cocok
-- 1 partner bisa apply multiple tasks
```

#### 3.8 partner_task_submissions
```sql
CREATE TABLE partner_task_submissions (
    submission_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    task_id INT UNSIGNED NOT NULL,
    partner_id INT UNSIGNED NOT NULL,
    submission_url VARCHAR(255),
    submission_files TEXT, -- JSON array of files
    description TEXT,
    status ENUM('submitted', 'under_review', 'approved', 'rejected', 'revision_needed') DEFAULT 'submitted',
    reviewed_by INT UNSIGNED NULL,
    review_notes TEXT,
    revision_count INT UNSIGNED DEFAULT 0,
    approved_at TIMESTAMP NULL,
    submitted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (task_id) REFERENCES partner_tasks(task_id) ON DELETE CASCADE,
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    FOREIGN KEY (reviewed_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_task (task_id),
    INDEX idx_partner (partner_id),
    INDEX idx_status (status)
) ENGINE=InnoDB;

-- NOTES:
-- Partner submit hasil task
-- Admin review & approve/reject
-- Support revisi
```

#### 3.9 partner_performance_logs
```sql
CREATE TABLE partner_performance_logs (
    log_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    partner_id INT UNSIGNED NOT NULL,
    metric_type ENUM('order_completed', 'client_retained', 'task_completed', 'late_delivery', 'complaint', 'positive_review') NOT NULL,
    value DECIMAL(10,2),
    description TEXT,
    recorded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    INDEX idx_partner (partner_id),
    INDEX idx_type (metric_type),
    INDEX idx_recorded (recorded_at)
) ENGINE=InnoDB;

-- NOTES:
-- Track performance partner
-- Auto-calculate performance_score
-- Untuk KPI & evaluation
```

#### 3.10 partner_bank_accounts
```sql
CREATE TABLE partner_bank_accounts (
    account_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    partner_id INT UNSIGNED NOT NULL,
    bank_name VARCHAR(100) NOT NULL,
    account_number VARCHAR(50) NOT NULL,
    account_holder VARCHAR(100) NOT NULL,
    is_primary BOOLEAN DEFAULT FALSE,
    is_verified BOOLEAN DEFAULT FALSE,
    verified_by INT UNSIGNED NULL,
    verified_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    FOREIGN KEY (verified_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_partner (partner_id)
) ENGINE=InnoDB;

-- NOTES:
-- Partner bisa punya multiple bank accounts
-- 1 account sebagai primary
-- Admin verify untuk security
```

---

### CATEGORY 4: SPV TABLES (8 Tables)

#### 4.1 spv_supervisors
```sql
CREATE TABLE spv_supervisors (
    spv_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL UNIQUE,
    manager_id INT UNSIGNED NULL, -- Manager Area atasan
    referral_code VARCHAR(20) UNIQUE NOT NULL,
    total_partners INT UNSIGNED DEFAULT 0,
    active_partners INT UNSIGNED DEFAULT 0,
    total_revenue_lifetime DECIMAL(15,2) DEFAULT 0.00,
    monthly_revenue DECIMAL(12,2) DEFAULT 0.00,
    arpu_current_month DECIMAL(12,2) DEFAULT 0.00,
    arpu_bonus_tier INT UNSIGNED DEFAULT 0, -- 0-4
    arpu_bonus_amount DECIMAL(10,2) DEFAULT 0.00,
    commission_balance DECIMAL(15,2) DEFAULT 0.00,
    commission_pending DECIMAL(15,2) DEFAULT 0.00,
    commission_paid_total DECIMAL(15,2) DEFAULT 0.00,
    commission_rate DECIMAL(5,2) DEFAULT 10.00, -- Always 10%
    status ENUM('active', 'inactive', 'suspended') DEFAULT 'active',
    performance_score DECIMAL(3,2) DEFAULT 5.00,
    registered_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    approved_at TIMESTAMP NULL,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (manager_id) REFERENCES manager_areas(manager_id) ON DELETE SET NULL,
    INDEX idx_manager (manager_id),
    INDEX idx_status (status),
    INDEX idx_arpu (arpu_current_month)
) ENGINE=InnoDB;

-- NOTES:
-- SPV manage partners
-- Commission: 10% base + ARPU bonus
-- ARPU bonus tiers: 15M, 35M, 75M, 200M+
```

#### 4.2 spv_partner_assignments
```sql
CREATE TABLE spv_partner_assignments (
    assignment_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    spv_id INT UNSIGNED NOT NULL,
    partner_id INT UNSIGNED NOT NULL,
    assigned_by INT UNSIGNED NULL, -- Admin yang assign
    assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    removed_at TIMESTAMP NULL,
    removal_reason TEXT,
    status ENUM('active', 'removed') DEFAULT 'active',
    
    FOREIGN KEY (spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE CASCADE,
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    FOREIGN KEY (assigned_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_spv (spv_id),
    INDEX idx_partner (partner_id),
    INDEX idx_status (status)
) ENGINE=InnoDB;

-- NOTES:
-- Track assignment partner ke SPV
-- Support reassignment
-- History tracking
```

#### 4.3 spv_commissions
```sql
CREATE TABLE spv_commissions (
    commission_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    spv_id INT UNSIGNED NOT NULL,
    partner_id INT UNSIGNED NOT NULL, -- Partner yang bawa order
    order_id INT UNSIGNED NOT NULL,
    commission_type ENUM('base', 'arpu_bonus') DEFAULT 'base',
    amount DECIMAL(12,2) NOT NULL,
    percentage DECIMAL(5,2) DEFAULT 10.00,
    status ENUM('pending', 'approved', 'paid', 'cancelled') DEFAULT 'pending',
    approved_by INT UNSIGNED NULL,
    approved_at TIMESTAMP NULL,
    paid_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE CASCADE,
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    FOREIGN KEY (order_id) REFERENCES client_orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (approved_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_spv (spv_id),
    INDEX idx_partner (partner_id),
    INDEX idx_order (order_id),
    INDEX idx_status (status)
) ENGINE=InnoDB;

-- NOTES:
-- base: 10% dari partner downline orders
-- arpu_bonus: Monthly ARPU bonus
```

#### 4.4 spv_arpu_history
```sql
CREATE TABLE spv_arpu_history (
    history_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    spv_id INT UNSIGNED NOT NULL,
    month DATE NOT NULL, -- YYYY-MM-01
    total_revenue DECIMAL(12,2) NOT NULL,
    total_partners INT UNSIGNED NOT NULL,
    arpu_value DECIMAL(12,2) NOT NULL,
    bonus_tier INT UNSIGNED DEFAULT 0,
    bonus_amount DECIMAL(10,2) DEFAULT 0.00,
    calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE CASCADE,
    INDEX idx_spv (spv_id),
    INDEX idx_month (month),
    UNIQUE KEY unique_spv_month (spv_id, month)
) ENGINE=InnoDB;

-- NOTES:
-- Track ARPU bulanan SPV
-- Auto-calculate setiap awal bulan (cron job)
```

#### 4.5 spv_withdrawals
```sql
CREATE TABLE spv_withdrawals (
    withdrawal_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    spv_id INT UNSIGNED NOT NULL,
    withdrawal_number VARCHAR(50) UNIQUE NOT NULL,
    amount DECIMAL(12,2) NOT NULL,
    fee DECIMAL(8,2) DEFAULT 0.00,
    net_amount DECIMAL(12,2) NOT NULL,
    method ENUM('transfer_bank', 'qris', 'other') NOT NULL,
    bank_name VARCHAR(100),
    account_number VARCHAR(50),
    account_holder VARCHAR(100),
    status ENUM('pending', 'processing', 'completed', 'rejected', 'cancelled') DEFAULT 'pending',
    proof_file VARCHAR(255),
    requested_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    processed_by INT UNSIGNED NULL,
    processed_at TIMESTAMP NULL,
    completed_at TIMESTAMP NULL,
    
    FOREIGN KEY (spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE CASCADE,
    FOREIGN KEY (processed_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_spv (spv_id),
    INDEX idx_status (status)
) ENGINE=InnoDB;
```

#### 4.6 spv_performance_logs
```sql
CREATE TABLE spv_performance_logs (
    log_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    spv_id INT UNSIGNED NOT NULL,
    metric_type ENUM('partner_recruited', 'partner_trained', 'team_revenue', 'arpu_achieved', 'partner_retained') NOT NULL,
    value DECIMAL(10,2),
    description TEXT,
    recorded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE CASCADE,
    INDEX idx_spv (spv_id),
    INDEX idx_type (metric_type)
) ENGINE=InnoDB;
```

#### 4.7 spv_bank_accounts
```sql
CREATE TABLE spv_bank_accounts (
    account_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    spv_id INT UNSIGNED NOT NULL,
    bank_name VARCHAR(100) NOT NULL,
    account_number VARCHAR(50) NOT NULL,
    account_holder VARCHAR(100) NOT NULL,
    is_primary BOOLEAN DEFAULT FALSE,
    is_verified BOOLEAN DEFAULT FALSE,
    verified_by INT UNSIGNED NULL,
    verified_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE CASCADE,
    FOREIGN KEY (verified_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_spv (spv_id)
) ENGINE=InnoDB;
```

#### 4.8 spv_referrals
```sql
CREATE TABLE spv_referrals (
    referral_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    spv_id INT UNSIGNED NOT NULL,
    referred_partner_id INT UNSIGNED NOT NULL,
    referral_code_used VARCHAR(20) NOT NULL,
    status ENUM('pending', 'active', 'inactive') DEFAULT 'pending',
    total_orders INT UNSIGNED DEFAULT 0,
    total_revenue DECIMAL(15,2) DEFAULT 0.00,
    total_commission DECIMAL(15,2) DEFAULT 0.00,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE CASCADE,
    FOREIGN KEY (referred_partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    INDEX idx_spv (spv_id),
    INDEX idx_partner (referred_partner_id)
) ENGINE=InnoDB;
```

---

### CATEGORY 5: MANAGER TABLES (8 Tables)

#### 5.1 manager_areas
```sql
CREATE TABLE manager_areas (
    manager_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL UNIQUE,
    referral_code VARCHAR(20) UNIQUE NOT NULL,
    area_name VARCHAR(100), -- Jakarta, Surabaya, etc
    total_spv INT UNSIGNED DEFAULT 0,
    total_partners INT UNSIGNED DEFAULT 0,
    total_revenue_lifetime DECIMAL(15,2) DEFAULT 0.00,
    monthly_revenue DECIMAL(12,2) DEFAULT 0.00,
    arpu_current_month DECIMAL(12,2) DEFAULT 0.00,
    arpu_bonus_tier INT UNSIGNED DEFAULT 0,
    arpu_bonus_amount DECIMAL(10,2) DEFAULT 0.00,
    commission_balance DECIMAL(15,2) DEFAULT 0.00,
    commission_pending DECIMAL(15,2) DEFAULT 0.00,
    commission_paid_total DECIMAL(15,2) DEFAULT 0.00,
    commission_rate DECIMAL(5,2) DEFAULT 5.00, -- Always 5%
    status ENUM('active', 'inactive', 'suspended') DEFAULT 'active',
    performance_score DECIMAL(3,2) DEFAULT 5.00,
    registered_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    approved_at TIMESTAMP NULL,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_status (status),
    INDEX idx_arpu (arpu_current_month)
) ENGINE=InnoDB;

-- NOTES:
-- Manager manage SPV & area
-- Commission: 5% base + ARPU bonus
-- ARPU bonus tiers: 45M, 105M, 225M, 600M+
```

#### 5.2 manager_spv_assignments
```sql
CREATE TABLE manager_spv_assignments (
    assignment_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    manager_id INT UNSIGNED NOT NULL,
    spv_id INT UNSIGNED NOT NULL,
    assigned_by INT UNSIGNED NULL,
    assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    removed_at TIMESTAMP NULL,
    removal_reason TEXT,
    status ENUM('active', 'removed') DEFAULT 'active',
    
    FOREIGN KEY (manager_id) REFERENCES manager_areas(manager_id) ON DELETE CASCADE,
    FOREIGN KEY (spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE CASCADE,
    FOREIGN KEY (assigned_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_manager (manager_id),
    INDEX idx_spv (spv_id),
    INDEX idx_status (status)
) ENGINE=InnoDB;
```

#### 5.3 manager_commissions
```sql
CREATE TABLE manager_commissions (
    commission_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    manager_id INT UNSIGNED NOT NULL,
    spv_id INT UNSIGNED NULL,
    partner_id INT UNSIGNED NULL,
    order_id INT UNSIGNED NOT NULL,
    commission_type ENUM('base', 'arpu_bonus') DEFAULT 'base',
    amount DECIMAL(12,2) NOT NULL,
    percentage DECIMAL(5,2) DEFAULT 5.00,
    status ENUM('pending', 'approved', 'paid', 'cancelled') DEFAULT 'pending',
    approved_by INT UNSIGNED NULL,
    approved_at TIMESTAMP NULL,
    paid_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (manager_id) REFERENCES manager_areas(manager_id) ON DELETE CASCADE,
    FOREIGN KEY (spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE SET NULL,
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE SET NULL,
    FOREIGN KEY (order_id) REFERENCES client_orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (approved_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_manager (manager_id),
    INDEX idx_order (order_id),
    INDEX idx_status (status)
) ENGINE=InnoDB;
```

#### 5.4 manager_arpu_history
```sql
CREATE TABLE manager_arpu_history (
    history_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    manager_id INT UNSIGNED NOT NULL,
    month DATE NOT NULL,
    total_revenue DECIMAL(12,2) NOT NULL,
    total_spv INT UNSIGNED NOT NULL,
    total_partners INT UNSIGNED NOT NULL,
    arpu_value DECIMAL(12,2) NOT NULL,
    bonus_tier INT UNSIGNED DEFAULT 0,
    bonus_amount DECIMAL(10,2) DEFAULT 0.00,
    calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (manager_id) REFERENCES manager_areas(manager_id) ON DELETE CASCADE,
    INDEX idx_manager (manager_id),
    INDEX idx_month (month),
    UNIQUE KEY unique_manager_month (manager_id, month)
) ENGINE=InnoDB;
```

#### 5.5 manager_withdrawals
```sql
CREATE TABLE manager_withdrawals (
    withdrawal_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    manager_id INT UNSIGNED NOT NULL,
    withdrawal_number VARCHAR(50) UNIQUE NOT NULL,
    amount DECIMAL(12,2) NOT NULL,
    fee DECIMAL(8,2) DEFAULT 0.00,
    net_amount DECIMAL(12,2) NOT NULL,
    method ENUM('transfer_bank', 'qris', 'other') NOT NULL,
    bank_name VARCHAR(100),
    account_number VARCHAR(50),
    account_holder VARCHAR(100),
    status ENUM('pending', 'processing', 'completed', 'rejected', 'cancelled') DEFAULT 'pending',
    proof_file VARCHAR(255),
    requested_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    processed_by INT UNSIGNED NULL,
    processed_at TIMESTAMP NULL,
    completed_at TIMESTAMP NULL,
    
    FOREIGN KEY (manager_id) REFERENCES manager_areas(manager_id) ON DELETE CASCADE,
    FOREIGN KEY (processed_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_manager (manager_id),
    INDEX idx_status (status)
) ENGINE=InnoDB;
```

#### 5.6 manager_performance_logs
```sql
CREATE TABLE manager_performance_logs (
    log_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    manager_id INT UNSIGNED NOT NULL,
    metric_type ENUM('spv_recruited', 'area_revenue', 'arpu_achieved', 'team_growth') NOT NULL,
    value DECIMAL(10,2),
    description TEXT,
    recorded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (manager_id) REFERENCES manager_areas(manager_id) ON DELETE CASCADE,
    INDEX idx_manager (manager_id),
    INDEX idx_type (metric_type)
) ENGINE=InnoDB;
```

#### 5.7 manager_bank_accounts
```sql
CREATE TABLE manager_bank_accounts (
    account_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    manager_id INT UNSIGNED NOT NULL,
    bank_name VARCHAR(100) NOT NULL,
    account_number VARCHAR(50) NOT NULL,
    account_holder VARCHAR(100) NOT NULL,
    is_primary BOOLEAN DEFAULT FALSE,
    is_verified BOOLEAN DEFAULT FALSE,
    verified_by INT UNSIGNED NULL,
    verified_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (manager_id) REFERENCES manager_areas(manager_id) ON DELETE CASCADE,
    FOREIGN KEY (verified_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_manager (manager_id)
) ENGINE=InnoDB;
```

#### 5.8 manager_referrals
```sql
CREATE TABLE manager_referrals (
    referral_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    manager_id INT UNSIGNED NOT NULL,
    referred_spv_id INT UNSIGNED NOT NULL,
    referral_code_used VARCHAR(20) NOT NULL,
    status ENUM('pending', 'active', 'inactive') DEFAULT 'pending',
    total_partners INT UNSIGNED DEFAULT 0,
    total_revenue DECIMAL(15,2) DEFAULT 0.00,
    total_commission DECIMAL(15,2) DEFAULT 0.00,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (manager_id) REFERENCES manager_areas(manager_id) ON DELETE CASCADE,
    FOREIGN KEY (referred_spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE CASCADE,
    INDEX idx_manager (manager_id),
    INDEX idx_spv (referred_spv_id)
) ENGINE=InnoDB;
```

---

**END OF MASTER BATCH 02 - PART 1**
*Total: 48 tables dijelaskan*

**LANJUT KE PART 2:**
- Service Tables (7)
- Demo Tables (3)
- Hierarchy Tables (4)
- Withdrawal & Commission Tables (6)
- Notification & Analytics Tables (10)
- Settings & CMS Tables (7+)


---

# 📋 SITUNEO DIGITAL - MASTER BATCH 02 (PART 2)
## DATABASE SCHEMA - TABLES 49-85

**Versi:** 3.0 FINAL  
**Tanggal:** 18 November 2025  
**Continuation From:** MASTER_BATCH_02_DATABASE_TECHNICAL.md (Part 1)

---

### CATEGORY 6: HIERARCHY TABLES (4 Tables)

#### 6.1 hierarchy_tree
```sql
CREATE TABLE hierarchy_tree (
    node_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    user_type ENUM('admin', 'manager', 'spv', 'partner', 'client') NOT NULL,
    parent_id INT UNSIGNED NULL, -- NULL untuk admin (root)
    path VARCHAR(500), -- Format: /1/23/456/789 (untuk query ancestor/descendant)
    level INT UNSIGNED DEFAULT 0, -- 0=admin, 1=manager, 2=spv, 3=partner, 4=client
    total_downline INT UNSIGNED DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (parent_id) REFERENCES hierarchy_tree(node_id) ON DELETE CASCADE,
    INDEX idx_user (user_id),
    INDEX idx_parent (parent_id),
    INDEX idx_path (path),
    INDEX idx_level (level),
    UNIQUE KEY unique_user (user_id)
) ENGINE=InnoDB;

-- NOTES:
-- Nested Set Model untuk hierarchy
-- path: Materialized path untuk fast ancestor queries
-- Example path: /1/5/12/45 = admin(1) > manager(5) > spv(12) > partner(45)
```

#### 6.2 hierarchy_changes
```sql
CREATE TABLE hierarchy_changes (
    change_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    old_parent_id INT UNSIGNED NULL,
    new_parent_id INT UNSIGNED NULL,
    old_path VARCHAR(500),
    new_path VARCHAR(500),
    reason VARCHAR(255),
    changed_by INT UNSIGNED NOT NULL,
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (changed_by) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user (user_id),
    INDEX idx_changed_at (changed_at)
) ENGINE=InnoDB;

-- NOTES:
-- History tracking untuk reassignment
-- Audit trail untuk hierarchy changes
```

#### 6.3 orphan_users
```sql
CREATE TABLE orphan_users (
    orphan_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    user_type ENUM('partner', 'spv', 'manager') NOT NULL,
    previous_parent_id INT UNSIGNED NULL,
    orphaned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    orphaned_reason ENUM('parent_deleted', 'parent_suspended', 'reassignment', 'other') NOT NULL,
    reassigned_to INT UNSIGNED NULL,
    reassigned_at TIMESTAMP NULL,
    reassigned_by INT UNSIGNED NULL,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (reassigned_to) REFERENCES users(id) ON DELETE SET NULL,
    FOREIGN KEY (reassigned_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_user (user_id),
    INDEX idx_user_type (user_type)
) ENGINE=InnoDB;

-- NOTES:
-- Temporary table untuk orphan users
-- Jika SPV dihapus, partner-nya jadi orphan
-- Admin bisa reassign orphans ke SPV lain
```

#### 6.4 hierarchy_statistics
```sql
CREATE TABLE hierarchy_statistics (
    stat_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    user_type ENUM('manager', 'spv', 'partner') NOT NULL,
    date DATE NOT NULL,
    total_downline INT UNSIGNED DEFAULT 0,
    active_downline INT UNSIGNED DEFAULT 0,
    new_recruits_today INT UNSIGNED DEFAULT 0,
    total_revenue_today DECIMAL(12,2) DEFAULT 0.00,
    total_orders_today INT UNSIGNED DEFAULT 0,
    calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user (user_id),
    INDEX idx_date (date),
    UNIQUE KEY unique_user_date (user_id, date)
) ENGINE=InnoDB;

-- NOTES:
-- Daily statistics per user
-- Auto-calculated setiap midnight (cron job)
```

---

### CATEGORY 7: SERVICE TABLES (7 Tables)

#### 7.1 services
```sql
CREATE TABLE services (
    service_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    category_id INT UNSIGNED NOT NULL,
    division_id INT UNSIGNED NOT NULL,
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    description TEXT,
    short_description VARCHAR(500),
    price_beli_putus DECIMAL(12,2) NOT NULL,
    price_sewa_monthly DECIMAL(12,2) NOT NULL,
    features TEXT, -- JSON array
    requirements TEXT,
    delivery_time INT UNSIGNED, -- Days
    is_popular BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    icon VARCHAR(255),
    image VARCHAR(255),
    sort_order INT UNSIGNED DEFAULT 0,
    view_count INT UNSIGNED DEFAULT 0,
    order_count INT UNSIGNED DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (category_id) REFERENCES service_categories(category_id) ON DELETE CASCADE,
    FOREIGN KEY (division_id) REFERENCES service_divisions(division_id) ON DELETE CASCADE,
    INDEX idx_category (category_id),
    INDEX idx_division (division_id),
    INDEX idx_slug (slug),
    INDEX idx_is_active (is_active),
    INDEX idx_is_popular (is_popular)
) ENGINE=InnoDB;

-- NOTES:
-- 232+ services catalog
-- price_beli_putus: Rp 350K per page (base)
-- price_sewa_monthly: Rp 150K per page (base)
```

#### 7.2 service_categories
```sql
CREATE TABLE service_categories (
    category_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    icon VARCHAR(255),
    sort_order INT UNSIGNED DEFAULT 0,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_slug (slug),
    INDEX idx_is_active (is_active)
) ENGINE=InnoDB;

-- NOTES:
-- 53 kategori bisnis
-- E-commerce, Food & Beverage, Services, Property, dll
```

#### 7.3 service_divisions
```sql
CREATE TABLE service_divisions (
    division_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    icon VARCHAR(255),
    color VARCHAR(20), -- Hex color for UI
    sort_order INT UNSIGNED DEFAULT 0,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_slug (slug),
    INDEX idx_is_active (is_active)
) ENGINE=InnoDB;

-- NOTES:
-- 10 divisi layanan
-- Website & Sistem, Digital Marketing, Automation, dll
```

#### 7.4 packages
```sql
CREATE TABLE packages (
    package_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    price_beli_putus DECIMAL(12,2) NOT NULL,
    price_sewa_monthly DECIMAL(12,2) NOT NULL,
    included_services TEXT, -- JSON array of service_ids
    features TEXT, -- JSON array
    pages_included INT UNSIGNED DEFAULT 1,
    is_popular BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    discount_percentage DECIMAL(5,2) DEFAULT 0.00,
    sort_order INT UNSIGNED DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_slug (slug),
    INDEX idx_is_active (is_active),
    INDEX idx_is_popular (is_popular)
) ENGINE=InnoDB;

-- NOTES:
-- Bundle packages: Starter, Business, Premium
-- Multiple services dalam 1 package
```

#### 7.5 service_addons
```sql
CREATE TABLE service_addons (
    addon_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    service_id INT UNSIGNED NULL, -- NULL = general addon
    name VARCHAR(200) NOT NULL,
    description TEXT,
    price_beli_putus DECIMAL(10,2) DEFAULT 0.00,
    price_sewa_monthly DECIMAL(10,2) DEFAULT 0.00,
    type ENUM('feature', 'support', 'extension', 'integration', 'other') DEFAULT 'feature',
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (service_id) REFERENCES services(service_id) ON DELETE CASCADE,
    INDEX idx_service (service_id),
    INDEX idx_type (type),
    INDEX idx_is_active (is_active)
) ENGINE=InnoDB;

-- NOTES:
-- Addon/upsell untuk services
-- E.g: SSL Certificate, Premium Support, Extra Revisions
```

#### 7.6 service_reviews
```sql
CREATE TABLE service_reviews (
    review_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    service_id INT UNSIGNED NOT NULL,
    client_id INT UNSIGNED NOT NULL,
    order_id INT UNSIGNED NULL,
    rating INT UNSIGNED NOT NULL, -- 1-5
    title VARCHAR(200),
    comment TEXT,
    pros TEXT,
    cons TEXT,
    is_verified_purchase BOOLEAN DEFAULT FALSE,
    is_approved BOOLEAN DEFAULT FALSE,
    approved_by INT UNSIGNED NULL,
    approved_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (service_id) REFERENCES services(service_id) ON DELETE CASCADE,
    FOREIGN KEY (client_id) REFERENCES clients(client_id) ON DELETE CASCADE,
    FOREIGN KEY (order_id) REFERENCES client_orders(order_id) ON DELETE SET NULL,
    FOREIGN KEY (approved_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_service (service_id),
    INDEX idx_client (client_id),
    INDEX idx_rating (rating),
    INDEX idx_is_approved (is_approved)
) ENGINE=InnoDB;

-- NOTES:
-- Client review services
-- Admin approve/reject reviews
```

#### 7.7 service_faqs
```sql
CREATE TABLE service_faqs (
    faq_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    service_id INT UNSIGNED NULL, -- NULL = general FAQ
    question TEXT NOT NULL,
    answer TEXT NOT NULL,
    sort_order INT UNSIGNED DEFAULT 0,
    is_active BOOLEAN DEFAULT TRUE,
    view_count INT UNSIGNED DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (service_id) REFERENCES services(service_id) ON DELETE CASCADE,
    INDEX idx_service (service_id),
    INDEX idx_is_active (is_active)
) ENGINE=InnoDB;

-- NOTES:
-- FAQ untuk each service atau general
```

---

### CATEGORY 8: DEMO TABLES (3 Tables)

#### 8.1 demo_requests
```sql
CREATE TABLE demo_requests (
    demo_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    client_id INT UNSIGNED NOT NULL,
    partner_id INT UNSIGNED NULL,
    demo_number VARCHAR(50) UNIQUE NOT NULL, -- DEMO-20251118-0001
    
    -- 26 FIELDS (8 Sections)
    -- SECTION 1: BUSINESS INFO (4 fields)
    field_01_nama_bisnis VARCHAR(200) NOT NULL,
    field_02_jenis_usaha VARCHAR(200) NOT NULL,
    field_03_target_market TEXT,
    field_04_lokasi VARCHAR(200),
    
    -- SECTION 2: EXISTING ASSETS (3 fields)
    field_05_website_existing VARCHAR(255),
    field_06_logo_existing VARCHAR(255), -- URL or upload path
    field_07_domain_existing VARCHAR(255),
    
    -- SECTION 3: BUDGET & TIMELINE (3 fields)
    field_08_budget VARCHAR(100),
    field_09_timeline VARCHAR(100),
    field_10_deadline_launch DATE,
    
    -- SECTION 4: FEATURES (7 fields)
    field_11_fitur_utama TEXT,
    field_12_jumlah_halaman INT UNSIGNED DEFAULT 1,
    field_13_bahasa ENUM('id', 'en', 'id_en') DEFAULT 'id',
    field_14_payment_gateway BOOLEAN DEFAULT FALSE,
    field_15_mobile_app BOOLEAN DEFAULT FALSE,
    field_16_seo_priority BOOLEAN DEFAULT FALSE,
    field_17_email_marketing BOOLEAN DEFAULT FALSE,
    
    -- SECTION 5: DESIGN (3 fields)
    field_18_referensi_website TEXT,
    field_19_warna_brand VARCHAR(200),
    field_20_konten_ready ENUM('ya', 'tidak', 'sebagian') DEFAULT 'tidak',
    
    -- SECTION 6: TECHNICAL (3 fields)
    field_21_hosting_preference VARCHAR(100),
    field_22_social_media TEXT,
    field_23_competitor_websites TEXT,
    
    -- SECTION 7: ADDITIONAL (3 fields)
    field_24_special_request TEXT,
    field_25_unique_selling_point TEXT,
    field_26_additional_notes TEXT,
    
    -- STATUS & METADATA
    status ENUM('pending', 'in_review', 'approved', 'rejected', 'demo_ready') DEFAULT 'pending',
    demo_url VARCHAR(255), -- URL to demo site
    admin_notes TEXT,
    reviewed_by INT UNSIGNED NULL,
    reviewed_at TIMESTAMP NULL,
    demo_sent_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (client_id) REFERENCES clients(client_id) ON DELETE CASCADE,
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE SET NULL,
    FOREIGN KEY (reviewed_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_client (client_id),
    INDEX idx_partner (partner_id),
    INDEX idx_status (status),
    INDEX idx_demo_number (demo_number)
) ENGINE=InnoDB;

-- NOTES:
-- 26 FIELDS DEMO REQUEST!
-- Client isi form lengkap
-- Admin review & create demo
-- "Copy Detail" button untuk AI prompt
```

#### 8.2 demo_websites
```sql
CREATE TABLE demo_websites (
    demo_website_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    category_id INT UNSIGNED NOT NULL,
    name VARCHAR(200) NOT NULL,
    slug VARCHAR(200) UNIQUE NOT NULL,
    business_type VARCHAR(100),
    description TEXT,
    demo_url VARCHAR(255) NOT NULL, -- situneo.my.id/demos/nama-bisnis/
    thumbnail VARCHAR(255),
    features TEXT, -- JSON array
    tech_stack TEXT, -- JSON array
    is_active BOOLEAN DEFAULT TRUE,
    is_featured BOOLEAN DEFAULT FALSE,
    view_count INT UNSIGNED DEFAULT 0,
    like_count INT UNSIGNED DEFAULT 0,
    sort_order INT UNSIGNED DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (category_id) REFERENCES service_categories(category_id) ON DELETE CASCADE,
    INDEX idx_category (category_id),
    INDEX idx_slug (slug),
    INDEX idx_is_active (is_active),
    INDEX idx_is_featured (is_featured)
) ENGINE=InnoDB;

-- NOTES:
-- 50 demo websites production-ready
-- Accessible tanpa login (public showcase)
```

#### 8.3 demo_feedback
```sql
CREATE TABLE demo_feedback (
    feedback_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    demo_request_id INT UNSIGNED NULL,
    demo_website_id INT UNSIGNED NULL,
    client_id INT UNSIGNED NULL, -- NULL if anonymous
    rating INT UNSIGNED, -- 1-5
    feedback_text TEXT,
    liked_features TEXT,
    improvement_suggestions TEXT,
    conversion_potential ENUM('low', 'medium', 'high') DEFAULT 'medium',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (demo_request_id) REFERENCES demo_requests(demo_id) ON DELETE CASCADE,
    FOREIGN KEY (demo_website_id) REFERENCES demo_websites(demo_website_id) ON DELETE CASCADE,
    FOREIGN KEY (client_id) REFERENCES clients(client_id) ON DELETE SET NULL,
    INDEX idx_demo_request (demo_request_id),
    INDEX idx_demo_website (demo_website_id),
    INDEX idx_rating (rating)
) ENGINE=InnoDB;

-- NOTES:
-- Client feedback untuk demo
-- For improvement & conversion tracking
```

---

### CATEGORY 9: LEADERBOARD TABLES (4 Tables)

#### 9.1 leaderboard_partners
```sql
CREATE TABLE leaderboard_partners (
    leaderboard_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    partner_id INT UNSIGNED NOT NULL,
    period_type ENUM('daily', 'weekly', 'monthly', 'yearly', 'all_time') NOT NULL,
    period_date DATE NOT NULL, -- Start date of period
    rank INT UNSIGNED NOT NULL,
    total_orders INT UNSIGNED DEFAULT 0,
    total_revenue DECIMAL(15,2) DEFAULT 0.00,
    total_commission DECIMAL(12,2) DEFAULT 0.00,
    total_clients INT UNSIGNED DEFAULT 0,
    performance_score DECIMAL(5,2) DEFAULT 0.00,
    calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (partner_id) REFERENCES partners(partner_id) ON DELETE CASCADE,
    INDEX idx_partner (partner_id),
    INDEX idx_period (period_type, period_date),
    INDEX idx_rank (rank),
    UNIQUE KEY unique_partner_period (partner_id, period_type, period_date)
) ENGINE=InnoDB;

-- NOTES:
-- Public leaderboard untuk partners
-- Updated daily via cron job
```

#### 9.2 leaderboard_spv
```sql
CREATE TABLE leaderboard_spv (
    leaderboard_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    spv_id INT UNSIGNED NOT NULL,
    period_type ENUM('daily', 'weekly', 'monthly', 'yearly', 'all_time') NOT NULL,
    period_date DATE NOT NULL,
    rank INT UNSIGNED NOT NULL,
    total_partners INT UNSIGNED DEFAULT 0,
    total_team_revenue DECIMAL(15,2) DEFAULT 0.00,
    arpu_value DECIMAL(12,2) DEFAULT 0.00,
    total_commission DECIMAL(12,2) DEFAULT 0.00,
    performance_score DECIMAL(5,2) DEFAULT 0.00,
    calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (spv_id) REFERENCES spv_supervisors(spv_id) ON DELETE CASCADE,
    INDEX idx_spv (spv_id),
    INDEX idx_period (period_type, period_date),
    INDEX idx_rank (rank),
    UNIQUE KEY unique_spv_period (spv_id, period_type, period_date)
) ENGINE=InnoDB;
```

#### 9.3 leaderboard_managers
```sql
CREATE TABLE leaderboard_managers (
    leaderboard_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    manager_id INT UNSIGNED NOT NULL,
    period_type ENUM('daily', 'weekly', 'monthly', 'yearly', 'all_time') NOT NULL,
    period_date DATE NOT NULL,
    rank INT UNSIGNED NOT NULL,
    total_spv INT UNSIGNED DEFAULT 0,
    total_partners INT UNSIGNED DEFAULT 0,
    total_area_revenue DECIMAL(15,2) DEFAULT 0.00,
    arpu_value DECIMAL(12,2) DEFAULT 0.00,
    total_commission DECIMAL(12,2) DEFAULT 0.00,
    performance_score DECIMAL(5,2) DEFAULT 0.00,
    calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (manager_id) REFERENCES manager_areas(manager_id) ON DELETE CASCADE,
    INDEX idx_manager (manager_id),
    INDEX idx_period (period_type, period_date),
    INDEX idx_rank (rank),
    UNIQUE KEY unique_manager_period (manager_id, period_type, period_date)
) ENGINE=InnoDB;
```

#### 9.4 leaderboard_achievements
```sql
CREATE TABLE leaderboard_achievements (
    achievement_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    user_type ENUM('partner', 'spv', 'manager') NOT NULL,
    achievement_type VARCHAR(100) NOT NULL, -- 'first_order', 'tier_up', '100_orders', etc
    achievement_name VARCHAR(200) NOT NULL,
    achievement_description TEXT,
    badge_icon VARCHAR(255),
    points INT UNSIGNED DEFAULT 0,
    achieved_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user (user_id),
    INDEX idx_type (achievement_type)
) ENGINE=InnoDB;

-- NOTES:
-- Gamification: Badges, achievements, points
-- Display in leaderboard & profile
```

---

### CATEGORY 10: NOTIFICATION TABLES (3 Tables)

#### 10.1 notifications
```sql
CREATE TABLE notifications (
    notification_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    type ENUM('info', 'success', 'warning', 'danger') DEFAULT 'info',
    category ENUM('order', 'payment', 'commission', 'task', 'system', 'other') DEFAULT 'other',
    title VARCHAR(255) NOT NULL,
    message TEXT NOT NULL,
    action_url VARCHAR(255),
    action_text VARCHAR(100),
    is_read BOOLEAN DEFAULT FALSE,
    read_at TIMESTAMP NULL,
    priority ENUM('low', 'normal', 'high', 'urgent') DEFAULT 'normal',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user (user_id),
    INDEX idx_is_read (is_read),
    INDEX idx_category (category),
    INDEX idx_created_at (created_at)
) ENGINE=InnoDB;

-- NOTES:
-- In-app notifications untuk users
-- Real-time notifications via WebSocket (optional)
```

#### 10.2 email_queue
```sql
CREATE TABLE email_queue (
    queue_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    to_email VARCHAR(255) NOT NULL,
    to_name VARCHAR(200),
    subject VARCHAR(500) NOT NULL,
    body_html TEXT NOT NULL,
    body_plain TEXT,
    template_id INT UNSIGNED NULL,
    priority ENUM('low', 'normal', 'high') DEFAULT 'normal',
    status ENUM('queued', 'sending', 'sent', 'failed') DEFAULT 'queued',
    attempts INT UNSIGNED DEFAULT 0,
    max_attempts INT UNSIGNED DEFAULT 3,
    error_message TEXT,
    sent_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    scheduled_at TIMESTAMP NULL, -- For delayed sending
    
    INDEX idx_status (status),
    INDEX idx_priority (priority),
    INDEX idx_created_at (created_at),
    INDEX idx_scheduled_at (scheduled_at)
) ENGINE=InnoDB;

-- NOTES:
-- Queue untuk email sending
-- Processed by cron job
-- Retry failed emails (max 3 attempts)
```

#### 10.3 email_templates
```sql
CREATE TABLE email_templates (
    template_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    subject VARCHAR(500) NOT NULL,
    body_html TEXT NOT NULL,
    body_plain TEXT,
    variables TEXT, -- JSON array of available variables
    category ENUM('registration', 'order', 'payment', 'commission', 'notification', 'other') DEFAULT 'other',
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_slug (slug),
    INDEX idx_category (category),
    INDEX idx_is_active (is_active)
) ENGINE=InnoDB;

-- NOTES:
-- 14+ email templates
-- Support variables: {name}, {email}, {amount}, etc
```

---

### CATEGORY 11: ANALYTICS TABLES (7 Tables)

#### 11.1 analytics_daily
```sql
CREATE TABLE analytics_daily (
    analytics_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    date DATE NOT NULL UNIQUE,
    total_users INT UNSIGNED DEFAULT 0,
    new_users INT UNSIGNED DEFAULT 0,
    active_users INT UNSIGNED DEFAULT 0,
    total_orders INT UNSIGNED DEFAULT 0,
    new_orders INT UNSIGNED DEFAULT 0,
    completed_orders INT UNSIGNED DEFAULT 0,
    cancelled_orders INT UNSIGNED DEFAULT 0,
    total_revenue DECIMAL(15,2) DEFAULT 0.00,
    total_commission DECIMAL(12,2) DEFAULT 0.00,
    total_withdrawals DECIMAL(12,2) DEFAULT 0.00,
    calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_date (date)
) ENGINE=InnoDB;

-- NOTES:
-- Daily aggregated statistics
-- Auto-calculated every midnight (cron job)
```

#### 11.2 analytics_traffic
```sql
CREATE TABLE analytics_traffic (
    traffic_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    date DATE NOT NULL,
    page_url VARCHAR(255) NOT NULL,
    page_title VARCHAR(255),
    visits INT UNSIGNED DEFAULT 0,
    unique_visitors INT UNSIGNED DEFAULT 0,
    avg_time_on_page INT UNSIGNED, -- Seconds
    bounce_rate DECIMAL(5,2), -- Percentage
    referrer_source VARCHAR(255),
    device_type ENUM('desktop', 'mobile', 'tablet', 'other') DEFAULT 'other',
    recorded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_date (date),
    INDEX idx_page (page_url),
    INDEX idx_referrer (referrer_source)
) ENGINE=InnoDB;

-- NOTES:
-- Page view tracking
-- Traffic analytics
```

#### 11.3 analytics_conversions
```sql
CREATE TABLE analytics_conversions (
    conversion_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    date DATE NOT NULL,
    conversion_type ENUM('demo_request', 'order', 'registration', 'contact') NOT NULL,
    source VARCHAR(100), -- organic, referral, direct, social
    medium VARCHAR(100), -- cpc, banner, email
    campaign VARCHAR(200),
    total_conversions INT UNSIGNED DEFAULT 0,
    total_value DECIMAL(15,2) DEFAULT 0.00,
    recorded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_date (date),
    INDEX idx_type (conversion_type),
    INDEX idx_source (source)
) ENGINE=InnoDB;

-- NOTES:
-- Conversion tracking
-- UTM parameters tracking
```

#### 11.4 analytics_revenue
```sql
CREATE TABLE analytics_revenue (
    revenue_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    date DATE NOT NULL,
    revenue_type ENUM('beli_putus', 'sewa', 'addon', 'other') NOT NULL,
    service_id INT UNSIGNED NULL,
    category_id INT UNSIGNED NULL,
    total_amount DECIMAL(15,2) DEFAULT 0.00,
    total_orders INT UNSIGNED DEFAULT 0,
    avg_order_value DECIMAL(12,2) DEFAULT 0.00,
    recorded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (service_id) REFERENCES services(service_id) ON DELETE SET NULL,
    FOREIGN KEY (category_id) REFERENCES service_categories(category_id) ON DELETE SET NULL,
    INDEX idx_date (date),
    INDEX idx_type (revenue_type),
    INDEX idx_service (service_id)
) ENGINE=InnoDB;
```

#### 11.5 analytics_retention
```sql
CREATE TABLE analytics_retention (
    retention_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    cohort_month DATE NOT NULL, -- YYYY-MM-01
    user_type ENUM('client', 'partner', 'spv', 'manager') NOT NULL,
    cohort_size INT UNSIGNED NOT NULL, -- Total users in cohort
    month_0 INT UNSIGNED DEFAULT 0, -- Active in month 0
    month_1 INT UNSIGNED DEFAULT 0,
    month_2 INT UNSIGNED DEFAULT 0,
    month_3 INT UNSIGNED DEFAULT 0,
    month_6 INT UNSIGNED DEFAULT 0,
    month_12 INT UNSIGNED DEFAULT 0,
    calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_cohort (cohort_month),
    INDEX idx_user_type (user_type),
    UNIQUE KEY unique_cohort_user_type (cohort_month, user_type)
) ENGINE=InnoDB;

-- NOTES:
-- Cohort retention analysis
-- Month-over-month retention rates
```

#### 11.6 analytics_churn
```sql
CREATE TABLE analytics_churn (
    churn_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    date DATE NOT NULL,
    user_type ENUM('client', 'partner', 'spv', 'manager') NOT NULL,
    total_churned INT UNSIGNED DEFAULT 0,
    churn_rate DECIMAL(5,2) DEFAULT 0.00, -- Percentage
    churn_reason TEXT,
    calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_date (date),
    INDEX idx_user_type (user_type),
    UNIQUE KEY unique_date_user_type (date, user_type)
) ENGINE=InnoDB;

-- NOTES:
-- Churn tracking & analysis
-- Help identify problems early
```

#### 11.7 analytics_top_performers
```sql
CREATE TABLE analytics_top_performers (
    performer_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    period DATE NOT NULL, -- YYYY-MM-01
    user_id INT UNSIGNED NOT NULL,
    user_type ENUM('partner', 'spv', 'manager') NOT NULL,
    metric_type ENUM('revenue', 'orders', 'clients', 'growth') NOT NULL,
    metric_value DECIMAL(15,2) NOT NULL,
    rank INT UNSIGNED NOT NULL,
    calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_period (period),
    INDEX idx_user (user_id),
    INDEX idx_metric (metric_type),
    INDEX idx_rank (rank)
) ENGINE=InnoDB;
```

---

### CATEGORY 12: SUPPORT TABLES (3 Tables)

#### 12.1 ticket_replies
```sql
CREATE TABLE ticket_replies (
    reply_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    ticket_id INT UNSIGNED NOT NULL,
    user_id INT UNSIGNED NOT NULL,
    message TEXT NOT NULL,
    attachments TEXT, -- JSON array of file paths
    is_internal_note BOOLEAN DEFAULT FALSE, -- Visible to admin only
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (ticket_id) REFERENCES client_support_tickets(ticket_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_ticket (ticket_id),
    INDEX idx_user (user_id),
    INDEX idx_created_at (created_at)
) ENGINE=InnoDB;

-- NOTES:
-- Replies untuk support tickets
-- Support attachments (images, files)
```

#### 12.2 contact_messages
```sql
CREATE TABLE contact_messages (
    message_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    email VARCHAR(200) NOT NULL,
    phone VARCHAR(20),
    subject VARCHAR(255),
    message TEXT NOT NULL,
    source_page VARCHAR(255),
    user_agent TEXT,
    ip_address VARCHAR(45),
    status ENUM('new', 'read', 'replied', 'spam', 'archived') DEFAULT 'new',
    replied_at TIMESTAMP NULL,
    replied_by INT UNSIGNED NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (replied_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_email (email),
    INDEX idx_status (status),
    INDEX idx_created_at (created_at)
) ENGINE=InnoDB;

-- NOTES:
-- Contact form submissions
-- From public website /contact page
```

#### 12.3 faq_general
```sql
CREATE TABLE faq_general (
    faq_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    category VARCHAR(100),
    question TEXT NOT NULL,
    answer TEXT NOT NULL,
    sort_order INT UNSIGNED DEFAULT 0,
    is_active BOOLEAN DEFAULT TRUE,
    view_count INT UNSIGNED DEFAULT 0,
    helpful_count INT UNSIGNED DEFAULT 0,
    not_helpful_count INT UNSIGNED DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_category (category),
    INDEX idx_is_active (is_active)
) ENGINE=InnoDB;

-- NOTES:
-- General FAQ (not service-specific)
-- Public FAQ page
```

---

### CATEGORY 13: SEO & MEDIA TABLES (5 Tables)

#### 13.1 seo_meta
```sql
CREATE TABLE seo_meta (
    seo_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    page_type ENUM('homepage', 'about', 'services', 'pricing', 'portfolio', 'blog', 'demo', 'custom') NOT NULL,
    page_slug VARCHAR(255) UNIQUE,
    meta_title VARCHAR(200),
    meta_description VARCHAR(500),
    meta_keywords VARCHAR(500),
    og_title VARCHAR(200),
    og_description VARCHAR(500),
    og_image VARCHAR(255),
    canonical_url VARCHAR(255),
    robots VARCHAR(100), -- index,follow or noindex,nofollow
    structured_data TEXT, -- JSON-LD
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_page_type (page_type),
    INDEX idx_page_slug (page_slug)
) ENGINE=InnoDB;

-- NOTES:
-- SEO metadata untuk semua pages
-- Dynamic per page
```

#### 13.2 seo_redirects
```sql
CREATE TABLE seo_redirects (
    redirect_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    old_url VARCHAR(255) NOT NULL,
    new_url VARCHAR(255) NOT NULL,
    redirect_type ENUM('301', '302', '307') DEFAULT '301',
    is_active BOOLEAN DEFAULT TRUE,
    hit_count INT UNSIGNED DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_old_url (old_url),
    INDEX idx_is_active (is_active)
) ENGINE=InnoDB;

-- NOTES:
-- URL redirects untuk SEO
-- 301 = Permanent, 302 = Temporary
```

#### 13.3 media_library
```sql
CREATE TABLE media_library (
    media_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    uploaded_by INT UNSIGNED NOT NULL,
    filename VARCHAR(255) NOT NULL,
    filepath VARCHAR(500) NOT NULL,
    file_type VARCHAR(100), -- image/png, application/pdf, etc
    file_size INT UNSIGNED, -- Bytes
    mime_type VARCHAR(100),
    width INT UNSIGNED, -- For images
    height INT UNSIGNED, -- For images
    alt_text VARCHAR(255),
    caption TEXT,
    description TEXT,
    folder VARCHAR(200) DEFAULT 'uncategorized',
    is_public BOOLEAN DEFAULT TRUE,
    usage_count INT UNSIGNED DEFAULT 0,
    uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (uploaded_by) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_uploaded_by (uploaded_by),
    INDEX idx_file_type (file_type),
    INDEX idx_folder (folder)
) ENGINE=InnoDB;

-- NOTES:
-- Central media library
-- Images, PDFs, documents, etc
```

#### 13.4 blog_posts
```sql
CREATE TABLE blog_posts (
    post_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    author_id INT UNSIGNED NOT NULL,
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    excerpt TEXT,
    content TEXT NOT NULL,
    featured_image VARCHAR(255),
    category VARCHAR(100),
    tags TEXT, -- JSON array
    status ENUM('draft', 'published', 'scheduled') DEFAULT 'draft',
    published_at TIMESTAMP NULL,
    view_count INT UNSIGNED DEFAULT 0,
    like_count INT UNSIGNED DEFAULT 0,
    share_count INT UNSIGNED DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (author_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_author (author_id),
    INDEX idx_slug (slug),
    INDEX idx_status (status),
    INDEX idx_published_at (published_at)
) ENGINE=InnoDB;

-- NOTES:
-- Blog system (optional)
-- For content marketing & SEO
```

#### 13.5 blog_comments
```sql
CREATE TABLE blog_comments (
    comment_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    post_id INT UNSIGNED NOT NULL,
    user_id INT UNSIGNED NULL, -- NULL if guest
    parent_comment_id INT UNSIGNED NULL, -- For nested comments
    author_name VARCHAR(200),
    author_email VARCHAR(200),
    comment_text TEXT NOT NULL,
    is_approved BOOLEAN DEFAULT FALSE,
    approved_by INT UNSIGNED NULL,
    approved_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (post_id) REFERENCES blog_posts(post_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    FOREIGN KEY (parent_comment_id) REFERENCES blog_comments(comment_id) ON DELETE CASCADE,
    FOREIGN KEY (approved_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_post (post_id),
    INDEX idx_user (user_id),
    INDEX idx_parent (parent_comment_id),
    INDEX idx_is_approved (is_approved)
) ENGINE=InnoDB;
```

---

### CATEGORY 14: SETTINGS & SYSTEM TABLES (7 Tables)

#### 14.1 system_settings
```sql
CREATE TABLE system_settings (
    setting_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    setting_key VARCHAR(100) UNIQUE NOT NULL,
    setting_value TEXT,
    setting_type ENUM('string', 'number', 'boolean', 'json', 'text') DEFAULT 'string',
    category VARCHAR(100), -- general, email, payment, commission, etc
    description TEXT,
    is_editable BOOLEAN DEFAULT TRUE,
    updated_by INT UNSIGNED NULL,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (updated_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_key (setting_key),
    INDEX idx_category (category)
) ENGINE=InnoDB;

-- NOTES:
-- System-wide settings
-- Examples: site_name, contact_email, commission_tiers, etc
```

#### 14.2 rate_limits
```sql
CREATE TABLE rate_limits (
    limit_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    identifier VARCHAR(100) NOT NULL, -- IP or user_id
    identifier_type ENUM('ip', 'user') NOT NULL,
    action VARCHAR(100) NOT NULL, -- login, register, contact, api_call, etc
    attempts INT UNSIGNED DEFAULT 0,
    window_start TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    locked_until TIMESTAMP NULL,
    
    INDEX idx_identifier (identifier, action),
    INDEX idx_locked_until (locked_until)
) ENGINE=InnoDB;

-- NOTES:
-- Rate limiting untuk security
-- Login: 5 attempts / 15 min
-- API: 100 requests / hour
```

#### 14.3 activity_logs
```sql
CREATE TABLE activity_logs (
    log_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NULL,
    action VARCHAR(100) NOT NULL,
    entity_type VARCHAR(100), -- order, user, payment, etc
    entity_id INT UNSIGNED,
    description TEXT,
    ip_address VARCHAR(45),
    user_agent TEXT,
    request_data TEXT, -- JSON
    response_data TEXT, -- JSON
    severity ENUM('info', 'warning', 'error', 'critical') DEFAULT 'info',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_user (user_id),
    INDEX idx_action (action),
    INDEX idx_entity (entity_type, entity_id),
    INDEX idx_created_at (created_at)
) ENGINE=InnoDB;

-- NOTES:
-- Audit trail untuk semua actions
-- Security & compliance
```

#### 14.4 cron_jobs
```sql
CREATE TABLE cron_jobs (
    job_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    job_name VARCHAR(100) UNIQUE NOT NULL,
    schedule VARCHAR(100) NOT NULL, -- Cron expression: 0 0 * * *
    command TEXT NOT NULL,
    description TEXT,
    is_active BOOLEAN DEFAULT TRUE,
    last_run_at TIMESTAMP NULL,
    last_run_status ENUM('success', 'failed', 'running') DEFAULT NULL,
    last_run_duration INT UNSIGNED, -- Seconds
    last_error_message TEXT,
    next_run_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_job_name (job_name),
    INDEX idx_is_active (is_active),
    INDEX idx_next_run (next_run_at)
) ENGINE=InnoDB;

-- NOTES:
-- Scheduled jobs tracking
-- Examples:
--   - tier-update.php (monthly, 1st 01:00)
--   - arpu-calculate.php (monthly, 1st 02:00)
--   - invoice-generate.php (monthly, recurring)
--   - email-queue-process.php (every 5 min)
```

#### 14.5 api_keys
```sql
CREATE TABLE api_keys (
    key_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    key_name VARCHAR(100) NOT NULL,
    api_key VARCHAR(255) UNIQUE NOT NULL,
    api_secret VARCHAR(255),
    permissions TEXT, -- JSON array
    rate_limit INT UNSIGNED DEFAULT 1000, -- Requests per hour
    is_active BOOLEAN DEFAULT TRUE,
    last_used_at TIMESTAMP NULL,
    expires_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user (user_id),
    INDEX idx_api_key (api_key),
    INDEX idx_is_active (is_active)
) ENGINE=InnoDB;

-- NOTES:
-- API keys untuk third-party integrations
-- Optional feature (Phase 2)
```

#### 14.6 backups
```sql
CREATE TABLE backups (
    backup_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    backup_name VARCHAR(200) NOT NULL,
    backup_type ENUM('database', 'files', 'full') NOT NULL,
    file_path VARCHAR(500),
    file_size BIGINT UNSIGNED, -- Bytes
    backup_method ENUM('manual', 'automatic') DEFAULT 'automatic',
    created_by INT UNSIGNED NULL,
    status ENUM('in_progress', 'completed', 'failed') DEFAULT 'in_progress',
    error_message TEXT,
    started_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP NULL,
    
    FOREIGN KEY (created_by) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_backup_type (backup_type),
    INDEX idx_status (status),
    INDEX idx_started_at (started_at)
) ENGINE=InnoDB;

-- NOTES:
-- Backup tracking
-- Auto-backup daily (via cron)
```

#### 14.7 error_logs
```sql
CREATE TABLE error_logs (
    error_id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    error_type VARCHAR(100),
    error_message TEXT,
    error_code VARCHAR(50),
    file_path VARCHAR(500),
    line_number INT UNSIGNED,
    stack_trace TEXT,
    request_url TEXT,
    request_method VARCHAR(10),
    user_id INT UNSIGNED NULL,
    ip_address VARCHAR(45),
    user_agent TEXT,
    severity ENUM('debug', 'info', 'warning', 'error', 'critical') DEFAULT 'error',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_error_type (error_type),
    INDEX idx_severity (severity),
    INDEX idx_created_at (created_at)
) ENGINE=InnoDB;

-- NOTES:
-- PHP errors, exceptions, warnings
-- For debugging & monitoring
```

---

## 🎯 TOTAL TABLES: 85 TABLES

### Summary by Category:
```
1. User Management: 6 tables ✅
2. Client Tables: 6 tables ✅
3. Partner Tables: 10 tables ✅
4. SPV Tables: 8 tables ✅
5. Manager Tables: 8 tables ✅
6. Hierarchy: 4 tables ✅
7. Service Tables: 7 tables ✅
8. Demo Tables: 3 tables ✅
9. Leaderboard: 4 tables ✅
10. Notification: 3 tables ✅
11. Analytics: 7 tables ✅
12. Support: 3 tables ✅
13. SEO & Media: 5 tables ✅
14. Settings & System: 7 tables ✅

TOTAL: 85 TABLES ✅
```

---

## 📚 TECHNICAL STACK

### Backend
```
- PHP 7.4+
- MySQL 8.0 (InnoDB engine)
- PDO for database (prepared statements)
- Composer for dependencies
```

### Frontend
```
- HTML5
- CSS3 (Bootstrap 5.3.3)
- JavaScript (ES6+)
- jQuery 3.6+
- AJAX for dynamic content
```

### Libraries & Tools
```
- PHPMailer (email sending)
- TCPDF or FPDF (PDF generation)
- Chart.js (charts & graphs)
- DataTables (table sorting/filtering)
- Select2 (dropdown enhancement)
- SweetAlert2 (beautiful alerts)
- Moment.js (date/time formatting)
- AOS (Animate On Scroll)
- GSAP (animations)
```

### Security
```
- bcrypt password hashing (cost 12)
- CSRF protection (tokens)
- XSS prevention (htmlspecialchars)
- SQL injection prevention (PDO prepared statements)
- Rate limiting (login, API)
- Session security (httponly, secure, samesite)
- Input validation & sanitization
```

---

## ✅ NEXT: MASTER BATCH 03
Rekap selanjutnya akan cover:
- File Structure (400+ files)
- Dashboard Details (5 roles)
- Authentication Flow
- Commission Calculation Logic
- ARPU Bonus Calculation
- Cron Jobs Setup

---

**END OF MASTER BATCH 02 - PART 2**  
*Total: 85 tables complete with detailed schema*  
*Versi 3.0 Final - 18 November 2025*

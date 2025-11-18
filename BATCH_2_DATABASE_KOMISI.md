# 📘 SITUNEO DIGITAL - BATCH 2
## DATABASE CONFIGURATION & SISTEM KOMISI FREELANCE

---

## 💾 DATABASE CONFIGURATION

### Credentials Database
```php
DB_HOST: localhost
DB_USER: nrrskfvk_user_situneo_digital
DB_PASS: Devin1922$
DB_NAME: nrrskfvk_situneo_digital
Status: CONNECTED ✅
```

### Database Requirements
- **PHP Version**: 7.4+ (Recommended: 8.0+)
- **MySQL Version**: 5.7+ atau MariaDB 10.3+
- **Storage**: Minimum 5GB
- **Memory**: Minimum 512MB (Recommended: 1GB+)
- **Minimum Tables**: 17+ tables
- **Recommended**: 30+ tables untuk fitur lengkap

### PHP Extensions Required
- mysqli / PDO
- GD Library (image processing)
- cURL (API calls)
- JSON
- mbstring
- OpenSSL
- Zip
- XML

---

## 🗄️ STRUKTUR DATABASE (30+ TABLES)

### 1. Users & Authentication Tables
```sql
-- Table: users
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(20),
    password VARCHAR(255) NOT NULL,
    role ENUM('admin', 'client', 'partner') DEFAULT 'client',
    tier ENUM('tier1', 'tier2', 'tier3', 'tier4') DEFAULT 'tier1',
    email_verified BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_email (email),
    INDEX idx_role (role)
);

-- Table: password_resets
CREATE TABLE password_resets (
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) NOT NULL,
    token VARCHAR(255) NOT NULL,
    expires_at TIMESTAMP NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Table: sessions
CREATE TABLE sessions (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    session_token VARCHAR(255) NOT NULL,
    ip_address VARCHAR(50),
    user_agent TEXT,
    expires_at TIMESTAMP NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

### 2. Partners & Clients Tables
```sql
-- Table: partners
CREATE TABLE partners (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    nik VARCHAR(20),
    portfolio_url TEXT,
    skills TEXT,
    experience_years INT,
    education VARCHAR(255),
    bank_account VARCHAR(50),
    bank_name VARCHAR(100),
    npwp VARCHAR(20),
    emergency_contact VARCHAR(20),
    motivation_letter TEXT,
    status ENUM('pending', 'approved', 'rejected', 'suspended') DEFAULT 'pending',
    tier ENUM('tier1', 'tier2', 'tier3', 'tier4') DEFAULT 'tier1',
    commission_rate DECIMAL(5,2) DEFAULT 30.00,
    total_orders INT DEFAULT 0,
    total_earnings DECIMAL(15,2) DEFAULT 0.00,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_status (status),
    INDEX idx_tier (tier)
);

-- Table: clients
CREATE TABLE clients (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    company_name VARCHAR(255),
    industry VARCHAR(100),
    website_url VARCHAR(255),
    address TEXT,
    city VARCHAR(100),
    project_count INT DEFAULT 0,
    total_spent DECIMAL(15,2) DEFAULT 0.00,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

### 3. Services & Divisions Tables
```sql
-- Table: divisions
CREATE TABLE divisions (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    icon VARCHAR(50),
    color VARCHAR(20),
    description TEXT,
    total_services INT DEFAULT 0,
    is_active BOOLEAN DEFAULT TRUE,
    order_number INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- Table: services
CREATE TABLE services (
    id INT PRIMARY KEY AUTO_INCREMENT,
    division_id INT NOT NULL,
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE,
    description TEXT,
    price_start DECIMAL(15,2),
    price_monthly DECIMAL(15,2),
    price_type ENUM('one_time', 'monthly', 'both') DEFAULT 'both',
    image_url VARCHAR(255),
    features TEXT,
    is_active BOOLEAN DEFAULT TRUE,
    is_popular BOOLEAN DEFAULT FALSE,
    order_number INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (division_id) REFERENCES divisions(id) ON DELETE CASCADE,
    INDEX idx_slug (slug),
    INDEX idx_active (is_active)
);

-- Table: service_templates
CREATE TABLE service_templates (
    id INT PRIMARY KEY AUTO_INCREMENT,
    service_id INT NOT NULL,
    template_name VARCHAR(255) NOT NULL,
    template_style VARCHAR(100),
    template_description TEXT,
    price_adjustment DECIMAL(15,2) DEFAULT 0.00,
    image_url VARCHAR(255),
    is_active BOOLEAN DEFAULT TRUE,
    FOREIGN KEY (service_id) REFERENCES services(id) ON DELETE CASCADE
);

-- Table: service_addons
CREATE TABLE service_addons (
    id INT PRIMARY KEY AUTO_INCREMENT,
    service_id INT,
    addon_name VARCHAR(255) NOT NULL,
    addon_description TEXT,
    price DECIMAL(15,2) NOT NULL,
    price_type ENUM('one_time', 'monthly') DEFAULT 'one_time',
    is_active BOOLEAN DEFAULT TRUE,
    FOREIGN KEY (service_id) REFERENCES services(id) ON DELETE CASCADE
);
```

### 4. Orders & Projects Tables
```sql
-- Table: orders
CREATE TABLE orders (
    id INT PRIMARY KEY AUTO_INCREMENT,
    order_number VARCHAR(50) UNIQUE NOT NULL,
    user_id INT NOT NULL,
    service_id INT NOT NULL,
    partner_id INT,
    order_type ENUM('one_time', 'subscription') DEFAULT 'one_time',
    status ENUM('pending', 'confirmed', 'in_progress', 'completed', 'cancelled') DEFAULT 'pending',
    price DECIMAL(15,2) NOT NULL,
    commission DECIMAL(15,2) DEFAULT 0.00,
    commission_rate DECIMAL(5,2) DEFAULT 0.00,
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    completed_at TIMESTAMP NULL,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (service_id) REFERENCES services(id) ON DELETE CASCADE,
    FOREIGN KEY (partner_id) REFERENCES partners(id) ON DELETE SET NULL,
    INDEX idx_order_number (order_number),
    INDEX idx_status (status)
);

-- Table: order_items
CREATE TABLE order_items (
    id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    item_type ENUM('service', 'addon', 'template') NOT NULL,
    item_id INT NOT NULL,
    item_name VARCHAR(255) NOT NULL,
    quantity INT DEFAULT 1,
    price DECIMAL(15,2) NOT NULL,
    subtotal DECIMAL(15,2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE
);

-- Table: projects
CREATE TABLE projects (
    id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    project_name VARCHAR(255) NOT NULL,
    project_description TEXT,
    progress INT DEFAULT 0,
    deadline DATE,
    status ENUM('pending', 'in_progress', 'review', 'completed', 'cancelled') DEFAULT 'pending',
    demo_url VARCHAR(255),
    live_url VARCHAR(255),
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    completed_at TIMESTAMP NULL,
    FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
    INDEX idx_status (status)
);

-- Table: project_milestones
CREATE TABLE project_milestones (
    id INT PRIMARY KEY AUTO_INCREMENT,
    project_id INT NOT NULL,
    milestone_name VARCHAR(255) NOT NULL,
    milestone_description TEXT,
    is_completed BOOLEAN DEFAULT FALSE,
    completed_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (project_id) REFERENCES projects(id) ON DELETE CASCADE
);
```

### 5. Invoices & Payments Tables
```sql
-- Table: invoices
CREATE TABLE invoices (
    id INT PRIMARY KEY AUTO_INCREMENT,
    invoice_number VARCHAR(50) UNIQUE NOT NULL,
    order_id INT NOT NULL,
    user_id INT NOT NULL,
    amount DECIMAL(15,2) NOT NULL,
    tax DECIMAL(15,2) DEFAULT 0.00,
    total_amount DECIMAL(15,2) NOT NULL,
    status ENUM('pending', 'paid', 'cancelled', 'overdue') DEFAULT 'pending',
    due_date DATE,
    paid_at TIMESTAMP NULL,
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_invoice_number (invoice_number),
    INDEX idx_status (status)
);

-- Table: payments
CREATE TABLE payments (
    id INT PRIMARY KEY AUTO_INCREMENT,
    payment_number VARCHAR(50) UNIQUE NOT NULL,
    invoice_id INT NOT NULL,
    user_id INT NOT NULL,
    amount DECIMAL(15,2) NOT NULL,
    payment_method ENUM('bank_transfer', 'e_wallet', 'credit_card', 'cash') NOT NULL,
    payment_proof VARCHAR(255),
    status ENUM('pending', 'verified', 'rejected') DEFAULT 'pending',
    verified_by INT,
    verified_at TIMESTAMP NULL,
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (invoice_id) REFERENCES invoices(id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_payment_number (payment_number),
    INDEX idx_status (status)
);
```

### 6. Commissions & Withdrawals Tables
```sql
-- Table: commissions
CREATE TABLE commissions (
    id INT PRIMARY KEY AUTO_INCREMENT,
    partner_id INT NOT NULL,
    order_id INT NOT NULL,
    amount DECIMAL(15,2) NOT NULL,
    commission_rate DECIMAL(5,2) NOT NULL,
    tier ENUM('tier1', 'tier2', 'tier3', 'tier4') NOT NULL,
    status ENUM('pending', 'approved', 'paid', 'cancelled') DEFAULT 'pending',
    paid_at TIMESTAMP NULL,
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (partner_id) REFERENCES partners(id) ON DELETE CASCADE,
    FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
    INDEX idx_partner (partner_id),
    INDEX idx_status (status)
);

-- Table: withdrawals
CREATE TABLE withdrawals (
    id INT PRIMARY KEY AUTO_INCREMENT,
    withdrawal_number VARCHAR(50) UNIQUE NOT NULL,
    partner_id INT NOT NULL,
    amount DECIMAL(15,2) NOT NULL,
    bank_account VARCHAR(50) NOT NULL,
    bank_name VARCHAR(100) NOT NULL,
    status ENUM('pending', 'approved', 'processed', 'rejected') DEFAULT 'pending',
    processed_by INT,
    processed_at TIMESTAMP NULL,
    proof_url VARCHAR(255),
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (partner_id) REFERENCES partners(id) ON DELETE CASCADE,
    INDEX idx_withdrawal_number (withdrawal_number),
    INDEX idx_status (status)
);

-- Table: tier_history
CREATE TABLE tier_history (
    id INT PRIMARY KEY AUTO_INCREMENT,
    partner_id INT NOT NULL,
    old_tier ENUM('tier1', 'tier2', 'tier3', 'tier4') NOT NULL,
    new_tier ENUM('tier1', 'tier2', 'tier3', 'tier4') NOT NULL,
    old_commission_rate DECIMAL(5,2),
    new_commission_rate DECIMAL(5,2),
    reason TEXT,
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (partner_id) REFERENCES partners(id) ON DELETE CASCADE
);
```

### 7. Support & Communication Tables
```sql
-- Table: inquiries
CREATE TABLE inquiries (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL,
    phone VARCHAR(20),
    subject VARCHAR(255) NOT NULL,
    message TEXT NOT NULL,
    status ENUM('new', 'in_progress', 'resolved', 'closed') DEFAULT 'new',
    assigned_to INT,
    resolved_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_status (status)
);

-- Table: support_tickets
CREATE TABLE support_tickets (
    id INT PRIMARY KEY AUTO_INCREMENT,
    ticket_number VARCHAR(50) UNIQUE NOT NULL,
    user_id INT NOT NULL,
    subject VARCHAR(255) NOT NULL,
    message TEXT NOT NULL,
    priority ENUM('low', 'medium', 'high', 'urgent') DEFAULT 'medium',
    status ENUM('open', 'in_progress', 'resolved', 'closed') DEFAULT 'open',
    assigned_to INT,
    resolved_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_ticket_number (ticket_number),
    INDEX idx_status (status)
);

-- Table: support_replies
CREATE TABLE support_replies (
    id INT PRIMARY KEY AUTO_INCREMENT,
    ticket_id INT NOT NULL,
    user_id INT NOT NULL,
    message TEXT NOT NULL,
    is_staff BOOLEAN DEFAULT FALSE,
    attachment_url VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (ticket_id) REFERENCES support_tickets(id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

### 8. Marketing & Content Tables
```sql
-- Table: portfolios
CREATE TABLE portfolios (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE,
    category VARCHAR(100),
    description TEXT,
    image_url VARCHAR(255),
    demo_url VARCHAR(255),
    client_name VARCHAR(255),
    project_date DATE,
    is_featured BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_slug (slug),
    INDEX idx_featured (is_featured)
);

-- Table: testimonials
CREATE TABLE testimonials (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    order_number VARCHAR(50),
    company VARCHAR(255),
    position VARCHAR(100),
    rating INT DEFAULT 5,
    review TEXT NOT NULL,
    avatar_url VARCHAR(255),
    is_featured BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_featured (is_featured)
);

-- Table: blog_posts
CREATE TABLE blog_posts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE,
    content TEXT NOT NULL,
    excerpt TEXT,
    featured_image VARCHAR(255),
    author_id INT NOT NULL,
    category VARCHAR(100),
    tags TEXT,
    views INT DEFAULT 0,
    is_published BOOLEAN DEFAULT FALSE,
    published_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (author_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_slug (slug),
    INDEX idx_published (is_published)
);
```

### 9. System & Logging Tables
```sql
-- Table: activities
CREATE TABLE activities (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    action VARCHAR(100) NOT NULL,
    description TEXT,
    ip_address VARCHAR(50),
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_user (user_id),
    INDEX idx_action (action)
);

-- Table: notifications
CREATE TABLE notifications (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    type ENUM('info', 'success', 'warning', 'error') DEFAULT 'info',
    title VARCHAR(255) NOT NULL,
    message TEXT NOT NULL,
    action_url VARCHAR(255),
    is_read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user (user_id),
    INDEX idx_read (is_read)
);

-- Table: settings
CREATE TABLE settings (
    id INT PRIMARY KEY AUTO_INCREMENT,
    setting_key VARCHAR(100) UNIQUE NOT NULL,
    setting_value TEXT,
    setting_group VARCHAR(50),
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_key (setting_key)
);

-- Table: error_logs
CREATE TABLE error_logs (
    id INT PRIMARY KEY AUTO_INCREMENT,
    error_type VARCHAR(50),
    error_message TEXT,
    error_file VARCHAR(255),
    error_line INT,
    user_id INT,
    ip_address VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_type (error_type)
);
```

---

## 💰 SISTEM KOMISI FREELANCE (4 TIER)

### Overview Sistem
Sistem komisi 4 tier dengan **auto-upgrade** dan **auto-downgrade** berdasarkan performa bulanan. Perhitungan dilakukan otomatis setiap akhir bulan.

---

### TIER 1: STARTER (Komisi 30%)

**Target**: 0-10 order per bulan  
**Komisi**: 30% dari nilai order  
**Status**: Entry level untuk partner baru

**Cara Naik ke Tier 2:**
- Minimal **10 order** dalam 1 bulan
- Auto-upgrade di awal bulan berikutnya

**Contoh Perhitungan:**
```
Order ke-1: Rp 1.000.000 x 30% = Rp 300.000
Order ke-2: Rp 750.000 x 30% = Rp 225.000
Order ke-3: Rp 500.000 x 30% = Rp 150.000
Total 3 order: Rp 675.000 komisi
```

---

### TIER 2: PROFESSIONAL (Komisi 40%)

**Target**: 10-25 order per bulan  
**Komisi**: 40% dari nilai order  
**Status**: Partner dengan performa konsisten

**Cara Naik ke Tier 3:**
- Minimal **25 order** dalam 1 bulan
- Auto-upgrade di awal bulan berikutnya

**Cara Turun ke Tier 1:**
- Order di bawah **10** dalam 1 bulan
- Auto-downgrade di awal bulan berikutnya

**Contoh Perhitungan:**
```
15 order rata-rata Rp 800.000:
Total omzet: Rp 12.000.000
Komisi 40%: Rp 4.800.000
```

---

### TIER 3: EXPERT (Komisi 50%)

**Target**: 25-75 order per bulan  
**Komisi**: 50% dari nilai order  
**Status**: Partner expert dengan volume tinggi

**Cara Naik ke Tier 4:**
- Minimal **75 order** dalam 1 bulan
- Auto-upgrade di awal bulan berikutnya

**Cara Turun ke Tier 2:**
- Order di bawah **25** dalam 1 bulan
- Auto-downgrade di awal bulan berikutnya

**Cara Maintain Tier 3:**
- Order stabil **50+ per bulan**
- Tetap di Tier 3 jika tidak mencapai 75 order

**Contoh Perhitungan:**
```
50 order rata-rata Rp 1.000.000:
Total omzet: Rp 50.000.000
Komisi 50%: Rp 25.000.000
```

---

### TIER 4: MASTER (Komisi 55% = 50% + 5% Bonus)

**Target**: 75+ order per bulan  
**Komisi**: 55% dari nilai order (50% base + 5% bonus)  
**Status**: TOP partner dengan volume maksimal

**Cara Maintain Tier 4:**
- Harus **75+ order per bulan**
- Jika turun di bawah 75, auto-downgrade ke Tier 3

**Cara Turun ke Tier 3:**
- Order turun ke range **25-75** per bulan
- Auto-downgrade di awal bulan berikutnya

**Contoh Perhitungan:**
```
100 order rata-rata Rp 1.200.000:
Total omzet: Rp 120.000.000
Komisi 55%: Rp 66.000.000
```

---

## 📊 TABEL PERBANDINGAN TIER

| Tier | Target Order/Bulan | Komisi | Naik Tier | Turun Tier | Maintain |
|------|-------------------|--------|-----------|------------|----------|
| **Tier 1** | 0-10 | 30% | ≥10 order → Tier 2 | - | - |
| **Tier 2** | 10-25 | 40% | ≥25 order → Tier 3 | <10 order → Tier 1 | 10-24 order |
| **Tier 3** | 25-75 | 50% | ≥75 order → Tier 4 | <25 order → Tier 2 | 50+ order |
| **Tier 4** | 75+ | 55% | - | 25-75 order → Tier 3 | ≥75 order |

---

## 🔄 MEKANISME AUTO TIER ADJUSTMENT

### Perhitungan Tier (Setiap Akhir Bulan)
```php
// Pseudocode
function calculateTierAdjustment($partner_id, $month, $year) {
    $total_orders = getTotalOrders($partner_id, $month, $year);
    $current_tier = getCurrentTier($partner_id);
    $new_tier = $current_tier;
    
    // Upgrade Logic
    if ($current_tier == 'tier1' && $total_orders >= 10) {
        $new_tier = 'tier2';
        $commission_rate = 40;
    } elseif ($current_tier == 'tier2' && $total_orders >= 25) {
        $new_tier = 'tier3';
        $commission_rate = 50;
    } elseif ($current_tier == 'tier3' && $total_orders >= 75) {
        $new_tier = 'tier4';
        $commission_rate = 55;
    }
    
    // Downgrade Logic
    elseif ($current_tier == 'tier2' && $total_orders < 10) {
        $new_tier = 'tier1';
        $commission_rate = 30;
    } elseif ($current_tier == 'tier3' && $total_orders < 25) {
        $new_tier = 'tier2';
        $commission_rate = 40;
    } elseif ($current_tier == 'tier4' && $total_orders < 75) {
        $new_tier = 'tier3';
        $commission_rate = 50;
    }
    
    // Update tier if changed
    if ($new_tier != $current_tier) {
        updatePartnerTier($partner_id, $new_tier, $commission_rate);
        sendTierNotification($partner_id, $new_tier);
        logTierHistory($partner_id, $current_tier, $new_tier);
    }
}
```

### Notifikasi Sistem
- ✅ Email notifikasi setiap perubahan tier
- ✅ Dashboard menampilkan progress real-time
- ✅ Notifikasi warning jika mendekati downgrade
- ✅ Notifikasi congratulations untuk upgrade

### Timeline Perhitungan
- **Setiap tanggal 1**: Auto-calculate tier berdasarkan performa bulan lalu
- **Real-time progress**: Dashboard menampilkan progress order saat ini
- **Warning notification**: Notifikasi 1 minggu sebelum akhir bulan jika belum mencapai target maintain

---

## 💡 CONTOH SKENARIO TIER MOVEMENT

### Skenario 1: Partner Baru Naik Tier
**Bulan 1** (Tier 1):
- 12 order, komisi 30%
- Total: Rp 3.600.000 (dari Rp 12.000.000 omzet)
- **Result**: Auto-upgrade ke Tier 2 bulan depan ✅

**Bulan 2** (Tier 2):
- 28 order, komisi 40%
- Total: Rp 11.200.000 (dari Rp 28.000.000 omzet)
- **Result**: Auto-upgrade ke Tier 3 bulan depan ✅

### Skenario 2: Partner Maintain Tier 3
**Bulan 1** (Tier 3):
- 55 order, komisi 50%
- Total: Rp 27.500.000 (dari Rp 55.000.000 omzet)
- **Result**: Maintain Tier 3 ✅

**Bulan 2** (Tier 3):
- 60 order, komisi 50%
- Total: Rp 30.000.000 (dari Rp 60.000.000 omzet)
- **Result**: Maintain Tier 3 ✅

### Skenario 3: Partner Turun Tier
**Bulan 1** (Tier 3):
- 20 order, komisi 50%
- Total: Rp 10.000.000 (dari Rp 20.000.000 omzet)
- **Result**: Auto-downgrade ke Tier 2 bulan depan ⚠️

**Bulan 2** (Tier 2):
- 15 order, komisi 40%
- Total: Rp 6.000.000 (dari Rp 15.000.000 omzet)
- **Result**: Maintain Tier 2 ✅

---

## 📈 TRACKING & REPORTING

### Dashboard Partner
- **Progress Bar Real-Time**: Menampilkan jumlah order bulan ini vs target
- **Commission Tracker**: Total komisi bulan ini
- **Tier Status**: Tier saat ini & next tier requirements
- **Performance Chart**: Grafik performa 6 bulan terakhir
- **Withdrawal History**: Riwayat penarikan komisi

### Admin Dashboard
- Monitor performa semua partner
- Approve/reject withdrawal requests
- Manual tier adjustment (jika diperlukan)
- Export report bulanan

---

## 🔐 SECURITY & COMPLIANCE

### Data Protection
- ✅ Enkripsi data sensitif (NIK, NPWP, Bank Account)
- ✅ Secure password hashing (bcrypt)
- ✅ Session management dengan token
- ✅ Rate limiting untuk login & API

### Transaction Security
- ✅ Double-check commission calculation
- ✅ Audit trail untuk setiap transaction
- ✅ Approval workflow untuk withdrawal
- ✅ Backup otomatis setiap hari

---

**© 2025 SITUNEO DIGITAL - All Rights Reserved**  
**NIB**: 20250-9261-4570-4515-5453

---

*END OF BATCH 2*

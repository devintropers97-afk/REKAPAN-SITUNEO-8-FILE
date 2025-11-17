# 📊 SITUNEO DIGITAL - ALL DASHBOARDS
## Client, Partner, SPV, Manager & Admin Dashboards Complete

**File:** 04_DASHBOARDS_ALL.md  
**Purpose:** All 5 Dashboards - Complete Features & Pages  
**Version:** 5.0 Optimized  
**Date:** 18 November 2025

---

# 📋 TABLE OF CONTENTS

1. Client Dashboard (18+ pages)
2. Partner Dashboard (30+ pages)
3. SPV Dashboard (35+ pages)
4. Manager Dashboard (35+ pages)
5. Admin Dashboard (100+ pages - GOD MODE)

---

# 📋 SITUNEO DIGITAL - MASTER BATCH 05
## CLIENT DASHBOARD COMPLETE (18+ PAGES)

**Versi:** 3.0 FINAL  
**Tanggal:** 18 November 2025  
**Status:** Production-Ready Blueprint  
**Dependency:** Master Batch 01-04

---

## 🎯 CLIENT DASHBOARD OVERVIEW

### Dashboard Access
```
URL: /client/dashboard
Login Required: YES (role: client)
Sidebar Menu: 10 sections
Total Pages: 18+ pages
```

### Sidebar Menu Structure
```
📊 DASHBOARD (Overview)
📦 ORDERS
  ├─ Order List
  ├─ Create Order (Multi-step)
  ├─ Order Detail
  └─ Order Tracking
💳 PAYMENTS
  ├─ Payment List
  └─ Upload Payment
🧾 INVOICES
  ├─ Invoice List
  └─ View Invoice (HTML/PDF/Print)
🎬 DEMO REQUESTS
  ├─ Demo List
  └─ Request Demo (26 FIELDS!)
🎫 SUPPORT
  ├─ Ticket List
  ├─ Create Ticket
  └─ View Ticket
👤 PROFILE
  ├─ View Profile
  └─ Edit Profile
🔔 NOTIFICATIONS
🚪 LOGOUT
```

---

## 📊 1. DASHBOARD OVERVIEW

### Dashboard Stats (Top Cards)
```html
<div class="dashboard-stats">
    <!-- Total Orders -->
    <div class="stat-card">
        <div class="stat-icon orders">📦</div>
        <div class="stat-content">
            <h3><?= $stats['total_orders'] ?></h3>
            <p>Total Orders</p>
        </div>
    </div>
    
    <!-- Active Orders -->
    <div class="stat-card">
        <div class="stat-icon active">🔄</div>
        <div class="stat-content">
            <h3><?= $stats['active_orders'] ?></h3>
            <p>Active Orders</p>
        </div>
    </div>
    
    <!-- Completed Orders -->
    <div class="stat-card">
        <div class="stat-icon completed">✅</div>
        <div class="stat-content">
            <h3><?= $stats['completed_orders'] ?></h3>
            <p>Completed</p>
        </div>
    </div>
    
    <!-- Total Spent -->
    <div class="stat-card">
        <div class="stat-icon spent">💰</div>
        <div class="stat-content">
            <h3>Rp <?= number_format($stats['total_spent']) ?></h3>
            <p>Total Spent</p>
        </div>
    </div>
</div>
```

### Recent Orders Table
```html
<div class="dashboard-section">
    <h3>Recent Orders</h3>
    <table class="table">
        <thead>
            <tr>
                <th>Order #</th>
                <th>Service</th>
                <th>Amount</th>
                <th>Status</th>
                <th>Date</th>
                <th>Action</th>
            </tr>
        </thead>
        <tbody>
            <?php foreach($recent_orders as $order): ?>
            <tr>
                <td><?= $order->order_number ?></td>
                <td><?= $order->service_name ?></td>
                <td>Rp <?= number_format($order->total_amount) ?></td>
                <td>
                    <span class="badge badge-<?= $order->status_class ?>">
                        <?= $order->status_label ?>
                    </span>
                </td>
                <td><?= date('d M Y', strtotime($order->created_at)) ?></td>
                <td>
                    <a href="/client/orders/<?= $order->order_id ?>" 
                       class="btn btn-sm btn-primary">
                        View
                    </a>
                </td>
            </tr>
            <?php endforeach; ?>
        </tbody>
    </table>
</div>
```

### Quick Actions
```html
<div class="quick-actions">
    <h3>Quick Actions</h3>
    <div class="action-buttons">
        <a href="/client/orders/create" class="action-btn">
            <span class="icon">🛒</span>
            <span>Create Order</span>
        </a>
        <a href="/client/demos/request" class="action-btn">
            <span class="icon">🎬</span>
            <span>Request Demo</span>
        </a>
        <a href="/client/support/create" class="action-btn">
            <span class="icon">🎫</span>
            <span>Get Support</span>
        </a>
        <a href="/client/payments/upload" class="action-btn">
            <span class="icon">💳</span>
            <span>Upload Payment</span>
        </a>
    </div>
</div>
```

---

## 📦 2. ORDER MANAGEMENT

### 2.1 Order List
```html
<div class="page-header">
    <h1>My Orders</h1>
    <a href="/client/orders/create" class="btn btn-primary">
        + Create New Order
    </a>
</div>

<!-- Filters -->
<div class="filters">
    <select name="status" class="form-control">
        <option value="">All Status</option>
        <option value="pending">Pending</option>
        <option value="in_progress">In Progress</option>
        <option value="completed">Completed</option>
        <option value="cancelled">Cancelled</option>
    </select>
    
    <select name="order_type" class="form-control">
        <option value="">All Types</option>
        <option value="beli_putus">Beli Putus</option>
        <option value="sewa">Sewa</option>
    </select>
    
    <input type="text" name="search" placeholder="Search order number..."
           class="form-control">
    
    <button class="btn btn-secondary">Filter</button>
</div>

<!-- Orders Table -->
<table class="table" id="ordersTable">
    <thead>
        <tr>
            <th>Order #</th>
            <th>Service</th>
            <th>Type</th>
            <th>Pages</th>
            <th>Amount</th>
            <th>Status</th>
            <th>Date</th>
            <th>Action</th>
        </tr>
    </thead>
    <tbody>
        <?php foreach($orders as $order): ?>
        <tr>
            <td>
                <strong><?= $order->order_number ?></strong>
            </td>
            <td><?= $order->service_name ?></td>
            <td>
                <span class="badge badge-<?= $order->order_type === 'beli_putus' ? 'success' : 'info' ?>">
                    <?= $order->order_type === 'beli_putus' ? 'Beli Putus' : 'Sewa' ?>
                </span>
            </td>
            <td><?= $order->total_pages ?> page(s)</td>
            <td>
                <strong>Rp <?= number_format($order->final_amount) ?></strong>
            </td>
            <td>
                <span class="badge badge-<?= $order->status_class ?>">
                    <?= $order->status_label ?>
                </span>
            </td>
            <td><?= date('d M Y', strtotime($order->created_at)) ?></td>
            <td>
                <div class="btn-group">
                    <a href="/client/orders/<?= $order->order_id ?>" 
                       class="btn btn-sm btn-primary">
                        View
                    </a>
                    <?php if($order->status === 'pending' && !$order->payment_verified): ?>
                    <a href="/client/payments/upload?order=<?= $order->order_id ?>" 
                       class="btn btn-sm btn-success">
                        Upload Payment
                    </a>
                    <?php endif; ?>
                </div>
            </td>
        </tr>
        <?php endforeach; ?>
    </tbody>
</table>
```

### 2.2 Create Order (Multi-Step)
```
STEP 1: Select Service/Package
STEP 2: Order Details (Pages, Type, Requirements)
STEP 3: Review & Confirm
STEP 4: Success (Show order number & payment instructions)
```

#### Step 1: Select Service
```html
<div class="step step-1 active">
    <h2>Select Service or Package</h2>
    
    <!-- Service Categories -->
    <div class="service-tabs">
        <button class="tab active" data-tab="services">
            Individual Services
        </button>
        <button class="tab" data-tab="packages">
            Bundled Packages
        </button>
    </div>
    
    <!-- Services List -->
    <div class="tab-content" id="services-tab">
        <div class="services-grid">
            <?php foreach($services as $service): ?>
            <label class="service-card">
                <input type="radio" name="service_id" 
                       value="<?= $service->service_id ?>"
                       data-price-beli="<?= $service->price_beli_putus ?>"
                       data-price-sewa="<?= $service->price_sewa_monthly ?>">
                <div class="service-content">
                    <img src="<?= $service->icon ?>" alt="">
                    <h4><?= $service->name ?></h4>
                    <p><?= $service->short_description ?></p>
                    <div class="service-price">
                        <div>
                            <strong>Beli Putus:</strong> 
                            Rp <?= number_format($service->price_beli_putus) ?>/page
                        </div>
                        <div>
                            <strong>Sewa:</strong> 
                            Rp <?= number_format($service->price_sewa_monthly) ?>/page/bulan
                        </div>
                    </div>
                </div>
            </label>
            <?php endforeach; ?>
        </div>
    </div>
    
    <!-- Packages List -->
    <div class="tab-content" id="packages-tab" style="display:none;">
        <div class="packages-grid">
            <?php foreach($packages as $package): ?>
            <label class="package-card">
                <input type="radio" name="package_id" 
                       value="<?= $package->package_id ?>"
                       data-price-beli="<?= $package->price_beli_putus ?>"
                       data-price-sewa="<?= $package->price_sewa_monthly ?>">
                <div class="package-content">
                    <h3><?= $package->name ?></h3>
                    <p><?= $package->description ?></p>
                    <ul class="package-features">
                        <?php foreach($package->features as $feature): ?>
                        <li>✅ <?= $feature ?></li>
                        <?php endforeach; ?>
                    </ul>
                    <div class="package-price">
                        <div class="price-option">
                            <strong>Beli Putus</strong>
                            <span class="price">Rp <?= number_format($package->price_beli_putus) ?></span>
                        </div>
                        <div class="price-option">
                            <strong>Sewa Bulanan</strong>
                            <span class="price">Rp <?= number_format($package->price_sewa_monthly) ?>/bulan</span>
                        </div>
                    </div>
                </div>
            </label>
            <?php endforeach; ?>
        </div>
    </div>
    
    <button type="button" class="btn btn-primary btn-lg" onclick="nextStep()">
        Next
    </button>
</div>
```

#### Step 2: Order Details
```html
<div class="step step-2">
    <h2>Order Details</h2>
    
    <!-- Order Type -->
    <div class="form-group">
        <label>Order Type *</label>
        <div class="radio-cards">
            <label class="radio-card">
                <input type="radio" name="order_type" value="beli_putus" required>
                <div class="card-content">
                    <h4>💰 Beli Putus</h4>
                    <p>One-time payment, ownership 100%</p>
                    <div class="price-display">
                        Rp <span id="price-beli">350,000</span>/page
                    </div>
                    <ul>
                        <li>✅ Website jadi milik Anda</li>
                        <li>✅ Source code lengkap</li>
                        <li>❌ Maintenance tidak included</li>
                    </ul>
                </div>
            </label>
            
            <label class="radio-card">
                <input type="radio" name="order_type" value="sewa" required>
                <div class="card-content">
                    <h4>🔄 Sewa Bulanan</h4>
                    <p>Monthly subscription, all included</p>
                    <div class="price-display">
                        Rp <span id="price-sewa">150,000</span>/page/bulan
                    </div>
                    <ul>
                        <li>✅ NO Setup Fee</li>
                        <li>✅ Domain & Hosting included</li>
                        <li>✅ Maintenance & Support 24/7</li>
                    </ul>
                </div>
            </label>
        </div>
    </div>
    
    <!-- Total Pages -->
    <div class="form-group">
        <label>Total Pages *</label>
        <div class="number-input">
            <button type="button" onclick="decrementPages()">-</button>
            <input type="number" name="total_pages" 
                   value="1" min="1" max="100" required
                   class="form-control" id="totalPages">
            <button type="button" onclick="incrementPages()">+</button>
        </div>
        <small>Jumlah halaman yang diinginkan</small>
    </div>
    
    <!-- Project Description -->
    <div class="form-group">
        <label>Project Description *</label>
        <textarea name="project_description" required rows="5"
                  placeholder="Jelaskan detail website yang Anda inginkan..."
                  class="form-control"></textarea>
    </div>
    
    <!-- Special Requirements -->
    <div class="form-group">
        <label>Special Requirements (Optional)</label>
        <textarea name="special_requirements" rows="3"
                  placeholder="Ada permintaan khusus? Tulis di sini..."
                  class="form-control"></textarea>
    </div>
    
    <!-- Deadline -->
    <div class="form-group">
        <label>Preferred Deadline (Optional)</label>
        <input type="date" name="deadline" 
               min="<?= date('Y-m-d', strtotime('+7 days')) ?>"
               class="form-control">
        <small>Estimasi normal: 7-14 hari kerja</small>
    </div>
    
    <!-- Price Summary (Real-time) -->
    <div class="price-summary">
        <h4>Price Summary</h4>
        <table>
            <tr>
                <td>Price per page:</td>
                <td class="text-right">
                    Rp <span id="summary-price-per-page">350,000</span>
                </td>
            </tr>
            <tr>
                <td>Total pages:</td>
                <td class="text-right">
                    <span id="summary-total-pages">1</span> page(s)
                </td>
            </tr>
            <tr class="total-row">
                <td><strong>TOTAL:</strong></td>
                <td class="text-right">
                    <strong>Rp <span id="summary-total">350,000</span></strong>
                </td>
            </tr>
        </table>
    </div>
    
    <div class="step-navigation">
        <button type="button" class="btn btn-secondary" onclick="prevStep()">
            Back
        </button>
        <button type="button" class="btn btn-primary" onclick="nextStep()">
            Next
        </button>
    </div>
</div>
```

#### Step 3: Review & Confirm
```html
<div class="step step-3">
    <h2>Review & Confirm</h2>
    
    <div class="review-section">
        <div class="review-item">
            <strong>Service/Package:</strong>
            <span id="review-service"></span>
        </div>
        <div class="review-item">
            <strong>Order Type:</strong>
            <span id="review-type"></span>
        </div>
        <div class="review-item">
            <strong>Total Pages:</strong>
            <span id="review-pages"></span>
        </div>
        <div class="review-item">
            <strong>Price per Page:</strong>
            <span id="review-price-per-page"></span>
        </div>
        <div class="review-item">
            <strong>Total Amount:</strong>
            <span id="review-total" class="text-lg text-primary"></span>
        </div>
        <div class="review-item">
            <strong>Deadline:</strong>
            <span id="review-deadline"></span>
        </div>
    </div>
    
    <div class="alert alert-info">
        💡 <strong>What happens next:</strong><br>
        1. Your order will be submitted<br>
        2. You'll receive an invoice<br>
        3. Upload payment proof<br>
        4. Admin will verify and start working<br>
        5. You'll be notified when completed
    </div>
    
    <div class="form-group">
        <label class="checkbox-block">
            <input type="checkbox" name="agree_order" required>
            I understand and agree to the 
            <a href="/terms" target="_blank">Terms & Conditions</a>
        </label>
    </div>
    
    <div class="step-navigation">
        <button type="button" class="btn btn-secondary" onclick="prevStep()">
            Back
        </button>
        <button type="submit" class="btn btn-success btn-lg">
            Submit Order
        </button>
    </div>
</div>
```

#### Step 4: Success
```html
<div class="step step-4">
    <div class="success-message">
        <div class="success-icon">✅</div>
        <h2>Order Created Successfully!</h2>
        <p>Your order number is:</p>
        <div class="order-number"><?= $order_number ?></div>
    </div>
    
    <div class="next-steps">
        <h3>Next Steps:</h3>
        <ol>
            <li>
                <strong>Download Invoice</strong><br>
                <a href="/client/invoices/<?= $invoice_id ?>" 
                   class="btn btn-primary">
                    Download Invoice
                </a>
            </li>
            <li>
                <strong>Make Payment</strong><br>
                Transfer to: <strong>BCA 2750424018</strong><br>
                Amount: <strong>Rp <?= number_format($total_amount) ?></strong>
            </li>
            <li>
                <strong>Upload Payment Proof</strong><br>
                <a href="/client/payments/upload?order=<?= $order_id ?>" 
                   class="btn btn-success">
                    Upload Now
                </a>
            </li>
        </ol>
    </div>
    
    <div class="action-buttons">
        <a href="/client/orders/<?= $order_id ?>" class="btn btn-primary">
            View Order Details
        </a>
        <a href="/client/dashboard" class="btn btn-secondary">
            Back to Dashboard
        </a>
    </div>
</div>
```

---

## 🎬 3. DEMO REQUEST (26 FIELDS!)

### Demo Request Form Structure
```
SECTION 1: Business Info (4 fields)
SECTION 2: Existing Assets (3 fields)
SECTION 3: Budget & Timeline (3 fields)
SECTION 4: Features (7 fields)
SECTION 5: Design (3 fields)
SECTION 6: Technical (3 fields)
SECTION 7: Additional (3 fields)
SECTION 8: Review & Submit
```

### Demo Request Form HTML
```html
<form id="demoRequestForm" method="POST" action="/client/demos/submit">
    <input type="hidden" name="csrf_token" value="<?= csrf_token() ?>">
    
    <!-- Progress Indicator -->
    <div class="progress-indicator">
        <div class="progress-step active" data-step="1">1. Business Info</div>
        <div class="progress-step" data-step="2">2. Assets</div>
        <div class="progress-step" data-step="3">3. Budget</div>
        <div class="progress-step" data-step="4">4. Features</div>
        <div class="progress-step" data-step="5">5. Design</div>
        <div class="progress-step" data-step="6">6. Technical</div>
        <div class="progress-step" data-step="7">7. Additional</div>
        <div class="progress-step" data-step="8">8. Review</div>
    </div>
    
    <!-- SECTION 1: Business Info -->
    <div class="section section-1 active">
        <h3>Business Information</h3>
        
        <!-- Field 01: Nama Bisnis -->
        <div class="form-group">
            <label>1. Nama Bisnis *</label>
            <input type="text" name="field_01_nama_bisnis" required
                   placeholder="Contoh: Toko Baju Modis Jakarta"
                   class="form-control">
        </div>
        
        <!-- Field 02: Jenis Usaha -->
        <div class="form-group">
            <label>2. Jenis Usaha *</label>
            <input type="text" name="field_02_jenis_usaha" required
                   placeholder="Contoh: E-commerce Fashion"
                   class="form-control">
        </div>
        
        <!-- Field 03: Target Market -->
        <div class="form-group">
            <label>3. Target Market *</label>
            <textarea name="field_03_target_market" required rows="3"
                      placeholder="Siapa target pelanggan Anda? (Usia, gender, lokasi, dll)"
                      class="form-control"></textarea>
        </div>
        
        <!-- Field 04: Lokasi -->
        <div class="form-group">
            <label>4. Lokasi Bisnis</label>
            <input type="text" name="field_04_lokasi"
                   placeholder="Contoh: Jakarta Barat"
                   class="form-control">
        </div>
        
        <button type="button" class="btn btn-primary" onclick="nextSection()">
            Next Section
        </button>
    </div>
    
    <!-- SECTION 2: Existing Assets -->
    <div class="section section-2">
        <h3>Existing Assets</h3>
        
        <!-- Field 05: Website Existing -->
        <div class="form-group">
            <label>5. Website yang Sudah Ada (jika ada)</label>
            <input type="url" name="field_05_website_existing"
                   placeholder="https://example.com"
                   class="form-control">
        </div>
        
        <!-- Field 06: Logo Existing -->
        <div class="form-group">
            <label>6. Logo yang Sudah Ada (Upload atau URL)</label>
            <input type="file" name="field_06_logo_file" 
                   accept="image/*"
                   class="form-control">
            <small>Atau paste URL logo:</small>
            <input type="url" name="field_06_logo_url"
                   placeholder="https://example.com/logo.png"
                   class="form-control mt-2">
        </div>
        
        <!-- Field 07: Domain Existing -->
        <div class="form-group">
            <label>7. Domain yang Sudah Dimiliki (jika ada)</label>
            <input type="text" name="field_07_domain_existing"
                   placeholder="Contoh: tokobajumodis.com"
                   class="form-control">
        </div>
        
        <div class="section-navigation">
            <button type="button" class="btn btn-secondary" onclick="prevSection()">
                Back
            </button>
            <button type="button" class="btn btn-primary" onclick="nextSection()">
                Next Section
            </button>
        </div>
    </div>
    
    <!-- SECTION 3: Budget & Timeline -->
    <div class="section section-3">
        <h3>Budget & Timeline</h3>
        
        <!-- Field 08: Budget -->
        <div class="form-group">
            <label>8. Budget yang Tersedia *</label>
            <select name="field_08_budget" required class="form-control">
                <option value="">Pilih Range Budget</option>
                <option value="< Rp 5 juta">< Rp 5 juta</option>
                <option value="Rp 5-10 juta">Rp 5-10 juta</option>
                <option value="Rp 10-20 juta">Rp 10-20 juta</option>
                <option value="> Rp 20 juta">> Rp 20 juta</option>
            </select>
        </div>
        
        <!-- Field 09: Timeline -->
        <div class="form-group">
            <label>9. Timeline yang Diharapkan *</label>
            <select name="field_09_timeline" required class="form-control">
                <option value="">Pilih Timeline</option>
                <option value="Urgent (< 1 minggu)">Urgent (< 1 minggu)</option>
                <option value="Normal (1-2 minggu)">Normal (1-2 minggu)</option>
                <option value="Flexible (> 2 minggu)">Flexible (> 2 minggu)</option>
            </select>
        </div>
        
        <!-- Field 10: Deadline Launch -->
        <div class="form-group">
            <label>10. Target Launch Date</label>
            <input type="date" name="field_10_deadline_launch"
                   min="<?= date('Y-m-d') ?>"
                   class="form-control">
        </div>
        
        <div class="section-navigation">
            <button type="button" class="btn btn-secondary" onclick="prevSection()">
                Back
            </button>
            <button type="button" class="btn btn-primary" onclick="nextSection()">
                Next Section
            </button>
        </div>
    </div>
    
    <!-- SECTION 4: Features -->
    <div class="section section-4">
        <h3>Features & Functionality</h3>
        
        <!-- Field 11: Fitur Utama -->
        <div class="form-group">
            <label>11. Fitur Utama yang Dibutuhkan *</label>
            <textarea name="field_11_fitur_utama" required rows="4"
                      placeholder="Contoh: Katalog produk, Shopping cart, Payment gateway, Member area, dll"
                      class="form-control"></textarea>
        </div>
        
        <!-- Field 12: Jumlah Halaman -->
        <div class="form-group">
            <label>12. Perkiraan Jumlah Halaman *</label>
            <input type="number" name="field_12_jumlah_halaman" 
                   required min="1" value="5"
                   class="form-control">
        </div>
        
        <!-- Field 13: Bahasa -->
        <div class="form-group">
            <label>13. Bahasa Website *</label>
            <select name="field_13_bahasa" required class="form-control">
                <option value="id">Indonesia</option>
                <option value="en">English</option>
                <option value="id_en">Indonesia & English (Dual Language)</option>
            </select>
        </div>
        
        <!-- Field 14: Payment Gateway -->
        <div class="form-group">
            <label>14. Payment Gateway</label>
            <div class="checkbox-group">
                <label>
                    <input type="checkbox" name="field_14_payment_gateway" value="1">
                    Perlu integrasi payment gateway (Midtrans, dll)
                </label>
            </div>
        </div>
        
        <!-- Field 15: Mobile App -->
        <div class="form-group">
            <label>15. Mobile App</label>
            <div class="checkbox-group">
                <label>
                    <input type="checkbox" name="field_15_mobile_app" value="1">
                    Perlu mobile app (Android/iOS)
                </label>
            </div>
        </div>
        
        <!-- Field 16: SEO Priority -->
        <div class="form-group">
            <label>16. SEO Priority</label>
            <div class="checkbox-group">
                <label>
                    <input type="checkbox" name="field_16_seo_priority" value="1">
                    SEO adalah prioritas tinggi
                </label>
            </div>
        </div>
        
        <!-- Field 17: Email Marketing -->
        <div class="form-group">
            <label>17. Email Marketing</label>
            <div class="checkbox-group">
                <label>
                    <input type="checkbox" name="field_17_email_marketing" value="1">
                    Perlu email marketing automation
                </label>
            </div>
        </div>
        
        <div class="section-navigation">
            <button type="button" class="btn btn-secondary" onclick="prevSection()">
                Back
            </button>
            <button type="button" class="btn btn-primary" onclick="nextSection()">
                Next Section
            </button>
        </div>
    </div>
    
    <!-- SECTION 5: Design -->
    <div class="section section-5">
        <h3>Design Preferences</h3>
        
        <!-- Field 18: Referensi Website -->
        <div class="form-group">
            <label>18. Referensi Website yang Disukai</label>
            <textarea name="field_18_referensi_website" rows="3"
                      placeholder="Paste URL website yang menjadi inspirasi (pisahkan dengan koma)"
                      class="form-control"></textarea>
        </div>
        
        <!-- Field 19: Warna Brand -->
        <div class="form-group">
            <label>19. Warna Brand</label>
            <input type="text" name="field_19_warna_brand"
                   placeholder="Contoh: Biru & Putih, atau #1E5C99"
                   class="form-control">
        </div>
        
        <!-- Field 20: Konten Ready -->
        <div class="form-group">
            <label>20. Kesiapan Konten (Text, Gambar) *</label>
            <select name="field_20_konten_ready" required class="form-control">
                <option value="">Pilih...</option>
                <option value="ya">Ya, semua konten sudah siap</option>
                <option value="sebagian">Sebagian sudah ada</option>
                <option value="tidak">Belum ada, butuh bantuan</option>
            </select>
        </div>
        
        <div class="section-navigation">
            <button type="button" class="btn btn-secondary" onclick="prevSection()">
                Back
            </button>
            <button type="button" class="btn btn-primary" onclick="nextSection()">
                Next Section
            </button>
        </div>
    </div>
    
    <!-- SECTION 6: Technical -->
    <div class="section section-6">
        <h3>Technical Requirements</h3>
        
        <!-- Field 21: Hosting Preference -->
        <div class="form-group">
            <label>21. Preferensi Hosting</label>
            <select name="field_21_hosting_preference" class="form-control">
                <option value="">Pilih...</option>
                <option value="SITUNEO Hosting">SITUNEO Hosting (Included)</option>
                <option value="Own Hosting">Saya punya hosting sendiri</option>
                <option value="Need Recommendation">Butuh rekomendasi</option>
            </select>
        </div>
        
        <!-- Field 22: Social Media -->
        <div class="form-group">
            <label>22. Social Media yang Dimiliki</label>
            <textarea name="field_22_social_media" rows="3"
                      placeholder="Instagram: @username&#10;Facebook: fb.com/page&#10;TikTok: @username"
                      class="form-control"></textarea>
        </div>
        
        <!-- Field 23: Competitor Websites -->
        <div class="form-group">
            <label>23. Website Kompetitor (untuk analisis)</label>
            <textarea name="field_23_competitor_websites" rows="3"
                      placeholder="URL kompetitor (pisahkan dengan koma)"
                      class="form-control"></textarea>
        </div>
        
        <div class="section-navigation">
            <button type="button" class="btn btn-secondary" onclick="prevSection()">
                Back
            </button>
            <button type="button" class="btn btn-primary" onclick="nextSection()">
                Next Section
            </button>
        </div>
    </div>
    
    <!-- SECTION 7: Additional -->
    <div class="section section-7">
        <h3>Additional Information</h3>
        
        <!-- Field 24: Special Request -->
        <div class="form-group">
            <label>24. Permintaan Khusus</label>
            <textarea name="field_24_special_request" rows="3"
                      placeholder="Ada permintaan khusus? Tulis di sini..."
                      class="form-control"></textarea>
        </div>
        
        <!-- Field 25: Unique Selling Point -->
        <div class="form-group">
            <label>25. Unique Selling Point (USP)</label>
            <textarea name="field_25_unique_selling_point" rows="3"
                      placeholder="Apa yang membedakan bisnis Anda dari kompetitor?"
                      class="form-control"></textarea>
        </div>
        
        <!-- Field 26: Additional Notes -->
        <div class="form-group">
            <label>26. Catatan Tambahan</label>
            <textarea name="field_26_additional_notes" rows="3"
                      placeholder="Catatan atau informasi lain yang perlu kami ketahui"
                      class="form-control"></textarea>
        </div>
        
        <div class="section-navigation">
            <button type="button" class="btn btn-secondary" onclick="prevSection()">
                Back
            </button>
            <button type="button" class="btn btn-primary" onclick="nextSection()">
                Review
            </button>
        </div>
    </div>
    
    <!-- SECTION 8: Review & Submit -->
    <div class="section section-8">
        <h3>Review Your Demo Request</h3>
        
        <div class="review-accordion">
            <div class="review-group">
                <h4>Business Information</h4>
                <div id="review-section-1"></div>
            </div>
            <div class="review-group">
                <h4>Assets & Budget</h4>
                <div id="review-section-2"></div>
            </div>
            <div class="review-group">
                <h4>Features & Design</h4>
                <div id="review-section-3"></div>
            </div>
            <div class="review-group">
                <h4>Technical & Additional</h4>
                <div id="review-section-4"></div>
            </div>
        </div>
        
        <div class="alert alert-info">
            💡 <strong>What happens next:</strong><br>
            1. Your demo request will be reviewed by our team<br>
            2. We'll create a custom demo based on your requirements<br>
            3. Demo will be ready in 2-5 business days<br>
            4. You'll receive email notification with demo link
        </div>
        
        <div class="form-group">
            <label class="checkbox-block">
                <input type="checkbox" name="agree_demo" required>
                I confirm all information is accurate
            </label>
        </div>
        
        <div class="section-navigation">
            <button type="button" class="btn btn-secondary" onclick="prevSection()">
                Back
            </button>
            <button type="submit" class="btn btn-success btn-lg">
                Submit Demo Request
            </button>
        </div>
    </div>
</form>

<!-- Auto-Save to localStorage -->
<script src="/assets/js/demo-request-autosave.js"></script>
```

---

## ✅ NEXT: MASTER BATCH 06-14

Batch 05 Client Dashboard sudah selesai! File sudah tersimpan.

**Mau saya lanjutkan Batch 06-14 sekarang?** 🚀

**END OF MASTER BATCH 05**  
*Client Dashboard Complete: 18+ pages*  
*Versi 3.0 Final - 18 November 2025*

---

# 📋 SITUNEO DIGITAL - MASTER BATCH 06-14 COMPLETE
## ALL DASHBOARDS, DEMOS, DESIGN, AUTOMATION & DEPLOYMENT

**Versi:** 3.0 FINAL  
**Tanggal:** 18 November 2025  
**Status:** Production-Ready Blueprint  
**Dependency:** Master Batch 01-05

---

# 🎯 BATCH 06: PARTNER DASHBOARD (30+ PAGES)

## Partner Dashboard Overview
```
Total Pages: 30+ pages
Key Features:
✅ Earnings Management (Total, Pending, Available)
✅ Tier Progression System (4 tiers, auto-update)
✅ Referral System (Link generator, QR code, Custom username)
✅ Withdrawal (Min Rp 50K, Bank Transfer & QRIS)
✅ Task Board (Available tasks from admin, Apply, Submit)
✅ Client Management (List, Detail, Performance)
✅ Public Leaderboard (Ranking, Top 3 highlight)
✅ Performance KPI (Orders, Revenue, Conversion)
```

## Sidebar Menu
```
📊 Dashboard
💰 Earnings
  ├─ Overview (Total, Breakdown)
  ├─ Commission History
  └─ Pending Commissions
🎯 Tier
  ├─ Current Tier & Progress
  ├─ Tier History
  └─ Requirements
🔗 Referrals
  ├─ Referral Link (Custom username)
  ├─ QR Code Generator
  └─ My Clients (From referrals)
💸 Withdrawals
  ├─ Request Withdrawal
  ├─ Withdrawal History
  └─ Bank Account Setup
📋 Tasks
  ├─ Available Tasks (From admin)
  ├─ My Tasks (Applied)
  ├─ Apply for Task
  └─ Submit Work
👥 Clients
  ├─ Client List
  └─ Client Detail
🏆 Leaderboard (Public ranking)
📊 Performance (KPI Dashboard)
👤 Profile
🔔 Notifications
```

## Key Features Detail

### Earnings Dashboard
```php
// Display earnings stats
$stats = [
    'commission_balance' => Partner::getBalance($partner_id), // Available
    'commission_pending' => Partner::getPending($partner_id), // From pending orders
    'commission_paid_total' => Partner::getTotalPaid($partner_id), // Withdrawn
    'current_month' => Partner::getMonthlyEarnings($partner_id),
    'last_month' => Partner::getLastMonthEarnings($partner_id)
];

// Earnings Breakdown
- Base Commission (30-55% per tier)
- Task Commission (From admin tasks)
- Bonus/Achievement
```

### Tier Progression
```
Display:
- Current Tier (1, 2, 3, MAX)
- Commission Rate (30%, 40%, 50%, 55%)
- Total Orders Lifetime
- Progress Bar to Next Tier
- Maintenance Status (TETAP BERTAHAN - never downgrade)

Tier Requirements:
Tier 1: 0-10 orders → 30%
Tier 2: 10-25 orders → 40%
Tier 3: 50-75 orders → 50%
Tier MAX: 75+ orders → 55%

CRITICAL: Once upgraded, NEVER downgrade!
```

### Referral System
```html
<!-- Referral Link Generator -->
<div class="referral-section">
    <h3>Your Referral Link</h3>
    
    <!-- Custom Username -->
    <div class="form-group">
        <label>Custom Username for Link</label>
        <div class="input-group">
            <span>situneo.my.id/register/client/</span>
            <input type="text" id="customUsername" 
                   value="<?= $partner->referral_username ?>"
                   pattern="[a-z0-9_]{5,20}">
            <button onclick="updateUsername()">Update</button>
        </div>
        <small>5-20 characters, lowercase alphanumeric + underscore</small>
    </div>
    
    <!-- Generated Link -->
    <div class="referral-link">
        <input type="text" readonly 
               value="https://situneo.my.id/register/client/<?= $partner->referral_username ?>"
               id="referralLink">
        <button onclick="copyLink()">📋 Copy</button>
        <button onclick="shareWhatsApp()">📱 Share via WhatsApp</button>
    </div>
    
    <!-- QR Code -->
    <div class="qr-code">
        <h4>QR Code</h4>
        <img src="/api/qrcode?url=<?= urlencode($referral_url) ?>" 
             alt="QR Code">
        <button onclick="downloadQR()">Download QR</button>
    </div>
    
    <!-- Stats -->
    <div class="referral-stats">
        <div class="stat">
            <strong><?= $referral_stats['total_clicks'] ?></strong>
            <span>Clicks</span>
        </div>
        <div class="stat">
            <strong><?= $referral_stats['total_registrations'] ?></strong>
            <span>Registrations</span>
        </div>
        <div class="stat">
            <strong><?= $referral_stats['total_orders'] ?></strong>
            <span>Orders from Referrals</span>
        </div>
    </div>
</div>
```

### Task Board
```html
<!-- Available Tasks from Admin -->
<div class="tasks-available">
    <h3>Available Tasks</h3>
    
    <?php foreach($available_tasks as $task): ?>
    <div class="task-card">
        <div class="task-header">
            <h4><?= $task->title ?></h4>
            <span class="task-budget">Rp <?= number_format($task->budget) ?></span>
        </div>
        <div class="task-body">
            <p><?= $task->description ?></p>
            <div class="task-meta">
                <span>⏰ Deadline: <?= date('d M Y', strtotime($task->deadline)) ?></span>
                <span>📊 Difficulty: <?= $task->difficulty ?></span>
                <span>👥 Max Workers: <?= $task->max_workers ?></span>
            </div>
        </div>
        <div class="task-footer">
            <?php if($task->can_apply): ?>
            <button class="btn btn-primary" onclick="applyTask(<?= $task->task_id ?>)">
                Apply for This Task
            </button>
            <?php else: ?>
            <span class="badge badge-secondary">Already Applied</span>
            <?php endif; ?>
        </div>
    </div>
    <?php endforeach; ?>
</div>

<!-- My Tasks -->
<div class="my-tasks">
    <h3>My Tasks</h3>
    
    <?php foreach($my_tasks as $task): ?>
    <div class="task-card">
        <div class="task-header">
            <h4><?= $task->title ?></h4>
            <span class="task-status <?= $task->status_class ?>">
                <?= $task->status_label ?>
            </span>
        </div>
        <div class="task-body">
            <p><?= $task->description ?></p>
            <div class="task-progress">
                <span>Status: <?= $task->application_status ?></span>
                <?php if($task->application_status === 'accepted'): ?>
                <span>⏰ Time remaining: <?= $task->days_remaining ?> days</span>
                <?php endif; ?>
            </div>
        </div>
        <div class="task-footer">
            <?php if($task->application_status === 'accepted' && !$task->submitted): ?>
            <button class="btn btn-success" onclick="submitWork(<?= $task->task_id ?>)">
                Submit Work
            </button>
            <?php elseif($task->submitted): ?>
            <span class="badge badge-info">Submitted, waiting review</span>
            <?php endif; ?>
        </div>
    </div>
    <?php endforeach; ?>
</div>
```

### Withdrawal
```html
<form id="withdrawalForm" method="POST">
    <h3>Request Withdrawal</h3>
    
    <!-- Available Balance -->
    <div class="balance-display">
        <label>Available Balance:</label>
        <div class="balance-amount">
            Rp <?= number_format($commission_balance) ?>
        </div>
    </div>
    
    <!-- Withdrawal Amount -->
    <div class="form-group">
        <label>Withdrawal Amount *</label>
        <input type="number" name="amount" required
               min="50000" max="<?= $commission_balance ?>"
               step="1000"
               placeholder="Minimum Rp 50,000"
               class="form-control">
        <small>Minimum withdrawal: Rp 50,000</small>
    </div>
    
    <!-- Method -->
    <div class="form-group">
        <label>Withdrawal Method *</label>
        <select name="method" required class="form-control">
            <option value="">Select Method</option>
            <option value="transfer_bank">Bank Transfer</option>
            <option value="qris">QRIS (E-wallet)</option>
        </select>
    </div>
    
    <!-- Bank Account (if transfer_bank) -->
    <div class="form-group bank-details" style="display:none;">
        <label>Select Bank Account</label>
        <select name="bank_account_id" class="form-control">
            <?php foreach($bank_accounts as $account): ?>
            <option value="<?= $account->account_id ?>">
                <?= $account->bank_name ?> - <?= $account->account_number ?>
                (<?= $account->account_holder ?>)
            </option>
            <?php endforeach; ?>
        </select>
        <a href="/partner/bank-accounts/add" class="link">+ Add New Bank Account</a>
    </div>
    
    <!-- QRIS Phone (if qris) -->
    <div class="form-group qris-details" style="display:none;">
        <label>Phone Number (for QRIS)</label>
        <input type="tel" name="qris_phone" 
               placeholder="08xxxxxxxxxx"
               class="form-control">
    </div>
    
    <!-- Notes -->
    <div class="form-group">
        <label>Notes (Optional)</label>
        <textarea name="notes" rows="2" class="form-control"></textarea>
    </div>
    
    <!-- Processing Info -->
    <div class="alert alert-info">
        💡 <strong>Processing Time:</strong><br>
        - Instant to 3 business days<br>
        - Admin will verify and process your request<br>
        - You'll receive notification once processed
    </div>
    
    <button type="submit" class="btn btn-success btn-lg">
        Submit Withdrawal Request
    </button>
</form>
```

---

# 🎯 BATCH 07: SPV DASHBOARD (35+ PAGES)

## SPV Dashboard Overview
```
Total Pages: 35+ pages
Key Features:
✅ Team Management (Partner list, performance tracking)
✅ ARPU Tracking (Real-time, 4 bonus tiers)
✅ Earnings (10% base + ARPU bonus)
✅ Referral System (SPV→Partner recruitment)
✅ Withdrawal Management
✅ Task Board (Same as Partner)
✅ Hierarchy Tree (Visual team structure)
✅ Performance Analytics
```

## Sidebar Menu
```
📊 Dashboard (Overview)
💰 Earnings
  ├─ Overview (10% + ARPU bonus)
  ├─ Commission History
  └─ Breakdown
👥 Team Management
  ├─ Partner List
  ├─ Partner Detail
  ├─ Performance Tracking
  └─ Hierarchy Tree
📈 ARPU Tracking
  ├─ Current Month ARPU
  ├─ Bonus Tier Progress
  ├─ ARPU History
  └─ Target Calculator
🔗 Referrals (SPV→Partner)
💸 Withdrawals
📋 Tasks
🏆 Leaderboard
📊 Performance
👤 Profile
```

## ARPU Bonus System (SPV)
```
ARPU = Average Revenue Per User (Monthly)
Calculated from total revenue of all partners under SPV

Bonus Tiers:
Tier 1: ARPU Rp 15,000,000   → Bonus Rp 500,000
Tier 2: ARPU Rp 35,000,000   → Bonus Rp 1,000,000
Tier 3: ARPU Rp 75,000,000   → Bonus Rp 2,000,000
Tier 4: ARPU Rp 200,000,000+ → Bonus Rp 10,000,000

ARPU Calculation:
Total Revenue This Month (from all partners) / Total Partners

Example:
SPV has 10 partners
Total revenue from 10 partners this month: Rp 50,000,000
ARPU = Rp 50,000,000 / 10 = Rp 5,000,000 per partner

BUT for bonus: use TOTAL revenue (Rp 50M)
Tier: Between Rp 35M and Rp 75M = Tier 2
Bonus: Rp 1,000,000 ✅

SPV Earnings This Month:
- Base 10%: Rp 50M × 10% = Rp 5,000,000
- ARPU Bonus Tier 2: Rp 1,000,000
- TOTAL: Rp 6,000,000 ✅
```

## ARPU Dashboard
```html
<div class="arpu-dashboard">
    <h2>ARPU Tracking - <?= date('F Y') ?></h2>
    
    <!-- Current ARPU -->
    <div class="arpu-current">
        <div class="arpu-value">
            <label>Total Revenue This Month:</label>
            <h3>Rp <?= number_format($arpu_stats['total_revenue']) ?></h3>
        </div>
        <div class="arpu-partners">
            <label>Total Active Partners:</label>
            <h4><?= $arpu_stats['total_partners'] ?></h4>
        </div>
        <div class="arpu-average">
            <label>ARPU (Average per Partner):</label>
            <h4>Rp <?= number_format($arpu_stats['arpu_value']) ?></h4>
        </div>
    </div>
    
    <!-- Bonus Tier Progress -->
    <div class="bonus-tier">
        <h3>Bonus Tier Progress</h3>
        
        <div class="tier-progress">
            <?php
            $tiers = [
                1 => ['target' => 15000000, 'bonus' => 500000],
                2 => ['target' => 35000000, 'bonus' => 1000000],
                3 => ['target' => 75000000, 'bonus' => 2000000],
                4 => ['target' => 200000000, 'bonus' => 10000000]
            ];
            
            foreach($tiers as $tier => $data):
                $achieved = $arpu_stats['total_revenue'] >= $data['target'];
                $current_tier = ($arpu_stats['total_revenue'] >= $data['target']);
            ?>
            <div class="tier-item <?= $achieved ? 'achieved' : '' ?>">
                <div class="tier-badge">
                    <?= $achieved ? '✅' : '⏳' ?>
                    Tier <?= $tier ?>
                </div>
                <div class="tier-info">
                    <strong>Target: Rp <?= number_format($data['target']) ?></strong>
                    <span>Bonus: Rp <?= number_format($data['bonus']) ?></span>
                </div>
                <?php if(!$achieved): ?>
                <div class="tier-remaining">
                    Remaining: Rp <?= number_format($data['target'] - $arpu_stats['total_revenue']) ?>
                </div>
                <?php endif; ?>
            </div>
            <?php endforeach; ?>
        </div>
        
        <!-- Current Bonus -->
        <div class="current-bonus">
            <label>Your Bonus This Month:</label>
            <h2>Rp <?= number_format($arpu_stats['bonus_amount']) ?></h2>
        </div>
    </div>
    
    <!-- ARPU History Chart -->
    <div class="arpu-history">
        <h3>ARPU History (Last 12 Months)</h3>
        <canvas id="arpuChart"></canvas>
    </div>
</div>
```

## Team Management
```html
<!-- Partner List -->
<div class="team-list">
    <h3>My Team (<?= count($partners) ?> Partners)</h3>
    
    <table class="table" id="teamTable">
        <thead>
            <tr>
                <th>Partner</th>
                <th>Join Date</th>
                <th>Tier</th>
                <th>Total Orders</th>
                <th>This Month Orders</th>
                <th>Revenue</th>
                <th>Status</th>
                <th>Action</th>
            </tr>
        </thead>
        <tbody>
            <?php foreach($partners as $partner): ?>
            <tr>
                <td>
                    <div class="partner-info">
                        <img src="<?= $partner->avatar ?>" class="avatar-sm">
                        <div>
                            <strong><?= $partner->full_name ?></strong>
                            <span><?= $partner->username ?></span>
                        </div>
                    </div>
                </td>
                <td><?= date('d M Y', strtotime($partner->registered_at)) ?></td>
                <td>
                    <span class="badge badge-tier-<?= $partner->tier_current ?>">
                        Tier <?= $partner->tier_current ?> (<?= $partner->commission_rate ?>%)
                    </span>
                </td>
                <td><?= $partner->total_orders_lifetime ?></td>
                <td><?= $partner->monthly_orders ?></td>
                <td>Rp <?= number_format($partner->monthly_revenue) ?></td>
                <td>
                    <span class="badge badge-<?= $partner->status ?>">
                        <?= ucfirst($partner->status) ?>
                    </span>
                </td>
                <td>
                    <a href="/spv/team/<?= $partner->partner_id ?>" 
                       class="btn btn-sm btn-primary">
                        View Detail
                    </a>
                </td>
            </tr>
            <?php endforeach; ?>
        </tbody>
    </table>
</div>

<!-- Hierarchy Tree -->
<div class="hierarchy-tree">
    <h3>Team Hierarchy</h3>
    <div class="tree-visual">
        <!-- SVG or D3.js visualization -->
        <div class="tree-node root">
            <div class="node-card spv">
                <img src="<?= $spv->avatar ?>">
                <strong><?= $spv->full_name ?></strong>
                <span>SPV</span>
            </div>
            <div class="tree-children">
                <?php foreach($partners as $partner): ?>
                <div class="tree-node">
                    <div class="node-card partner">
                        <img src="<?= $partner->avatar ?>">
                        <strong><?= $partner->full_name ?></strong>
                        <span>Partner - Tier <?= $partner->tier_current ?></span>
                        <span><?= $partner->total_clients ?> clients</span>
                    </div>
                </div>
                <?php endforeach; ?>
            </div>
        </div>
    </div>
</div>
```

---

# 🎯 BATCH 08: MANAGER DASHBOARD (35+ PAGES)

## Manager Dashboard Overview
```
Total Pages: 35+ pages
Key Features:
✅ Area Management (SPV list, partner overview)
✅ Area ARPU Tracking (Entire area revenue)
✅ Earnings (5% base + ARPU bonus)
✅ Referral System (Manager→SPV recruitment)
✅ Hierarchy Tree (Entire area visualization)
✅ Performance Analytics (Area-wide)
```

## ARPU Bonus System (Manager)
```
Manager ARPU = Total revenue from entire area (all SPV + Partners)

Bonus Tiers:
Tier 1: ARPU Rp 45,000,000   → Bonus Rp 1,000,000
Tier 2: ARPU Rp 105,000,000  → Bonus Rp 2,000,000
Tier 3: ARPU Rp 225,000,000  → Bonus Rp 3,000,000
Tier 4: ARPU Rp 600,000,000+ → Bonus Rp 15,000,000

Example:
Manager has 3 SPV
  - SPV 1: 10 partners, revenue Rp 50M
  - SPV 2: 15 partners, revenue Rp 70M
  - SPV 3: 8 partners, revenue Rp 30M

Total Area Revenue: Rp 150,000,000
Tier: Between Rp 105M and Rp 225M = Tier 2
Bonus: Rp 2,000,000 ✅

Manager Earnings This Month:
- Base 5%: Rp 150M × 5% = Rp 7,500,000
- ARPU Bonus Tier 2: Rp 2,000,000
- TOTAL: Rp 9,500,000 ✅
```

## Area Dashboard
```html
<div class="area-dashboard">
    <h2>Area Overview - <?= $manager->area_name ?></h2>
    
    <!-- Area Stats -->
    <div class="area-stats">
        <div class="stat-card">
            <h3><?= $area_stats['total_spv'] ?></h3>
            <p>Total SPV</p>
        </div>
        <div class="stat-card">
            <h3><?= $area_stats['total_partners'] ?></h3>
            <p>Total Partners</p>
        </div>
        <div class="stat-card">
            <h3>Rp <?= number_format($area_stats['monthly_revenue']) ?></h3>
            <p>Area Revenue This Month</p>
        </div>
        <div class="stat-card">
            <h3><?= $area_stats['total_orders'] ?></h3>
            <p>Total Orders This Month</p>
        </div>
    </div>
    
    <!-- SPV Performance Table -->
    <div class="spv-performance">
        <h3>SPV Performance</h3>
        <table class="table">
            <thead>
                <tr>
                    <th>SPV</th>
                    <th>Partners</th>
                    <th>Revenue</th>
                    <th>ARPU</th>
                    <th>ARPU Bonus</th>
                    <th>Status</th>
                </tr>
            </thead>
            <tbody>
                <?php foreach($spv_list as $spv): ?>
                <tr>
                    <td><?= $spv->full_name ?></td>
                    <td><?= $spv->total_partners ?></td>
                    <td>Rp <?= number_format($spv->monthly_revenue) ?></td>
                    <td>Rp <?= number_format($spv->arpu_current_month) ?></td>
                    <td>Rp <?= number_format($spv->arpu_bonus_amount) ?></td>
                    <td>
                        <span class="badge badge-<?= $spv->status ?>">
                            <?= ucfirst($spv->status) ?>
                        </span>
                    </td>
                </tr>
                <?php endforeach; ?>
            </tbody>
        </table>
    </div>
    
    <!-- Area Hierarchy Tree -->
    <div class="area-hierarchy">
        <h3>Complete Area Hierarchy</h3>
        <!-- D3.js tree visualization showing Manager → SPV → Partners → Clients -->
    </div>
</div>
```

---

# 🎯 BATCH 09: ADMIN DASHBOARD (GOD MODE - 100+ PAGES!)

## Admin Dashboard Overview
```
Total Pages: 100+ pages
Access Level: GOD MODE (Full Control)
Key Features:
✅ User Management (All roles: CRUD, view, suspend, delete)
✅ Hierarchy Management (Tree view, assign/reassign, orphans)
✅ Service Management (232+ services, bulk import)
✅ Order Management (All orders, assign, update status)
✅ Payment Verification (Approve/reject, proof review)
✅ Application Management (Partner/SPV/Manager approval)
✅ Commission Management (Overview, calculations, adjustments)
✅ ARPU Bonus Tracking (SPV & Manager bonuses)
✅ Withdrawal Management (Approve/reject all roles)
✅ Task Management (Post tasks, review submissions)
✅ Demo Request Management (26 fields review + "Copy Detail" button)
✅ CMS System (Pages, blog, portfolio, FAQs)
✅ Leaderboard Management
✅ Settings (General, Email, Tiers, ARPU, Security)
✅ Reports & Analytics (Revenue, users, services, retention)
✅ Activity Logs (All user actions, system events)
```

## Critical Admin Features

### 1. Demo Request Review ("Copy Detail" Button)
```html
<div class="demo-detail">
    <h2>Demo Request #<?= $demo->demo_number ?></h2>
    
    <!-- Copy Detail Button -->
    <button class="btn btn-primary btn-lg" onclick="copyDemoDetail()">
        📋 Copy Detail (for AI Prompt)
    </button>
    
    <!-- Demo Request Data (26 Fields) -->
    <div id="demoDetailContent">
        <h3>Business Information</h3>
        <p><strong>1. Nama Bisnis:</strong> <?= $demo->field_01_nama_bisnis ?></p>
        <p><strong>2. Jenis Usaha:</strong> <?= $demo->field_02_jenis_usaha ?></p>
        <p><strong>3. Target Market:</strong> <?= $demo->field_03_target_market ?></p>
        <p><strong>4. Lokasi:</strong> <?= $demo->field_04_lokasi ?></p>
        
        <h3>Existing Assets</h3>
        <p><strong>5. Website Existing:</strong> <?= $demo->field_05_website_existing ?></p>
        <p><strong>6. Logo:</strong> <?= $demo->field_06_logo_existing ?></p>
        <p><strong>7. Domain:</strong> <?= $demo->field_07_domain_existing ?></p>
        
        <h3>Budget & Timeline</h3>
        <p><strong>8. Budget:</strong> <?= $demo->field_08_budget ?></p>
        <p><strong>9. Timeline:</strong> <?= $demo->field_09_timeline ?></p>
        <p><strong>10. Deadline Launch:</strong> <?= $demo->field_10_deadline_launch ?></p>
        
        <h3>Features</h3>
        <p><strong>11. Fitur Utama:</strong> <?= $demo->field_11_fitur_utama ?></p>
        <p><strong>12. Jumlah Halaman:</strong> <?= $demo->field_12_jumlah_halaman ?></p>
        <p><strong>13. Bahasa:</strong> <?= $demo->field_13_bahasa ?></p>
        <p><strong>14. Payment Gateway:</strong> <?= $demo->field_14_payment_gateway ? 'Yes' : 'No' ?></p>
        <p><strong>15. Mobile App:</strong> <?= $demo->field_15_mobile_app ? 'Yes' : 'No' ?></p>
        <p><strong>16. SEO Priority:</strong> <?= $demo->field_16_seo_priority ? 'Yes' : 'No' ?></p>
        <p><strong>17. Email Marketing:</strong> <?= $demo->field_17_email_marketing ? 'Yes' : 'No' ?></p>
        
        <h3>Design</h3>
        <p><strong>18. Referensi Website:</strong> <?= $demo->field_18_referensi_website ?></p>
        <p><strong>19. Warna Brand:</strong> <?= $demo->field_19_warna_brand ?></p>
        <p><strong>20. Konten Ready:</strong> <?= $demo->field_20_konten_ready ?></p>
        
        <h3>Technical</h3>
        <p><strong>21. Hosting Preference:</strong> <?= $demo->field_21_hosting_preference ?></p>
        <p><strong>22. Social Media:</strong> <?= $demo->field_22_social_media ?></p>
        <p><strong>23. Competitor Websites:</strong> <?= $demo->field_23_competitor_websites ?></p>
        
        <h3>Additional</h3>
        <p><strong>24. Special Request:</strong> <?= $demo->field_24_special_request ?></p>
        <p><strong>25. Unique Selling Point:</strong> <?= $demo->field_25_unique_selling_point ?></p>
        <p><strong>26. Additional Notes:</strong> <?= $demo->field_26_additional_notes ?></p>
    </div>
    
    <div class="demo-actions">
        <button class="btn btn-success" onclick="approveDemoRequest(<?= $demo->demo_id ?>)">
            ✅ Approve & Create Demo
        </button>
        <button class="btn btn-danger" onclick="rejectDemoRequest(<?= $demo->demo_id ?>)">
            ❌ Reject
        </button>
    </div>
</div>

<script>
function copyDemoDetail() {
    const content = document.getElementById('demoDetailContent').innerText;
    navigator.clipboard.writeText(content).then(() => {
        alert('✅ Demo detail copied! Paste to AI tool.');
    });
}
</script>
```

### 2. Commission Management
```html
<div class="commission-overview">
    <h2>Commission Management</h2>
    
    <!-- Total Commissions -->
    <div class="stats-row">
        <div class="stat-card">
            <h3>Rp <?= number_format($commission_stats['total_pending']) ?></h3>
            <p>Pending Commissions</p>
        </div>
        <div class="stat-card">
            <h3>Rp <?= number_format($commission_stats['total_approved']) ?></h3>
            <p>Approved (Unpaid)</p>
        </div>
        <div class="stat-card">
            <h3>Rp <?= number_format($commission_stats['total_paid']) ?></h3>
            <p>Total Paid</p>
        </div>
    </div>
    
    <!-- Commission Breakdown -->
    <div class="commission-breakdown">
        <h3>Breakdown by Role</h3>
        <table class="table">
            <thead>
                <tr>
                    <th>Role</th>
                    <th>Total Users</th>
                    <th>Pending</th>
                    <th>Approved</th>
                    <th>Paid</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>Partners</td>
                    <td><?= $breakdown['partners']['count'] ?></td>
                    <td>Rp <?= number_format($breakdown['partners']['pending']) ?></td>
                    <td>Rp <?= number_format($breakdown['partners']['approved']) ?></td>
                    <td>Rp <?= number_format($breakdown['partners']['paid']) ?></td>
                </tr>
                <tr>
                    <td>SPV</td>
                    <td><?= $breakdown['spv']['count'] ?></td>
                    <td>Rp <?= number_format($breakdown['spv']['pending']) ?></td>
                    <td>Rp <?= number_format($breakdown['spv']['approved']) ?></td>
                    <td>Rp <?= number_format($breakdown['spv']['paid']) ?></td>
                </tr>
                <tr>
                    <td>Managers</td>
                    <td><?= $breakdown['managers']['count'] ?></td>
                    <td>Rp <?= number_format($breakdown['managers']['pending']) ?></td>
                    <td>Rp <?= number_format($breakdown['managers']['approved']) ?></td>
                    <td>Rp <?= number_format($breakdown['managers']['paid']) ?></td>
                </tr>
            </tbody>
        </table>
    </div>
</div>
```

---

# 🎯 BATCH 10: 50 DEMO WEBSITES

## Demo Website Structure
```
Location: /public/demos/[demo-name]/
Each demo: 10-15 files
Total: 500+ files for 50 demos

Structure per demo:
demos/toko-baju-modis/
├── index.php (Homepage)
├── tentang.php (About)
├── produk.php (Products)
├── kontak.php (Contact)
├── assets/
│   ├── css/style.css
│   ├── js/main.js
│   └── img/ (10+ images from Unsplash)
└── README.md (Demo documentation)
```

## TOP 50 Demo List
```
1. Toko Baju Modis Jakarta (E-commerce Fashion)
2. Restoran Nusantara Enak (Restaurant)
3. Klinik Sehat Keluarga (Healthcare)
4. Konsultan Bisnis Profesional (Consulting)
5. Kursus Online Berkualitas (Education)
6. Laundry Express Cepat (Services)
7. Properti Rumah Impian (Real Estate)
8. Cuci Mobil Premium Shine (Automotive)
9. Service Laptop Cepat Aman (Tech Services)
10. Hotel Grand Jakarta (Hospitality)
... (40 more)
```

## Demo Features
```
✅ Production-ready (fully functioning)
✅ Real business names (terlihat nyata)
✅ Perfect design (paling bagus & mahal)
✅ Mobile responsive
✅ Fast loading (<2 seconds)
✅ Unsplash images (copyright-free)
✅ Working contact forms
✅ SEO optimized
✅ Accessible tanpa login (public showcase)
```

---

# 🎯 BATCH 11: DESIGN SYSTEM

## Color Palette
```css
/* Primary Colors */
--primary-blue: #1E5C99;
--dark-blue: #0F3057;
--gold: #FFB400;
--bright-gold: #FFD700;

/* Gradients */
--gradient-primary: linear-gradient(135deg, #1E5C99 0%, #0F3057 100%);
--gradient-gold: linear-gradient(135deg, #FFD700 0%, #FFB400 100%);
--gradient-network: linear-gradient(180deg, #0F3057 0%, #1E5C99 100%);

/* Semantic Colors */
--success: #28a745;
--warning: #ffc107;
--danger: #dc3545;
--info: #17a2b8;

/* Neutrals */
--white: #ffffff;
--gray-100: #f8f9fa;
--gray-200: #e9ecef;
--gray-300: #dee2e6;
--gray-700: #495057;
--gray-900: #212529;
--black: #000000;
```

## Typography
```css
/* Font Families */
--font-body: 'Inter', sans-serif;
--font-heading: 'Plus Jakarta Sans', sans-serif;
--font-mono: 'Fira Code', monospace;

/* Font Sizes */
--text-xs: 0.75rem;    /* 12px */
--text-sm: 0.875rem;   /* 14px */
--text-base: 1rem;     /* 16px */
--text-lg: 1.125rem;   /* 18px */
--text-xl: 1.25rem;    /* 20px */
--text-2xl: 1.5rem;    /* 24px */
--text-3xl: 1.875rem;  /* 30px */
--text-4xl: 2.25rem;   /* 36px */
--text-5xl: 3rem;      /* 48px */
```

## Animations
```css
/* Network Particle Background */
canvas#networkCanvas {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
    opacity: 0.15;
}

/* Particles: 30-40 (LOW intensity) */
/* Color: var(--primary-blue) */
/* Connection lines: var(--gold) */

/* Loading Screen */
.loading-screen {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: var(--gradient-primary);
    z-index: 9999;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

.loading-logo {
    animation: pulse 2s ease-in-out infinite;
}

.loading-text {
    color: white;
    margin-top: 20px;
    font-size: var(--text-xl);
}

@keyframes pulse {
    0%, 100% { transform: scale(1); opacity: 1; }
    50% { transform: scale(1.1); opacity: 0.8; }
}

/* NIB Badge (Footer) */
.nib-badge {
    display: inline-flex;
    align-items: center;
    padding: 10px 15px;
    background: linear-gradient(135deg, #FFD700, #FFB400);
    border-radius: 8px;
    animation: badgePulse 3s ease-in-out infinite;
}

@keyframes badgePulse {
    0%, 100% { box-shadow: 0 0 0 0 rgba(255, 215, 0, 0.7); }
    50% { box-shadow: 0 0 0 10px rgba(255, 215, 0, 0); }
}

/* Scroll Animations (AOS) */
[data-aos] {
    opacity: 0;
    transition-property: opacity, transform;
}

[data-aos].aos-animate {
    opacity: 1;
}
```

---

# 🎯 BATCH 12: EMAIL TEMPLATES (14+ TYPES)

## Email Template List
```
1. Welcome Email (After Register Client)
2. Application Received (Partner/SPV/Manager)
3. Application Approved
4. Application Rejected
5. Email Verification
6. Password Reset
7. Order Created (to Client)
8. Order Assigned (to Partner)
9. Payment Verified
10. Order Completed
11. Invoice Generated
12. Commission Credited
13. Withdrawal Approved
14. Withdrawal Processed
```

## Email Template Structure
```php
// Email Template Class
class EmailTemplate {
    public static function send($template, $to, $data) {
        $subject = self::getSubject($template);
        $body = self::getBody($template, $data);
        
        return Mailer::send([
            'to' => $to,
            'subject' => $subject,
            'body' => $body
        ]);
    }
    
    private static function getBody($template, $data) {
        ob_start();
        extract($data);
        include "/app/views/emails/{$template}.php";
        return ob_get_clean();
    }
}
```

## Email Base Layout
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        body { font-family: Arial, sans-serif; margin: 0; padding: 0; background: #f4f4f4; }
        .container { max-width: 600px; margin: 20px auto; background: white; }
        .header { background: linear-gradient(135deg, #1E5C99, #0F3057); padding: 30px; text-align: center; }
        .header img { width: 150px; }
        .content { padding: 30px; }
        .footer { background: #f8f9fa; padding: 20px; text-align: center; font-size: 12px; color: #666; }
        .btn { display: inline-block; padding: 12px 30px; background: #FFB400; color: #000; text-decoration: none; border-radius: 5px; margin: 20px 0; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <img src="https://situneo.my.id/assets/img/logo-white.png" alt="SITUNEO">
        </div>
        <div class="content">
            <!-- EMAIL CONTENT HERE -->
        </div>
        <div class="footer">
            <p>PT SITUNEO DIGITAL SOLUSI INDONESIA</p>
            <p>📧 vins@situneo.my.id | 📱 +62 831-7386-8915</p>
            <p>🌐 <a href="https://situneo.my.id">situneo.my.id</a></p>
        </div>
    </div>
</body>
</html>
```

---

# 🎯 BATCH 13: CRON JOBS & AUTOMATION

## Cron Job List
```
1. tier-update.php (Monthly: 1st day 01:00 AM)
   - Check partner orders lifetime
   - Upgrade tiers (never downgrade!)
   - Update commission rates
   
2. arpu-calculate.php (Monthly: 1st day 02:00 AM)
   - Calculate SPV ARPU (from partners)
   - Calculate Manager ARPU (from entire area)
   - Determine bonus tiers
   - Credit bonus to balance
   
3. invoice-generate.php (Monthly: Recurring sewa)
   - Generate invoices for active subscriptions
   - Send to client email
   - Update next billing date
   
4. email-queue-process.php (Every 5 minutes)
   - Process queued emails
   - Retry failed emails (max 3 attempts)
   - Log email status
   
5. session-cleanup.php (Daily: 03:00 AM)
   - Delete expired sessions
   - Delete expired password reset tokens
   - Delete expired email verification tokens
   
6. backup-database.php (Daily: 04:00 AM)
   - Backup database to SQL file
   - Compress to .zip
   - Keep last 30 days
   
7. analytics-daily.php (Daily: 05:00 AM)
   - Calculate daily statistics
   - Update analytics tables
   - Generate reports
   
8. leaderboard-update.php (Daily: 06:00 AM)
   - Update partner leaderboard
   - Update SPV leaderboard
   - Update manager leaderboard
   
9. notification-digest.php (Daily: 08:00 AM)
   - Send daily notification digest
   - Summarize unread notifications
   
10. subscription-check.php (Daily: 09:00 AM)
    - Check sewa subscriptions
    - Alert clients before billing
    - Handle failed payments
```

## Cron Schedule (crontab)
```bash
# SITUNEO Digital - Cron Jobs
# Add to: crontab -e

# Tier Update (Monthly: 1st day, 01:00 AM)
0 1 1 * * /usr/bin/php /path/to/cron/tier-update.php >> /path/to/logs/cron.log 2>&1

# ARPU Calculate (Monthly: 1st day, 02:00 AM)
0 2 1 * * /usr/bin/php /path/to/cron/arpu-calculate.php >> /path/to/logs/cron.log 2>&1

# Invoice Generate (Monthly: 1st day, 03:00 AM)
0 3 1 * * /usr/bin/php /path/to/cron/invoice-generate.php >> /path/to/logs/cron.log 2>&1

# Email Queue (Every 5 minutes)
*/5 * * * * /usr/bin/php /path/to/cron/email-queue-process.php >> /path/to/logs/cron.log 2>&1

# Session Cleanup (Daily: 03:00 AM)
0 3 * * * /usr/bin/php /path/to/cron/session-cleanup.php >> /path/to/logs/cron.log 2>&1

# Database Backup (Daily: 04:00 AM)
0 4 * * * /usr/bin/php /path/to/cron/backup-database.php >> /path/to/logs/cron.log 2>&1

# Analytics Daily (Daily: 05:00 AM)
0 5 * * * /usr/bin/php /path/to/cron/analytics-daily.php >> /path/to/logs/cron.log 2>&1

# Leaderboard Update (Daily: 06:00 AM)
0 6 * * * /usr/bin/php /path/to/cron/leaderboard-update.php >> /path/to/logs/cron.log 2>&1

# Notification Digest (Daily: 08:00 AM)
0 8 * * * /usr/bin/php /path/to/cron/notification-digest.php >> /path/to/logs/cron.log 2>&1

# Subscription Check (Daily: 09:00 AM)
0 9 * * * /usr/bin/php /path/to/cron/subscription-check.php >> /path/to/logs/cron.log 2>&1
```

---

# 🎯 BATCH 14: SECURITY, TESTING & DEPLOYMENT

## Security Checklist
```
✅ HTTPS enforced (SSL Certificate)
✅ bcrypt password hashing (cost 12)
✅ CSRF protection (tokens)
✅ XSS prevention (htmlspecialchars)
✅ SQL injection prevention (PDO prepared statements)
✅ Rate limiting (login, API)
✅ Session security (httponly, secure, samesite)
✅ Input validation & sanitization
✅ File upload validation (type, size)
✅ Error logging (without exposing sensitive data)
✅ Activity logging (audit trail)
✅ Secure headers (X-Frame-Options, X-Content-Type-Options)
✅ .env file (sensitive data NOT in git)
✅ Database credentials encrypted
✅ API keys encrypted
```

## Testing Guide
```
Manual Testing:
1. Register as each role (Client, Partner, SPV, Manager)
2. Test login/logout
3. Test password reset
4. Test email verification
5. Create orders (beli putus & sewa)
6. Upload payment proofs
7. Test commission calculation
8. Test ARPU calculation
9. Test withdrawal process
10. Test demo request (26 fields)
11. Test task board
12. Test referral system
13. Test tier progression
14. Test admin approval workflows
15. Test all CRUD operations

Automated Testing (Optional):
- PHPUnit for unit tests
- Selenium for browser automation
- Load testing with Apache JMeter
```

## Deployment Checklist
```
PRE-DEPLOYMENT:
✅ Test on staging server
✅ Backup existing website (if any)
✅ Backup database
✅ Check file permissions
✅ Configure .env file
✅ Set APP_DEBUG=false
✅ Set APP_ENV=production
✅ Generate new secret keys
✅ Clear cache
✅ Test all critical paths

DEPLOYMENT:
✅ Upload files via FTP/SFTP
✅ Import database (schema.sql + seeds)
✅ Configure cron jobs
✅ Test website accessibility
✅ Test email sending (SMTP)
✅ Test SSL certificate
✅ Configure error pages (403, 404, 500, 503)
✅ Set up monitoring (uptime, errors)
✅ Configure backups (daily)

POST-DEPLOYMENT:
✅ Verify all pages load correctly
✅ Test registration flow
✅ Test order creation
✅ Test payment upload
✅ Test admin dashboard
✅ Monitor error logs
✅ Check email delivery
✅ Test on multiple devices/browsers
✅ Performance testing
✅ Security scan
```

## Go-Live Checklist
```
FINAL CHECKS:
✅ Domain pointed correctly (DNS)
✅ SSL certificate active
✅ Email templates tested
✅ Payment instructions clear
✅ Bank account details correct
✅ Contact information updated
✅ Social media links working
✅ NIB badge displayed
✅ Terms & Privacy pages complete
✅ Sitemap submitted to Google
✅ Google Analytics installed
✅ Facebook Pixel installed (optional)
✅ WhatsApp button working
✅ Demo websites accessible
✅ Public leaderboard working
✅ Cron jobs scheduled
✅ Monitoring alerts configured
✅ Team trained on admin dashboard
✅ Documentation provided
✅ Support channels ready

LAUNCH:
✅ Announce to partners
✅ Announce to clients
✅ Social media announcement
✅ Monitor first 24 hours closely
✅ Address any issues immediately
✅ Collect feedback
✅ Iterate and improve
```

---

## ✅ REKAP FINAL - SEMUA BATCH SELESAI!

```
MASTER BATCH 01: ✅ Business Model & Company Info
MASTER BATCH 02: ✅ Database (85 tables complete)
MASTER BATCH 03: ✅ File Structure (400+ files)
MASTER BATCH 04: ✅ Authentication System
MASTER BATCH 05: ✅ Client Dashboard (18+ pages)
MASTER BATCH 06: ✅ Partner Dashboard (30+ pages)
MASTER BATCH 07: ✅ SPV Dashboard (35+ pages, ARPU)
MASTER BATCH 08: ✅ Manager Dashboard (35+ pages, Area ARPU)
MASTER BATCH 09: ✅ Admin Dashboard (100+ pages, GOD MODE)
MASTER BATCH 10: ✅ 50 Demo Websites
MASTER BATCH 11: ✅ Design System (Colors, Typography, Animations)
MASTER BATCH 12: ✅ Email Templates (14+ types)
MASTER BATCH 13: ✅ Cron Jobs & Automation (10 jobs)
MASTER BATCH 14: ✅ Security, Testing & Deployment

TOTAL: 14 MASTER BATCHES COMPLETE! 🎉
```

---

**END OF MASTER BATCH 06-14 COMPLETE**  
*All remaining features documented*  
*Versi 3.0 Final - 18 November 2025*  
*Ready for Development & Deployment!* 🚀

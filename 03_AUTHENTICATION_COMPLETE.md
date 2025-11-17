# 🔐 SITUNEO DIGITAL - AUTHENTICATION COMPLETE
## Login, Register, Verification, Password Reset & Security

**File:** 03_AUTHENTICATION_COMPLETE.md  
**Purpose:** Complete Authentication System  
**Version:** 5.0 Optimized  
**Date:** 18 November 2025

---

# 📋 TABLE OF CONTENTS

1. Login System (Split Design)
2. Registration (Client & Partner)
3. Email Verification
4. Forgot Password Flow
5. Reset Password System
6. Security Features (CSRF, Rate Limit, Session)

---

# 📋 SITUNEO DIGITAL - MASTER BATCH 04
## AUTHENTICATION SYSTEM COMPLETE

**Versi:** 3.0 FINAL  
**Tanggal:** 18 November 2025  
**Status:** Production-Ready Blueprint  
**Dependency:** Master Batch 01, 02, 03

---

## 🔐 AUTHENTICATION OVERVIEW

### System Features
```
✅ Login (Split Design 2 Sections)
✅ Register Client (Simple Form)
✅ Register Partner/SPV/Manager (Multi-Step: KTP, CV, Alamat)
✅ Forgot Password
✅ Reset Password
✅ Email Verification
✅ Session Management (2 hours timeout, 30 days remember me)
✅ CSRF Protection
✅ Rate Limiting (5 attempts / 15 min)
✅ Activity Logging
✅ Multi-Language (ID & EN)
```

---

## 1️⃣ LOGIN SYSTEM (SPLIT DESIGN)

### Design Layout
```
┌──────────────────────────────────────────────────────────┐
│                    LOGIN PAGE                             │
├────────────────────┬─────────────────────────────────────┤
│                    │                                      │
│   LEFT SECTION     │       RIGHT SECTION                  │
│   (Form)           │       (Branding)                     │
│                    │                                      │
│  ┌──────────────┐  │  ╔════════════════════════════════╗ │
│  │ SITUNEO Logo │  │  ║  Network Animation Background  ║ │
│  └──────────────┘  │  ║  (Particle + Circuit)          ║ │
│                    │  ╚════════════════════════════════╝ │
│  [Email]           │                                      │
│  ________________  │  🚀 Platform Digital Agency         │
│                    │     Terlengkap di Indonesia         │
│  [Password]        │                                      │
│  ________________  │  ✅ 232+ Layanan Digital            │
│                    │  ✅ 1500+ Website Types             │
│  □ Remember Me     │  ✅ Commission 30-55%               │
│                    │  ✅ NO Setup Fee                    │
│  [Forgot Password?]│                                      │
│                    │  📊 500+ Clients                    │
│  [ LOGIN BUTTON ]  │  ⭐ 4.9/5 Rating                   │
│   (Full Width)     │                                      │
│                    │  "Bergabung dengan ribuan           │
│  ─── OR ───        │   partner sukses kami!"             │
│                    │                                      │
│  [Register Client] │                                      │
│  [Register Partner]│                                      │
│                    │                                      │
└────────────────────┴─────────────────────────────────────┘
```

### Login HTML Structure
```html
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login - SITUNEO Digital</title>
    <link rel="stylesheet" href="/assets/css/auth.css">
</head>
<body>
    <div class="auth-container split-design">
        <!-- LEFT: Login Form -->
        <div class="auth-left">
            <div class="auth-form-wrapper">
                <img src="/assets/img/logo.png" alt="SITUNEO" class="auth-logo">
                <h1>Selamat Datang Kembali</h1>
                <p>Masuk ke akun SITUNEO Anda</p>
                
                <form id="loginForm" method="POST" action="/auth/login">
                    <!-- CSRF Token -->
                    <input type="hidden" name="csrf_token" value="<?= csrf_token() ?>">
                    
                    <!-- Email -->
                    <div class="form-group">
                        <label>Email</label>
                        <input type="email" name="email" required 
                               placeholder="nama@email.com"
                               class="form-control">
                        <span class="error" id="email-error"></span>
                    </div>
                    
                    <!-- Password -->
                    <div class="form-group">
                        <label>Password</label>
                        <div class="password-wrapper">
                            <input type="password" name="password" 
                                   id="password" required 
                                   placeholder="Masukkan password"
                                   class="form-control">
                            <button type="button" class="toggle-password">
                                👁️
                            </button>
                        </div>
                        <span class="error" id="password-error"></span>
                    </div>
                    
                    <!-- Remember Me & Forgot Password -->
                    <div class="form-row">
                        <label class="checkbox-inline">
                            <input type="checkbox" name="remember_me" value="1">
                            Ingat Saya
                        </label>
                        <a href="/auth/forgot-password" class="link-forgot">
                            Lupa Password?
                        </a>
                    </div>
                    
                    <!-- Login Button -->
                    <button type="submit" class="btn btn-primary btn-block">
                        Masuk
                    </button>
                    
                    <!-- Divider -->
                    <div class="divider">
                        <span>ATAU</span>
                    </div>
                    
                    <!-- Register Links -->
                    <div class="register-links">
                        <a href="/auth/register" class="btn btn-outline">
                            Daftar Sebagai Client
                        </a>
                        <a href="/auth/register/partner" class="btn btn-outline">
                            Daftar Sebagai Partner/SPV/Manager
                        </a>
                    </div>
                </form>
            </div>
        </div>
        
        <!-- RIGHT: Branding -->
        <div class="auth-right">
            <!-- Network Animation Background -->
            <canvas id="networkCanvas"></canvas>
            
            <div class="branding-content">
                <h2>Platform Digital Agency Terlengkap</h2>
                <p class="subtitle">Solusi digital untuk semua bisnis Anda</p>
                
                <div class="feature-list">
                    <div class="feature-item">
                        <span class="icon">✅</span>
                        <span>232+ Layanan Digital Lengkap</span>
                    </div>
                    <div class="feature-item">
                        <span class="icon">✅</span>
                        <span>1500+ Template Website Siap Pakai</span>
                    </div>
                    <div class="feature-item">
                        <span class="icon">✅</span>
                        <span>Komisi Partner 30-55%</span>
                    </div>
                    <div class="feature-item">
                        <span class="icon">✅</span>
                        <span>NO Setup Fee untuk Sewa</span>
                    </div>
                </div>
                
                <div class="stats">
                    <div class="stat-item">
                        <div class="stat-number">500+</div>
                        <div class="stat-label">Clients</div>
                    </div>
                    <div class="stat-item">
                        <div class="stat-number">4.9/5</div>
                        <div class="stat-label">Rating</div>
                    </div>
                </div>
                
                <blockquote>
                    "Bergabung dengan ribuan partner sukses kami dan raih penghasilan unlimited!"
                </blockquote>
            </div>
        </div>
    </div>
    
    <script src="/assets/js/network-animation.js"></script>
    <script src="/assets/js/auth.js"></script>
</body>
</html>
```

### Login Backend (PHP)
```php
<?php
/**
 * Login Controller
 */
class LoginController extends Controller {
    
    public function index() {
        // If already logged in, redirect to dashboard
        if (Auth::check()) {
            return redirect($this->getDashboardUrl());
        }
        
        $this->view('auth/login');
    }
    
    public function authenticate() {
        // CSRF Validation
        if (!CSRF::validate($_POST['csrf_token'])) {
            return json(['error' => 'Invalid CSRF token'], 403);
        }
        
        // Rate Limiting (5 attempts / 15 min per IP)
        $ip = $_SERVER['REMOTE_ADDR'];
        if (RateLimit::exceeded('login', $ip, 5, 900)) {
            return json([
                'error' => 'Terlalu banyak percobaan. Coba lagi dalam 15 menit.'
            ], 429);
        }
        
        // Input Validation
        $validator = new Validator($_POST);
        $validator->rule('required', ['email', 'password']);
        $validator->rule('email', 'email');
        
        if (!$validator->validate()) {
            return json(['errors' => $validator->errors()], 422);
        }
        
        $email = filter_var($_POST['email'], FILTER_SANITIZE_EMAIL);
        $password = $_POST['password'];
        $rememberMe = isset($_POST['remember_me']);
        
        // Attempt Login
        $user = User::findByEmail($email);
        
        if (!$user || !password_verify($password, $user->password_hash)) {
            // Increment rate limit
            RateLimit::increment('login', $ip);
            
            return json([
                'error' => 'Email atau password salah.'
            ], 401);
        }
        
        // Check if email verified
        if (!$user->email_verified) {
            return json([
                'error' => 'Email belum diverifikasi. Silakan cek inbox Anda.',
                'action' => 'resend_verification'
            ], 403);
        }
        
        // Check if account active
        if ($user->status !== 'active') {
            $statusMsg = [
                'pending' => 'Akun Anda masih menunggu approval admin.',
                'inactive' => 'Akun Anda tidak aktif. Hubungi admin.',
                'suspended' => 'Akun Anda di-suspend. Hubungi admin.'
            ];
            
            return json([
                'error' => $statusMsg[$user->status] ?? 'Akun tidak dapat diakses.'
            ], 403);
        }
        
        // Login Success - Create Session
        Auth::login($user, $rememberMe);
        
        // Reset rate limit
        RateLimit::reset('login', $ip);
        
        // Update last login
        $user->updateLastLogin($_SERVER['REMOTE_ADDR']);
        
        // Log activity
        ActivityLog::log([
            'user_id' => $user->id,
            'action' => 'login',
            'description' => 'User logged in',
            'ip_address' => $_SERVER['REMOTE_ADDR']
        ]);
        
        // Return success with redirect URL
        return json([
            'success' => true,
            'message' => 'Login berhasil!',
            'redirect' => $this->getDashboardUrl($user->role)
        ]);
    }
    
    private function getDashboardUrl($role = null) {
        $role = $role ?? Auth::user()->role;
        
        $dashboards = [
            'admin' => '/admin/dashboard',
            'manager' => '/manager/dashboard',
            'spv' => '/spv/dashboard',
            'partner' => '/partner/dashboard',
            'client' => '/client/dashboard'
        ];
        
        return $dashboards[$role] ?? '/';
    }
}
```

---

## 2️⃣ REGISTER CLIENT (SIMPLE FORM)

### Register Client Form
```html
<form id="registerClientForm" method="POST" action="/auth/register/client">
    <input type="hidden" name="csrf_token" value="<?= csrf_token() ?>">
    
    <!-- Full Name -->
    <div class="form-group">
        <label>Nama Lengkap *</label>
        <input type="text" name="full_name" required 
               placeholder="Masukkan nama lengkap"
               class="form-control">
    </div>
    
    <!-- Email -->
    <div class="form-group">
        <label>Email *</label>
        <input type="email" name="email" required 
               placeholder="nama@email.com"
               class="form-control">
        <small>Email akan digunakan untuk login</small>
    </div>
    
    <!-- Phone -->
    <div class="form-group">
        <label>No. WhatsApp *</label>
        <input type="tel" name="phone" required 
               placeholder="08xxxxxxxxxx atau +628xxxxxxxxxx"
               pattern="^(08|\\+628)\\d{8,11}$"
               class="form-control">
    </div>
    
    <!-- Password -->
    <div class="form-group">
        <label>Password *</label>
        <input type="password" name="password" required 
               minlength="8"
               placeholder="Minimal 8 karakter"
               class="form-control">
        <div class="password-strength" id="passwordStrength"></div>
    </div>
    
    <!-- Confirm Password -->
    <div class="form-group">
        <label>Konfirmasi Password *</label>
        <input type="password" name="password_confirm" required 
               placeholder="Masukkan ulang password"
               class="form-control">
    </div>
    
    <!-- Terms & Conditions -->
    <div class="form-group">
        <label class="checkbox-block">
            <input type="checkbox" name="agree_terms" required>
            Saya setuju dengan 
            <a href="/terms" target="_blank">Syarat & Ketentuan</a> 
            dan 
            <a href="/privacy" target="_blank">Kebijakan Privasi</a>
        </label>
    </div>
    
    <!-- Submit Button -->
    <button type="submit" class="btn btn-primary btn-block">
        Daftar Sekarang
    </button>
    
    <!-- Login Link -->
    <p class="text-center mt-3">
        Sudah punya akun? 
        <a href="/auth/login">Login di sini</a>
    </p>
</form>
```

### Register Client Backend
```php
<?php
class RegisterClientController extends Controller {
    
    public function store() {
        // CSRF Validation
        CSRF::validate($_POST['csrf_token']);
        
        // Validation
        $validator = new Validator($_POST);
        $validator->rule('required', ['full_name', 'email', 'phone', 'password', 'password_confirm']);
        $validator->rule('email', 'email');
        $validator->rule('lengthMin', 'password', 8);
        $validator->rule('equals', 'password', 'password_confirm');
        $validator->rule('regex', 'phone', '/^(08|\\+628)\\d{8,11}$/');
        $validator->rule('accepted', 'agree_terms');
        
        if (!$validator->validate()) {
            return json(['errors' => $validator->errors()], 422);
        }
        
        // Check if email already exists
        if (User::emailExists($_POST['email'])) {
            return json([
                'error' => 'Email sudah terdaftar. Silakan login atau gunakan email lain.'
            ], 422);
        }
        
        // Check if phone already exists
        if (User::phoneExists($_POST['phone'])) {
            return json([
                'error' => 'Nomor WhatsApp sudah terdaftar.'
            ], 422);
        }
        
        DB::beginTransaction();
        
        try {
            // Create User
            $user = User::create([
                'role' => 'client',
                'username' => $this->generateUsername($_POST['email']),
                'email' => $_POST['email'],
                'password_hash' => password_hash($_POST['password'], PASSWORD_BCRYPT, ['cost' => 12]),
                'phone' => $_POST['phone'],
                'full_name' => $_POST['full_name'],
                'status' => 'pending',
                'email_verified' => false,
                'language_preference' => 'id'
            ]);
            
            // Create Client Record
            $client = Client::create([
                'user_id' => $user->id,
                'partner_id' => $this->getPartnerIdFromReferral(), // If from referral link
                'status' => 'active'
            ]);
            
            // Create User Profile
            UserProfile::create([
                'user_id' => $user->id,
                'whatsapp' => $_POST['phone']
            ]);
            
            // Generate Email Verification Token
            $token = bin2hex(random_bytes(32));
            EmailVerification::create([
                'user_id' => $user->id,
                'email' => $_POST['email'],
                'token' => $token,
                'expires_at' => date('Y-m-d H:i:s', strtotime('+24 hours'))
            ]);
            
            // Send Verification Email
            Mailer::send([
                'to' => $_POST['email'],
                'subject' => 'Verifikasi Email - SITUNEO Digital',
                'template' => 'email-verification',
                'data' => [
                    'name' => $_POST['full_name'],
                    'verification_url' => url("/auth/verify-email?token={$token}")
                ]
            ]);
            
            // Log activity
            ActivityLog::log([
                'user_id' => $user->id,
                'action' => 'register',
                'description' => 'New client registered',
                'ip_address' => $_SERVER['REMOTE_ADDR']
            ]);
            
            DB::commit();
            
            return json([
                'success' => true,
                'message' => 'Pendaftaran berhasil! Silakan cek email Anda untuk verifikasi.'
            ]);
            
        } catch (Exception $e) {
            DB::rollback();
            Logger::error('Register Client Failed', $e);
            
            return json([
                'error' => 'Terjadi kesalahan. Silakan coba lagi.'
            ], 500);
        }
    }
    
    private function generateUsername($email) {
        $base = explode('@', $email)[0];
        $username = preg_replace('/[^a-zA-Z0-9]/', '', $base);
        
        // Ensure unique
        $counter = 1;
        $original = $username;
        while (User::usernameExists($username)) {
            $username = $original . $counter;
            $counter++;
        }
        
        return $username;
    }
    
    private function getPartnerIdFromReferral() {
        // Check if registration came from partner referral link
        if (isset($_SESSION['referral_code'])) {
            $partner = Partner::findByReferralCode($_SESSION['referral_code']);
            return $partner ? $partner->partner_id : null;
        }
        return null;
    }
}
```

---

## 3️⃣ REGISTER PARTNER/SPV/MANAGER (MULTI-STEP)

### Multi-Step Form Structure
```
STEP 1: Pilih Role
STEP 2: Data Diri
STEP 3: Alamat Domisili
STEP 4: Upload Dokumen (KTP, CV)
STEP 5: Referral Code (Optional)
STEP 6: Review & Submit
```

### Step 1: Role Selection
```html
<div class="step step-1 active">
    <h3>Pilih Role Anda</h3>
    <p>Pilih posisi yang sesuai dengan keahlian Anda</p>
    
    <div class="role-cards">
        <!-- Partner -->
        <label class="role-card">
            <input type="radio" name="role" value="partner" required>
            <div class="role-content">
                <h4>🚀 PARTNER</h4>
                <p class="role-desc">Sales & Freelancer</p>
                <ul class="role-benefits">
                    <li>✅ Komisi 30-55% (Tier-based)</li>
                    <li>✅ Cari client via referral link</li>
                    <li>✅ Dapat task tambahan dari admin</li>
                    <li>✅ Tidak perlu manage team</li>
                </ul>
                <span class="role-tag">Paling Mudah</span>
            </div>
        </label>
        
        <!-- SPV -->
        <label class="role-card">
            <input type="radio" name="role" value="spv" required>
            <div class="role-content">
                <h4>👨‍💼 SPV (SUPERVISOR)</h4>
                <p class="role-desc">Manage Partner</p>
                <ul class="role-benefits">
                    <li>✅ Komisi 10% dari partner</li>
                    <li>✅ ARPU Bonus hingga Rp 10 juta</li>
                    <li>✅ Manage 10+ partner</li>
                    <li>✅ Passive income</li>
                </ul>
                <span class="role-tag">Butuh Pengalaman</span>
            </div>
        </label>
        
        <!-- Manager -->
        <label class="role-card">
            <input type="radio" name="role" value="manager" required>
            <div class="role-content">
                <h4>🏢 MANAGER AREA</h4>
                <p class="role-desc">Manage SPV & Area</p>
                <ul class="role-benefits">
                    <li>✅ Komisi 5% dari seluruh area</li>
                    <li>✅ ARPU Bonus hingga Rp 15 juta</li>
                    <li>✅ Manage 3+ SPV</li>
                    <li>✅ Highest passive income</li>
                </ul>
                <span class="role-tag">Leadership</span>
            </div>
        </label>
    </div>
    
    <button type="button" class="btn btn-primary" onclick="nextStep()">
        Lanjut
    </button>
</div>
```

### Step 2: Data Diri
```html
<div class="step step-2">
    <h3>Data Diri</h3>
    
    <!-- Full Name -->
    <div class="form-group">
        <label>Nama Lengkap *</label>
        <input type="text" name="full_name" required class="form-control">
    </div>
    
    <!-- Email -->
    <div class="form-group">
        <label>Email *</label>
        <input type="email" name="email" required class="form-control">
    </div>
    
    <!-- Phone -->
    <div class="form-group">
        <label>No. WhatsApp *</label>
        <input type="tel" name="phone" required class="form-control">
    </div>
    
    <!-- Password -->
    <div class="form-group">
        <label>Password *</label>
        <input type="password" name="password" required class="form-control">
    </div>
    
    <!-- Confirm Password -->
    <div class="form-group">
        <label>Konfirmasi Password *</label>
        <input type="password" name="password_confirm" required class="form-control">
    </div>
    
    <div class="step-navigation">
        <button type="button" class="btn btn-secondary" onclick="prevStep()">
            Kembali
        </button>
        <button type="button" class="btn btn-primary" onclick="nextStep()">
            Lanjut
        </button>
    </div>
</div>
```

### Step 3: Alamat Domisili
```html
<div class="step step-3">
    <h3>Alamat Domisili</h3>
    <p>Alamat lengkap tempat tinggal Anda saat ini</p>
    
    <!-- Jalan -->
    <div class="form-group">
        <label>Alamat Jalan *</label>
        <textarea name="address_street" required rows="3" 
                  placeholder="Jl. Kebon Jeruk No. 123"
                  class="form-control"></textarea>
    </div>
    
    <!-- Kota -->
    <div class="form-group">
        <label>Kota/Kabupaten *</label>
        <input type="text" name="address_city" required 
               placeholder="Jakarta Barat"
               class="form-control">
    </div>
    
    <!-- Provinsi -->
    <div class="form-group">
        <label>Provinsi *</label>
        <select name="address_province" required class="form-control">
            <option value="">Pilih Provinsi</option>
            <option value="DKI Jakarta">DKI Jakarta</option>
            <option value="Jawa Barat">Jawa Barat</option>
            <option value="Jawa Tengah">Jawa Tengah</option>
            <!-- 34 provinsi Indonesia -->
        </select>
    </div>
    
    <!-- Kode Pos -->
    <div class="form-group">
        <label>Kode Pos *</label>
        <input type="text" name="address_postal_code" required 
               pattern="\\d{5}"
               placeholder="11530"
               class="form-control">
    </div>
    
    <div class="step-navigation">
        <button type="button" class="btn btn-secondary" onclick="prevStep()">
            Kembali
        </button>
        <button type="button" class="btn btn-primary" onclick="nextStep()">
            Lanjut
        </button>
    </div>
</div>
```

### Step 4: Upload Dokumen
```html
<div class="step step-4">
    <h3>Upload Dokumen</h3>
    <p>Dokumen wajib untuk verifikasi identitas Anda</p>
    
    <!-- KTP -->
    <div class="form-group">
        <label>Upload KTP * (JPG, PNG, max 2MB)</label>
        <div class="file-upload-wrapper">
            <input type="file" name="ktp_file" 
                   accept="image/jpeg,image/png"
                   required
                   id="ktpFile"
                   class="file-input">
            <label for="ktpFile" class="file-label">
                <span class="file-icon">📄</span>
                <span class="file-text">Klik untuk upload KTP</span>
            </label>
            <div class="file-preview" id="ktpPreview"></div>
        </div>
        <small>Pastikan foto KTP jelas dan tidak blur</small>
    </div>
    
    <!-- CV -->
    <div class="form-group">
        <label>Upload CV * (PDF, DOC, max 5MB)</label>
        <div class="file-upload-wrapper">
            <input type="file" name="cv_file" 
                   accept=".pdf,.doc,.docx"
                   required
                   id="cvFile"
                   class="file-input">
            <label for="cvFile" class="file-label">
                <span class="file-icon">📄</span>
                <span class="file-text">Klik untuk upload CV</span>
            </label>
            <div class="file-preview" id="cvPreview"></div>
        </div>
        <small>Format: PDF atau Word Document</small>
    </div>
    
    <div class="step-navigation">
        <button type="button" class="btn btn-secondary" onclick="prevStep()">
            Kembali
        </button>
        <button type="button" class="btn btn-primary" onclick="nextStep()">
            Lanjut
        </button>
    </div>
</div>
```

### Step 5: Referral Code (Optional)
```html
<div class="step step-5">
    <h3>Kode Referral (Opsional)</h3>
    <p>Jika Anda direferensikan oleh SPV/Manager, masukkan kode referral</p>
    
    <div class="form-group">
        <label>Kode Referral</label>
        <input type="text" name="referral_code" 
               placeholder="Contoh: SITABC123"
               class="form-control">
        <small>Kosongkan jika tidak ada</small>
    </div>
    
    <div class="referral-info" id="referralInfo" style="display:none;">
        <div class="alert alert-success">
            ✅ Kode valid! Anda akan terhubung dengan:
            <strong id="referralName"></strong>
        </div>
    </div>
    
    <div class="alert alert-info">
        💡 <strong>Keuntungan Punya SPV/Manager:</strong><br>
        - Mendapat bimbingan & support<br>
        - Jika client stop <3 bulan, tanggungan dibagi 85%-10%-5%<br>
        - Networking lebih luas
    </div>
    
    <div class="step-navigation">
        <button type="button" class="btn btn-secondary" onclick="prevStep()">
            Kembali
        </button>
        <button type="button" class="btn btn-primary" onclick="nextStep()">
            Lanjut
        </button>
    </div>
</div>
```

### Step 6: Review & Submit
```html
<div class="step step-6">
    <h3>Review & Submit</h3>
    <p>Pastikan semua data sudah benar sebelum submit</p>
    
    <div class="review-section">
        <div class="review-item">
            <strong>Role:</strong>
            <span id="review-role"></span>
        </div>
        <div class="review-item">
            <strong>Nama:</strong>
            <span id="review-name"></span>
        </div>
        <div class="review-item">
            <strong>Email:</strong>
            <span id="review-email"></span>
        </div>
        <div class="review-item">
            <strong>WhatsApp:</strong>
            <span id="review-phone"></span>
        </div>
        <div class="review-item">
            <strong>Alamat:</strong>
            <span id="review-address"></span>
        </div>
        <div class="review-item">
            <strong>KTP:</strong>
            <span id="review-ktp">✅ Uploaded</span>
        </div>
        <div class="review-item">
            <strong>CV:</strong>
            <span id="review-cv">✅ Uploaded</span>
        </div>
        <div class="review-item">
            <strong>Referral:</strong>
            <span id="review-referral"></span>
        </div>
    </div>
    
    <!-- Terms & Conditions -->
    <div class="form-group">
        <label class="checkbox-block">
            <input type="checkbox" name="agree_terms" required>
            Saya setuju dengan 
            <a href="/terms" target="_blank">Syarat & Ketentuan</a> 
            dan bersedia mengikuti aturan SITUNEO Digital
        </label>
    </div>
    
    <div class="alert alert-warning">
        ⏳ <strong>Proses Selanjutnya:</strong><br>
        Setelah submit, aplikasi Anda akan direview oleh admin dalam 1-3 hari kerja. 
        Anda akan dihubungi untuk interview lebih lanjut.
    </div>
    
    <div class="step-navigation">
        <button type="button" class="btn btn-secondary" onclick="prevStep()">
            Kembali
        </button>
        <button type="submit" class="btn btn-success btn-lg">
            Submit Aplikasi
        </button>
    </div>
</div>
```

### Register Partner Backend
```php
<?php
class RegisterPartnerController extends Controller {
    
    public function store() {
        // CSRF Validation
        CSRF::validate($_POST['csrf_token']);
        
        // Validation
        $validator = new Validator($_POST);
        $validator->rule('required', [
            'role', 'full_name', 'email', 'phone', 
            'password', 'password_confirm',
            'address_street', 'address_city', 
            'address_province', 'address_postal_code'
        ]);
        $validator->rule('in', 'role', ['partner', 'spv', 'manager']);
        $validator->rule('email', 'email');
        $validator->rule('lengthMin', 'password', 8);
        $validator->rule('equals', 'password', 'password_confirm');
        $validator->rule('accepted', 'agree_terms');
        
        if (!$validator->validate()) {
            return json(['errors' => $validator->errors()], 422);
        }
        
        // Validate files
        $ktpValidation = $this->validateFile($_FILES['ktp_file'], [
            'types' => ['image/jpeg', 'image/png'],
            'max_size' => 2 * 1024 * 1024 // 2MB
        ]);
        
        $cvValidation = $this->validateFile($_FILES['cv_file'], [
            'types' => ['application/pdf', 'application/msword', 'application/vnd.openxmlformats-officedocument.wordprocessingml.document'],
            'max_size' => 5 * 1024 * 1024 // 5MB
        ]);
        
        if (!$ktpValidation['valid'] || !$cvValidation['valid']) {
            return json([
                'error' => $ktpValidation['error'] ?? $cvValidation['error']
            ], 422);
        }
        
        // Check if email/phone already exists
        if (User::emailExists($_POST['email'])) {
            return json(['error' => 'Email sudah terdaftar.'], 422);
        }
        
        if (User::phoneExists($_POST['phone'])) {
            return json(['error' => 'Nomor WhatsApp sudah terdaftar.'], 422);
        }
        
        // Verify referral code (if provided)
        $referrer = null;
        if (!empty($_POST['referral_code'])) {
            $referrer = $this->verifyReferralCode(
                $_POST['referral_code'],
                $_POST['role']
            );
            
            if (!$referrer) {
                return json(['error' => 'Kode referral tidak valid.'], 422);
            }
        }
        
        DB::beginTransaction();
        
        try {
            // Upload files
            $ktpPath = Uploader::upload($_FILES['ktp_file'], 'ktp');
            $cvPath = Uploader::upload($_FILES['cv_file'], 'cv');
            
            // Create User
            $user = User::create([
                'role' => $_POST['role'],
                'username' => $this->generateUsername($_POST['email']),
                'email' => $_POST['email'],
                'password_hash' => password_hash($_POST['password'], PASSWORD_BCRYPT, ['cost' => 12]),
                'phone' => $_POST['phone'],
                'full_name' => $_POST['full_name'],
                'status' => 'pending', // Wait admin approval
                'email_verified' => false,
                'language_preference' => 'id'
            ]);
            
            // Create User Profile
            UserProfile::create([
                'user_id' => $user->id,
                'whatsapp' => $_POST['phone'],
                'address_street' => $_POST['address_street'],
                'address_city' => $_POST['address_city'],
                'address_province' => $_POST['address_province'],
                'address_postal_code' => $_POST['address_postal_code'],
                'id_card_file' => $ktpPath,
                'cv_file' => $cvPath
            ]);
            
            // Create Partner/SPV/Manager Record
            $roleData = [
                'user_id' => $user->id,
                'referral_code' => $this->generateReferralCode(),
                'status' => 'active' // Will be activated after admin approval
            ];
            
            if ($_POST['role'] === 'partner') {
                $partner = Partner::create(array_merge($roleData, [
                    'spv_id' => $referrer ? $referrer->id : null,
                    'tier_current' => '1',
                    'commission_rate' => 30.00
                ]));
                
                // If has referrer (SPV), create referral record
                if ($referrer) {
                    SpvReferral::create([
                        'spv_id' => $referrer->id,
                        'referred_partner_id' => $partner->partner_id,
                        'referral_code_used' => $_POST['referral_code'],
                        'status' => 'pending'
                    ]);
                }
            } 
            elseif ($_POST['role'] === 'spv') {
                $spv = Spv::create(array_merge($roleData, [
                    'manager_id' => $referrer ? $referrer->id : null,
                    'commission_rate' => 10.00
                ]));
                
                if ($referrer) {
                    ManagerReferral::create([
                        'manager_id' => $referrer->id,
                        'referred_spv_id' => $spv->spv_id,
                        'referral_code_used' => $_POST['referral_code'],
                        'status' => 'pending'
                    ]);
                }
            }
            elseif ($_POST['role'] === 'manager') {
                Manager::create(array_merge($roleData, [
                    'commission_rate' => 5.00
                ]));
            }
            
            // Send notification to admin
            Notification::create([
                'user_id' => 1, // Admin user_id
                'type' => 'info',
                'category' => 'system',
                'title' => 'Aplikasi ' . strtoupper($_POST['role']) . ' Baru',
                'message' => $_POST['full_name'] . ' telah mendaftar sebagai ' . $_POST['role'] . '. Perlu review.',
                'action_url' => '/admin/applications/detail/' . $user->id
            ]);
            
            // Send email to applicant
            Mailer::send([
                'to' => $_POST['email'],
                'subject' => 'Aplikasi Diterima - SITUNEO Digital',
                'template' => 'application-received',
                'data' => [
                    'name' => $_POST['full_name'],
                    'role' => $_POST['role']
                ]
            ]);
            
            // Log activity
            ActivityLog::log([
                'user_id' => $user->id,
                'action' => 'register_' . $_POST['role'],
                'description' => 'New ' . $_POST['role'] . ' application submitted',
                'ip_address' => $_SERVER['REMOTE_ADDR']
            ]);
            
            DB::commit();
            
            return json([
                'success' => true,
                'message' => 'Aplikasi berhasil dikirim! Tim kami akan menghubungi Anda dalam 1-3 hari kerja untuk interview.'
            ]);
            
        } catch (Exception $e) {
            DB::rollback();
            Logger::error('Register Partner Failed', $e);
            
            // Delete uploaded files if error
            if (isset($ktpPath)) @unlink($ktpPath);
            if (isset($cvPath)) @unlink($cvPath);
            
            return json([
                'error' => 'Terjadi kesalahan. Silakan coba lagi.'
            ], 500);
        }
    }
    
    private function verifyReferralCode($code, $role) {
        // Partner can only use SPV referral
        // SPV can only use Manager referral
        // Manager cannot use referral
        
        if ($role === 'partner') {
            return Spv::findByReferralCode($code);
        } elseif ($role === 'spv') {
            return Manager::findByReferralCode($code);
        }
        
        return null;
    }
    
    private function generateReferralCode() {
        do {
            $code = 'SIT' . strtoupper(substr(md5(uniqid()), 0, 7));
        } while ($this->referralCodeExists($code));
        
        return $code;
    }
    
    private function referralCodeExists($code) {
        return Partner::where('referral_code', $code)->exists() ||
               Spv::where('referral_code', $code)->exists() ||
               Manager::where('referral_code', $code)->exists();
    }
}
```

---

## ✅ NEXT: MASTER BATCH 05-14

Batch 04 sudah selesai! File sudah tersimpan. Mau saya lanjut batch 05-14 sekarang?

**END OF MASTER BATCH 04**  
*Authentication System Complete*  
*Versi 3.0 Final - 18 November 2025*

---

# 📋 SITUNEO DIGITAL - MASTER BATCH 15
## COMPLETE AUTHENTICATION CODE (FORGOT PASSWORD, RESET, VERIFICATION)

**Versi:** 3.0 FINAL  
**Tanggal:** 18 November 2025  
**Status:** Production-Ready Code  
**Copy-Paste Ready:** YES ✅

---

## 🔐 FORGOT PASSWORD SYSTEM

### 1. Forgot Password Page (HTML)
```html
<!-- File: /app/views/auth/forgot-password.php -->
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lupa Password - SITUNEO Digital</title>
    <link rel="stylesheet" href="/assets/css/auth.css">
</head>
<body>
    <div class="auth-container split-design">
        <!-- LEFT: Form -->
        <div class="auth-left">
            <div class="auth-form-wrapper">
                <img src="/assets/img/logo.png" alt="SITUNEO" class="auth-logo">
                <h1>Lupa Password?</h1>
                <p>Masukkan email Anda dan kami akan mengirim link reset password</p>
                
                <form id="forgotPasswordForm" method="POST" action="/auth/forgot-password/send">
                    <input type="hidden" name="csrf_token" value="<?= csrf_token() ?>">
                    
                    <div class="form-group">
                        <label>Email</label>
                        <input type="email" name="email" required 
                               placeholder="nama@email.com"
                               class="form-control">
                        <span class="error" id="email-error"></span>
                    </div>
                    
                    <button type="submit" class="btn btn-primary btn-block" id="submitBtn">
                        Kirim Link Reset Password
                    </button>
                    
                    <div class="text-center mt-3">
                        <a href="/auth/login">Kembali ke Login</a>
                    </div>
                </form>
                
                <div id="successMessage" class="alert alert-success" style="display:none;">
                    ✅ Link reset password telah dikirim ke email Anda. 
                    Silakan cek inbox/spam Anda.
                </div>
            </div>
        </div>
        
        <!-- RIGHT: Branding -->
        <div class="auth-right">
            <canvas id="networkCanvas"></canvas>
            <div class="branding-content">
                <h2>Reset Password Mudah</h2>
                <p>Kami akan membantu Anda mendapatkan akses kembali</p>
            </div>
        </div>
    </div>
    
    <script src="/assets/js/network-animation.js"></script>
    <script src="/assets/js/forgot-password.js"></script>
</body>
</html>
```

### 2. Forgot Password Controller (PHP)
```php
<?php
/**
 * File: /app/controllers/auth/ForgotPasswordController.php
 * Forgot Password Controller
 */

class ForgotPasswordController extends Controller {
    
    public function index() {
        // If already logged in, redirect
        if (Auth::check()) {
            return redirect('/dashboard');
        }
        
        $this->view('auth/forgot-password');
    }
    
    public function send() {
        // CSRF Validation
        if (!CSRF::validate($_POST['csrf_token'])) {
            return json(['error' => 'Invalid CSRF token'], 403);
        }
        
        // Rate Limiting (3 attempts per hour per IP)
        $ip = $_SERVER['REMOTE_ADDR'];
        if (RateLimit::exceeded('forgot_password', $ip, 3, 3600)) {
            return json([
                'error' => 'Terlalu banyak percobaan. Coba lagi dalam 1 jam.'
            ], 429);
        }
        
        // Validation
        $validator = new Validator($_POST);
        $validator->rule('required', 'email');
        $validator->rule('email', 'email');
        
        if (!$validator->validate()) {
            return json(['errors' => $validator->errors()], 422);
        }
        
        $email = filter_var($_POST['email'], FILTER_SANITIZE_EMAIL);
        
        // Find user by email
        $user = User::findByEmail($email);
        
        // SECURITY: Always return success even if email not found
        // To prevent email enumeration attacks
        if (!$user) {
            // Increment rate limit
            RateLimit::increment('forgot_password', $ip);
            
            // Return success (fake)
            return json([
                'success' => true,
                'message' => 'Jika email terdaftar, link reset password telah dikirim.'
            ]);
        }
        
        // Check if user is active
        if ($user->status !== 'active') {
            return json([
                'error' => 'Akun tidak aktif. Hubungi admin.'
            ], 403);
        }
        
        DB::beginTransaction();
        
        try {
            // Generate token
            $token = bin2hex(random_bytes(32));
            $expires = date('Y-m-d H:i:s', strtotime('+1 hour'));
            
            // Delete old tokens for this user
            PasswordReset::deleteByUserId($user->id);
            
            // Create new token
            PasswordReset::create([
                'user_id' => $user->id,
                'email' => $email,
                'token' => hash('sha256', $token), // Hash token in DB
                'expires_at' => $expires
            ]);
            
            // Send email
            $resetUrl = url("/auth/reset-password?token={$token}");
            
            Mailer::send([
                'to' => $email,
                'subject' => 'Reset Password - SITUNEO Digital',
                'template' => 'password-reset',
                'data' => [
                    'name' => $user->full_name,
                    'reset_url' => $resetUrl,
                    'expires_in' => '1 jam'
                ]
            ]);
            
            // Log activity
            ActivityLog::log([
                'user_id' => $user->id,
                'action' => 'forgot_password_requested',
                'description' => 'Password reset requested',
                'ip_address' => $ip
            ]);
            
            DB::commit();
            
            // Increment rate limit
            RateLimit::increment('forgot_password', $ip);
            
            return json([
                'success' => true,
                'message' => 'Link reset password telah dikirim ke email Anda.'
            ]);
            
        } catch (Exception $e) {
            DB::rollback();
            Logger::error('Forgot Password Failed', $e);
            
            return json([
                'error' => 'Terjadi kesalahan. Silakan coba lagi.'
            ], 500);
        }
    }
}
```

### 3. Reset Password Page (HTML)
```html
<!-- File: /app/views/auth/reset-password.php -->
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Reset Password - SITUNEO Digital</title>
    <link rel="stylesheet" href="/assets/css/auth.css">
</head>
<body>
    <div class="auth-container split-design">
        <!-- LEFT: Form -->
        <div class="auth-left">
            <div class="auth-form-wrapper">
                <img src="/assets/img/logo.png" alt="SITUNEO" class="auth-logo">
                <h1>Reset Password</h1>
                <p>Masukkan password baru Anda</p>
                
                <?php if (isset($error)): ?>
                <div class="alert alert-danger">
                    <?= $error ?>
                </div>
                <?php endif; ?>
                
                <form id="resetPasswordForm" method="POST" action="/auth/reset-password/update">
                    <input type="hidden" name="csrf_token" value="<?= csrf_token() ?>">
                    <input type="hidden" name="token" value="<?= htmlspecialchars($_GET['token'] ?? '') ?>">
                    
                    <!-- New Password -->
                    <div class="form-group">
                        <label>Password Baru *</label>
                        <div class="password-wrapper">
                            <input type="password" name="password" 
                                   id="password" required 
                                   minlength="8"
                                   placeholder="Minimal 8 karakter"
                                   class="form-control">
                            <button type="button" class="toggle-password" 
                                    onclick="togglePassword('password')">
                                👁️
                            </button>
                        </div>
                        <div class="password-strength" id="passwordStrength"></div>
                        <span class="error" id="password-error"></span>
                    </div>
                    
                    <!-- Confirm Password -->
                    <div class="form-group">
                        <label>Konfirmasi Password *</label>
                        <div class="password-wrapper">
                            <input type="password" name="password_confirm" 
                                   id="password_confirm" required 
                                   placeholder="Masukkan ulang password"
                                   class="form-control">
                            <button type="button" class="toggle-password" 
                                    onclick="togglePassword('password_confirm')">
                                👁️
                            </button>
                        </div>
                        <span class="error" id="password-confirm-error"></span>
                    </div>
                    
                    <button type="submit" class="btn btn-primary btn-block">
                        Reset Password
                    </button>
                    
                    <div class="text-center mt-3">
                        <a href="/auth/login">Kembali ke Login</a>
                    </div>
                </form>
            </div>
        </div>
        
        <!-- RIGHT: Branding -->
        <div class="auth-right">
            <canvas id="networkCanvas"></canvas>
            <div class="branding-content">
                <h2>Buat Password Baru</h2>
                <p>Pastikan password Anda kuat dan mudah diingat</p>
            </div>
        </div>
    </div>
    
    <script src="/assets/js/network-animation.js"></script>
    <script src="/assets/js/reset-password.js"></script>
</body>
</html>
```

### 4. Reset Password Controller (PHP)
```php
<?php
/**
 * File: /app/controllers/auth/ResetPasswordController.php
 */

class ResetPasswordController extends Controller {
    
    public function index() {
        $token = $_GET['token'] ?? null;
        
        if (!$token) {
            return redirect('/auth/forgot-password');
        }
        
        // Verify token exists and not expired
        $hashedToken = hash('sha256', $token);
        $reset = PasswordReset::findByToken($hashedToken);
        
        if (!$reset) {
            $this->view('auth/reset-password', [
                'error' => 'Link reset password tidak valid atau sudah kadaluarsa.'
            ]);
            return;
        }
        
        // Check if expired
        if (strtotime($reset->expires_at) < time()) {
            $this->view('auth/reset-password', [
                'error' => 'Link reset password sudah kadaluarsa. Silakan request ulang.'
            ]);
            return;
        }
        
        $this->view('auth/reset-password');
    }
    
    public function update() {
        // CSRF Validation
        if (!CSRF::validate($_POST['csrf_token'])) {
            return json(['error' => 'Invalid CSRF token'], 403);
        }
        
        // Validation
        $validator = new Validator($_POST);
        $validator->rule('required', ['token', 'password', 'password_confirm']);
        $validator->rule('lengthMin', 'password', 8);
        $validator->rule('equals', 'password', 'password_confirm');
        
        if (!$validator->validate()) {
            return json(['errors' => $validator->errors()], 422);
        }
        
        $token = $_POST['token'];
        $hashedToken = hash('sha256', $token);
        
        // Find reset record
        $reset = PasswordReset::findByToken($hashedToken);
        
        if (!$reset) {
            return json([
                'error' => 'Link reset password tidak valid.'
            ], 400);
        }
        
        // Check if expired
        if (strtotime($reset->expires_at) < time()) {
            return json([
                'error' => 'Link reset password sudah kadaluarsa.'
            ], 400);
        }
        
        DB::beginTransaction();
        
        try {
            // Get user
            $user = User::find($reset->user_id);
            
            if (!$user) {
                throw new Exception('User not found');
            }
            
            // Update password
            $user->update([
                'password_hash' => password_hash($_POST['password'], PASSWORD_BCRYPT, ['cost' => 12]),
                'updated_at' => date('Y-m-d H:i:s')
            ]);
            
            // Delete reset token
            PasswordReset::deleteByUserId($user->id);
            
            // Log activity
            ActivityLog::log([
                'user_id' => $user->id,
                'action' => 'password_reset',
                'description' => 'Password successfully reset',
                'ip_address' => $_SERVER['REMOTE_ADDR']
            ]);
            
            // Send confirmation email
            Mailer::send([
                'to' => $user->email,
                'subject' => 'Password Berhasil Direset - SITUNEO Digital',
                'template' => 'password-reset-success',
                'data' => [
                    'name' => $user->full_name,
                    'reset_time' => date('d M Y H:i')
                ]
            ]);
            
            DB::commit();
            
            return json([
                'success' => true,
                'message' => 'Password berhasil direset! Silakan login.',
                'redirect' => '/auth/login'
            ]);
            
        } catch (Exception $e) {
            DB::rollback();
            Logger::error('Reset Password Failed', $e);
            
            return json([
                'error' => 'Terjadi kesalahan. Silakan coba lagi.'
            ], 500);
        }
    }
}
```

---

## ✅ EMAIL VERIFICATION SYSTEM

### 1. Email Verification Page (HTML)
```html
<!-- File: /app/views/auth/verify-email.php -->
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Verifikasi Email - SITUNEO Digital</title>
    <link rel="stylesheet" href="/assets/css/auth.css">
</head>
<body>
    <div class="auth-container centered">
        <div class="verification-card">
            <?php if ($verified): ?>
            <!-- Success -->
            <div class="success-icon">✅</div>
            <h1>Email Berhasil Diverifikasi!</h1>
            <p>Terima kasih telah memverifikasi email Anda.</p>
            <a href="/auth/login" class="btn btn-primary">
                Login Sekarang
            </a>
            
            <?php elseif ($error): ?>
            <!-- Error -->
            <div class="error-icon">❌</div>
            <h1>Verifikasi Gagal</h1>
            <p><?= $error ?></p>
            <a href="/auth/resend-verification" class="btn btn-primary">
                Kirim Ulang Email Verifikasi
            </a>
            
            <?php else: ?>
            <!-- Processing -->
            <div class="loading-icon">⏳</div>
            <h1>Memverifikasi Email...</h1>
            <p>Mohon tunggu sebentar.</p>
            <?php endif; ?>
        </div>
    </div>
    
    <script>
    <?php if (!$verified && !$error): ?>
    // Auto verify
    window.location.href = '/auth/verify-email/process?token=<?= $_GET['token'] ?? '' ?>';
    <?php endif; ?>
    </script>
</body>
</html>
```

### 2. Email Verification Controller (PHP)
```php
<?php
/**
 * File: /app/controllers/auth/EmailVerificationController.php
 */

class EmailVerificationController extends Controller {
    
    public function index() {
        $token = $_GET['token'] ?? null;
        
        if (!$token) {
            $this->view('auth/verify-email', [
                'verified' => false,
                'error' => 'Token verifikasi tidak ditemukan.'
            ]);
            return;
        }
        
        $this->view('auth/verify-email', [
            'verified' => false,
            'error' => null
        ]);
    }
    
    public function process() {
        $token = $_GET['token'] ?? null;
        
        if (!$token) {
            $this->view('auth/verify-email', [
                'verified' => false,
                'error' => 'Token verifikasi tidak ditemukan.'
            ]);
            return;
        }
        
        // Find verification record
        $verification = EmailVerification::findByToken($token);
        
        if (!$verification) {
            $this->view('auth/verify-email', [
                'verified' => false,
                'error' => 'Token verifikasi tidak valid.'
            ]);
            return;
        }
        
        // Check if expired (24 hours)
        if (strtotime($verification->expires_at) < time()) {
            $this->view('auth/verify-email', [
                'verified' => false,
                'error' => 'Token verifikasi sudah kadaluarsa. Silakan kirim ulang.'
            ]);
            return;
        }
        
        DB::beginTransaction();
        
        try {
            // Get user
            $user = User::find($verification->user_id);
            
            if (!$user) {
                throw new Exception('User not found');
            }
            
            // Update user email_verified
            $user->update([
                'email_verified' => true,
                'email_verified_at' => date('Y-m-d H:i:s')
            ]);
            
            // For Partner/SPV/Manager: Update application status
            if (in_array($user->role, ['partner', 'spv', 'manager'])) {
                // Don't auto-activate, wait for admin approval
                // But mark email as verified
            }
            
            // Delete verification token
            EmailVerification::deleteByUserId($user->id);
            
            // Log activity
            ActivityLog::log([
                'user_id' => $user->id,
                'action' => 'email_verified',
                'description' => 'Email successfully verified',
                'ip_address' => $_SERVER['REMOTE_ADDR']
            ]);
            
            // Send welcome email
            Mailer::send([
                'to' => $user->email,
                'subject' => 'Selamat Datang di SITUNEO Digital!',
                'template' => 'welcome',
                'data' => [
                    'name' => $user->full_name,
                    'role' => $user->role
                ]
            ]);
            
            DB::commit();
            
            $this->view('auth/verify-email', [
                'verified' => true,
                'error' => null
            ]);
            
        } catch (Exception $e) {
            DB::rollback();
            Logger::error('Email Verification Failed', $e);
            
            $this->view('auth/verify-email', [
                'verified' => false,
                'error' => 'Terjadi kesalahan. Silakan coba lagi.'
            ]);
        }
    }
    
    public function resend() {
        // Show resend form
        $this->view('auth/resend-verification');
    }
    
    public function sendNew() {
        // CSRF Validation
        if (!CSRF::validate($_POST['csrf_token'])) {
            return json(['error' => 'Invalid CSRF token'], 403);
        }
        
        // Rate Limiting
        $ip = $_SERVER['REMOTE_ADDR'];
        if (RateLimit::exceeded('resend_verification', $ip, 3, 3600)) {
            return json([
                'error' => 'Terlalu banyak percobaan. Coba lagi dalam 1 jam.'
            ], 429);
        }
        
        $email = filter_var($_POST['email'], FILTER_SANITIZE_EMAIL);
        
        // Find user
        $user = User::findByEmail($email);
        
        if (!$user) {
            // Don't reveal if email exists
            return json([
                'success' => true,
                'message' => 'Jika email terdaftar, link verifikasi telah dikirim.'
            ]);
        }
        
        // Check if already verified
        if ($user->email_verified) {
            return json([
                'error' => 'Email sudah diverifikasi. Silakan login.'
            ], 400);
        }
        
        DB::beginTransaction();
        
        try {
            // Delete old tokens
            EmailVerification::deleteByUserId($user->id);
            
            // Generate new token
            $token = bin2hex(random_bytes(32));
            
            EmailVerification::create([
                'user_id' => $user->id,
                'email' => $email,
                'token' => $token,
                'expires_at' => date('Y-m-d H:i:s', strtotime('+24 hours'))
            ]);
            
            // Send email
            Mailer::send([
                'to' => $email,
                'subject' => 'Verifikasi Email - SITUNEO Digital',
                'template' => 'email-verification',
                'data' => [
                    'name' => $user->full_name,
                    'verification_url' => url("/auth/verify-email?token={$token}")
                ]
            ]);
            
            DB::commit();
            
            RateLimit::increment('resend_verification', $ip);
            
            return json([
                'success' => true,
                'message' => 'Link verifikasi baru telah dikirim ke email Anda.'
            ]);
            
        } catch (Exception $e) {
            DB::rollback();
            Logger::error('Resend Verification Failed', $e);
            
            return json([
                'error' => 'Terjadi kesalahan. Silakan coba lagi.'
            ], 500);
        }
    }
}
```

---

## 📧 EMAIL TEMPLATES

### 1. Password Reset Email
```php
<?php
/**
 * File: /app/views/emails/password-reset.php
 */
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <style>
        body { font-family: Arial, sans-serif; margin: 0; padding: 0; background: #f4f4f4; }
        .container { max-width: 600px; margin: 20px auto; background: white; border-radius: 8px; overflow: hidden; }
        .header { background: linear-gradient(135deg, #1E5C99, #0F3057); padding: 30px; text-align: center; }
        .header img { width: 150px; }
        .content { padding: 30px; }
        .content h2 { color: #1E5C99; margin-top: 0; }
        .btn { display: inline-block; padding: 15px 30px; background: #FFB400; color: #000; text-decoration: none; border-radius: 5px; margin: 20px 0; font-weight: bold; }
        .footer { background: #f8f9fa; padding: 20px; text-align: center; font-size: 12px; color: #666; }
        .alert { background: #fff3cd; border-left: 4px solid #FFB400; padding: 15px; margin: 20px 0; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <img src="https://situneo.my.id/assets/img/logo-white.png" alt="SITUNEO">
        </div>
        <div class="content">
            <h2>Reset Password</h2>
            <p>Halo <strong><?= $name ?></strong>,</p>
            <p>Kami menerima permintaan untuk reset password akun SITUNEO Anda.</p>
            <p>Klik tombol di bawah untuk membuat password baru:</p>
            
            <div style="text-align: center;">
                <a href="<?= $reset_url ?>" class="btn">Reset Password</a>
            </div>
            
            <div class="alert">
                ⏰ <strong>Link ini akan kadaluarsa dalam <?= $expires_in ?>.</strong>
            </div>
            
            <p>Jika Anda tidak meminta reset password, abaikan email ini. Password Anda tidak akan berubah.</p>
            
            <p style="color: #666; font-size: 12px; margin-top: 30px;">
                Atau copy link berikut ke browser Anda:<br>
                <a href="<?= $reset_url ?>"><?= $reset_url ?></a>
            </p>
        </div>
        <div class="footer">
            <p><strong>PT SITUNEO DIGITAL SOLUSI INDONESIA</strong></p>
            <p>📧 vins@situneo.my.id | 📱 +62 831-7386-8915</p>
            <p>🌐 <a href="https://situneo.my.id">situneo.my.id</a></p>
        </div>
    </div>
</body>
</html>
```

### 2. Email Verification Template
```php
<?php
/**
 * File: /app/views/emails/email-verification.php
 */
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <style>
        body { font-family: Arial, sans-serif; margin: 0; padding: 0; background: #f4f4f4; }
        .container { max-width: 600px; margin: 20px auto; background: white; border-radius: 8px; overflow: hidden; }
        .header { background: linear-gradient(135deg, #1E5C99, #0F3057); padding: 30px; text-align: center; }
        .header img { width: 150px; }
        .content { padding: 30px; }
        .content h2 { color: #1E5C99; margin-top: 0; }
        .btn { display: inline-block; padding: 15px 30px; background: #28a745; color: white; text-decoration: none; border-radius: 5px; margin: 20px 0; font-weight: bold; }
        .footer { background: #f8f9fa; padding: 20px; text-align: center; font-size: 12px; color: #666; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <img src="https://situneo.my.id/assets/img/logo-white.png" alt="SITUNEO">
        </div>
        <div class="content">
            <h2>Verifikasi Email Anda</h2>
            <p>Halo <strong><?= $name ?></strong>,</p>
            <p>Terima kasih telah mendaftar di SITUNEO Digital!</p>
            <p>Untuk melengkapi pendaftaran, silakan verifikasi email Anda dengan klik tombol di bawah:</p>
            
            <div style="text-align: center;">
                <a href="<?= $verification_url ?>" class="btn">Verifikasi Email</a>
            </div>
            
            <p>Jika Anda tidak mendaftar di SITUNEO, abaikan email ini.</p>
            
            <p style="color: #666; font-size: 12px; margin-top: 30px;">
                Atau copy link berikut ke browser Anda:<br>
                <a href="<?= $verification_url ?>"><?= $verification_url ?></a>
            </p>
        </div>
        <div class="footer">
            <p><strong>PT SITUNEO DIGITAL SOLUSI INDONESIA</strong></p>
            <p>📧 vins@situneo.my.id | 📱 +62 831-7386-8915</p>
            <p>🌐 <a href="https://situneo.my.id">situneo.my.id</a></p>
        </div>
    </div>
</body>
</html>
```

---

## 🔑 PASSWORD RESET MODEL

```php
<?php
/**
 * File: /app/models/PasswordReset.php
 */

class PasswordReset extends Model {
    protected $table = 'password_resets';
    
    public static function findByToken($hashedToken) {
        $db = Database::getInstance();
        
        return $db->query(
            "SELECT * FROM password_resets 
             WHERE token = :token 
             AND expires_at > NOW() 
             LIMIT 1",
            ['token' => $hashedToken]
        )->fetch();
    }
    
    public static function deleteByUserId($userId) {
        $db = Database::getInstance();
        
        return $db->query(
            "DELETE FROM password_resets WHERE user_id = :user_id",
            ['user_id' => $userId]
        );
    }
    
    public static function create($data) {
        $db = Database::getInstance();
        
        return $db->query(
            "INSERT INTO password_resets (user_id, email, token, expires_at, created_at) 
             VALUES (:user_id, :email, :token, :expires_at, NOW())",
            $data
        );
    }
    
    public static function cleanExpired() {
        $db = Database::getInstance();
        
        return $db->query(
            "DELETE FROM password_resets WHERE expires_at < NOW()"
        );
    }
}
```

---

## 📧 EMAIL VERIFICATION MODEL

```php
<?php
/**
 * File: /app/models/EmailVerification.php
 */

class EmailVerification extends Model {
    protected $table = 'email_verifications';
    
    public static function findByToken($token) {
        $db = Database::getInstance();
        
        return $db->query(
            "SELECT * FROM email_verifications 
             WHERE token = :token 
             AND expires_at > NOW() 
             LIMIT 1",
            ['token' => $token]
        )->fetch();
    }
    
    public static function deleteByUserId($userId) {
        $db = Database::getInstance();
        
        return $db->query(
            "DELETE FROM email_verifications WHERE user_id = :user_id",
            ['user_id' => $userId]
        );
    }
    
    public static function create($data) {
        $db = Database::getInstance();
        
        return $db->query(
            "INSERT INTO email_verifications (user_id, email, token, expires_at, created_at) 
             VALUES (:user_id, :email, :token, :expires_at, NOW())",
            $data
        );
    }
}
```

---

## ✅ COMPLETE! BATCH 15 DONE!

**END OF MASTER BATCH 15**  
*Complete Auth System: Forgot Password, Reset, Verification*  
*Copy-Paste Ready Code ✅*  
*Versi 3.0 Final - 18 November 2025*

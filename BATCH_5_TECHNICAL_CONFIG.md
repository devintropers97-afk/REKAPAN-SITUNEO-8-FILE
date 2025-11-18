# 📘 SITUNEO DIGITAL - BATCH 5 (FINAL)
## KONFIGURASI TEKNIS, JAVASCRIPT, CSS & DESIGN SYSTEM

---

# ⚙️ KONFIGURASI TEKNIS WEBSITE

## PHP CONFIGURATION (config.php)

### Database Connection
```php
<?php
// Database configuration
define('DB_HOST', 'localhost');
define('DB_USER', 'nrrskfvk_user_situneo_digital');
define('DB_PASS', 'Devin1922$');
define('DB_NAME', 'nrrskfvk_situneo_digital');

// Create connection
$conn = new mysqli(DB_HOST, DB_USER, DB_PASS, DB_NAME);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

// Set charset
$conn->set_charset("utf8mb4");
?>
```

### Session Management
```php
<?php
// Start session
session_start();

// Session configuration
ini_set('session.cookie_httponly', 1);
ini_set('session.cookie_secure', 1);
ini_set('session.use_only_cookies', 1);
ini_set('session.gc_maxlifetime', 3600); // 1 hour

// Regenerate session ID
if (!isset($_SESSION['initiated'])) {
    session_regenerate_id(true);
    $_SESSION['initiated'] = true;
}
?>
```

### Security Headers
```php
<?php
// Security headers
header("X-Frame-Options: DENY");
header("X-Content-Type-Options: nosniff");
header("X-XSS-Protection: 1; mode=block");
header("Referrer-Policy: strict-origin-when-cross-origin");
header("Content-Security-Policy: default-src 'self'");
?>
```

### Error Handling
```php
<?php
// Error handling
error_reporting(E_ALL);
ini_set('display_errors', 0);
ini_set('log_errors', 1);
ini_set('error_log', __DIR__ . '/logs/error.log');

// Custom error handler
function customErrorHandler($errno, $errstr, $errfile, $errline) {
    $error_message = date('Y-m-d H:i:s') . " - Error [$errno]: $errstr in $errfile on line $errline\n";
    error_log($error_message, 3, __DIR__ . '/logs/custom_errors.log');
    return true;
}

set_error_handler("customErrorHandler");
?>
```

---

## AUTHENTICATION SYSTEM

### User Login Function
```php
<?php
function requireLogin() {
    if (!isset($_SESSION['user_id'])) {
        header('Location: login.php');
        exit();
    }
}

function isLoggedIn() {
    return isset($_SESSION['user_id']);
}

function getCurrentUser() {
    global $conn;
    if (!isLoggedIn()) {
        return null;
    }
    
    $user_id = $_SESSION['user_id'];
    $stmt = $conn->prepare("SELECT * FROM users WHERE id = ?");
    $stmt->bind_param("i", $user_id);
    $stmt->execute();
    $result = $stmt->get_result();
    return $result->fetch_assoc();
}

function hasRole($role) {
    $user = getCurrentUser();
    return $user && $user['role'] === $role;
}
?>
```

### Password Hashing
```php
<?php
function hashPassword($password) {
    return password_hash($password, PASSWORD_BCRYPT);
}

function verifyPassword($password, $hash) {
    return password_verify($password, $hash);
}
?>
```

---

# 🎨 CSS DESIGN SYSTEM

## Color Variables
```css
:root {
    /* Primary Colors */
    --primary-blue: #1E5C99;
    --dark-blue: #0F3057;
    --gold: #FFB400;
    --bright-gold: #FFD700;
    --white: #ffffff;
    --text-light: #e9ecef;
    
    /* Gradients */
    --gradient-primary: linear-gradient(135deg, #1E5C99 0%, #0F3057 100%);
    --gradient-gold: linear-gradient(135deg, #FFD700 0%, #FFB400 100%);
    
    /* Semantic Colors */
    --success: #2ecc71;
    --danger: #e74c3c;
    --warning: #f39c12;
    --info: #3498db;
    
    /* Neutral Colors */
    --gray-100: #f8f9fa;
    --gray-200: #e9ecef;
    --gray-300: #dee2e6;
    --gray-400: #ced4da;
    --gray-500: #adb5bd;
    --gray-600: #6c757d;
    --gray-700: #495057;
    --gray-800: #343a40;
    --gray-900: #212529;
    
    /* Spacing */
    --spacing-xs: 0.25rem;   /* 4px */
    --spacing-sm: 0.5rem;    /* 8px */
    --spacing-md: 1rem;      /* 16px */
    --spacing-lg: 1.5rem;    /* 24px */
    --spacing-xl: 2rem;      /* 32px */
    --spacing-xxl: 3rem;     /* 48px */
    
    /* Border Radius */
    --radius-sm: 5px;
    --radius-md: 10px;
    --radius-lg: 15px;
    --radius-xl: 20px;
    --radius-full: 9999px;
    
    /* Shadows */
    --shadow-sm: 0 2px 4px rgba(0, 0, 0, 0.1);
    --shadow-md: 0 4px 8px rgba(0, 0, 0, 0.15);
    --shadow-lg: 0 8px 16px rgba(0, 0, 0, 0.2);
    --shadow-xl: 0 12px 24px rgba(0, 0, 0, 0.25);
    
    /* Transitions */
    --transition-fast: 0.15s ease;
    --transition-base: 0.3s ease;
    --transition-slow: 0.5s ease;
}
```

## Typography System
```css
/* Font Imports */
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&family=Plus+Jakarta+Sans:wght@400;600;700;800;900&display=swap');

/* Base Typography */
body {
    font-family: 'Inter', sans-serif;
    font-size: 16px;
    line-height: 1.6;
    color: var(--white);
}

/* Headings */
h1, h2, h3, h4, h5, h6 {
    font-family: 'Plus Jakarta Sans', sans-serif;
    font-weight: 700;
    line-height: 1.2;
    margin-bottom: 1rem;
}

h1 {
    font-size: clamp(2rem, 5vw, 3.5rem);
    font-weight: 800;
}

h2 {
    font-size: clamp(1.75rem, 4vw, 2.5rem);
    font-weight: 700;
}

h3 {
    font-size: clamp(1.5rem, 3vw, 2rem);
    font-weight: 700;
}

h4 {
    font-size: clamp(1.25rem, 2.5vw, 1.5rem);
    font-weight: 600;
}

h5 {
    font-size: 1.125rem;
    font-weight: 600;
}

h6 {
    font-size: 1rem;
    font-weight: 600;
}

/* Paragraphs */
p {
    margin-bottom: 1rem;
}

/* Links */
a {
    color: var(--gold);
    text-decoration: none;
    transition: var(--transition-base);
}

a:hover {
    color: var(--bright-gold);
    text-decoration: underline;
}

/* Text Utilities */
.text-gradient {
    background: var(--gradient-gold);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
}
```

## Button System
```css
/* Base Button */
.btn {
    display: inline-block;
    padding: 12px 24px;
    font-family: 'Inter', sans-serif;
    font-size: 1rem;
    font-weight: 500;
    text-align: center;
    text-decoration: none;
    border: none;
    border-radius: var(--radius-md);
    cursor: pointer;
    transition: var(--transition-base);
}

/* Primary Button */
.btn-primary {
    background: var(--gradient-primary);
    color: var(--white);
    box-shadow: var(--shadow-md);
}

.btn-primary:hover {
    transform: translateY(-2px);
    box-shadow: var(--shadow-lg);
}

/* Gold Button */
.btn-gold {
    background: var(--gradient-gold);
    color: var(--dark-blue);
    box-shadow: var(--shadow-md);
}

.btn-gold:hover {
    transform: translateY(-2px);
    box-shadow: var(--shadow-lg);
}

/* Outline Button */
.btn-outline {
    background: transparent;
    color: var(--gold);
    border: 2px solid var(--gold);
}

.btn-outline:hover {
    background: var(--gold);
    color: var(--dark-blue);
}

/* Button Sizes */
.btn-sm {
    padding: 8px 16px;
    font-size: 0.875rem;
}

.btn-lg {
    padding: 16px 32px;
    font-size: 1.125rem;
}

/* Button with Icon */
.btn i {
    margin-right: 8px;
}
```

## Card Components
```css
/* Base Card */
.card {
    background: rgba(15, 48, 87, 0.6);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 180, 0, 0.2);
    border-radius: var(--radius-lg);
    padding: var(--spacing-lg);
    transition: var(--transition-base);
}

.card:hover {
    transform: translateY(-5px);
    box-shadow: var(--shadow-xl);
    border-color: var(--gold);
}

/* Card Header */
.card-header {
    background: rgba(15, 48, 87, 0.8);
    border-bottom: 1px solid rgba(255, 180, 0, 0.2);
    padding: var(--spacing-md);
    border-radius: var(--radius-lg) var(--radius-lg) 0 0;
}

/* Card Body */
.card-body {
    padding: var(--spacing-lg);
}

/* Card Footer */
.card-footer {
    background: rgba(15, 48, 87, 0.8);
    border-top: 1px solid rgba(255, 180, 0, 0.2);
    padding: var(--spacing-md);
    border-radius: 0 0 var(--radius-lg) var(--radius-lg);
}
```

## Grid System
```css
/* Container */
.container {
    width: 100%;
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 var(--spacing-md);
}

/* Grid */
.grid {
    display: grid;
    gap: var(--spacing-lg);
}

.grid-2 {
    grid-template-columns: repeat(2, 1fr);
}

.grid-3 {
    grid-template-columns: repeat(3, 1fr);
}

.grid-4 {
    grid-template-columns: repeat(4, 1fr);
}

/* Responsive Grid */
@media (max-width: 768px) {
    .grid-2,
    .grid-3,
    .grid-4 {
        grid-template-columns: 1fr;
    }
}
```

---

# 📱 JAVASCRIPT FUNCTIONALITY

## Smooth Scrolling
```javascript
// Smooth scrolling for anchor links
document.addEventListener('DOMContentLoaded', function() {
    document.querySelectorAll('a[href^="#"]').forEach(anchor => {
        anchor.addEventListener('click', function (e) {
            e.preventDefault();
            
            const target = document.querySelector(this.getAttribute('href'));
            if (target) {
                target.scrollIntoView({
                    behavior: 'smooth',
                    block: 'start'
                });
            }
        });
    });
});
```

## Navigation Active State
```javascript
// Add active class to current nav item
const currentLocation = location.pathname;
const menuItems = document.querySelectorAll('.navbar-nav .nav-link');

menuItems.forEach(item => {
    if (item.href.includes(currentLocation)) {
        item.classList.add('active');
    }
});
```

## Navbar Scroll Effect
```javascript
// Navbar background on scroll
window.addEventListener('scroll', function() {
    const navbar = document.querySelector('.navbar-premium');
    
    if (window.scrollY > 50) {
        navbar.classList.add('scrolled');
    } else {
        navbar.classList.remove('scrolled');
    }
});
```

## Network Animation Background
```javascript
// Network Background Animation
const canvas = document.createElement('canvas');
const ctx = canvas.getContext('2d');
const networkBg = document.getElementById('networkBg');
networkBg.appendChild(canvas);

function resizeCanvas() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
}

resizeCanvas();
window.addEventListener('resize', resizeCanvas);

const nodes = [];
const nodeCount = 50;
const connectionDistance = 150;

class Node {
    constructor() {
        this.x = Math.random() * canvas.width;
        this.y = Math.random() * canvas.height;
        this.vx = (Math.random() - 0.5) * 0.5;
        this.vy = (Math.random() - 0.5) * 0.5;
        this.radius = Math.random() * 2 + 1;
    }
    
    update() {
        this.x += this.vx;
        this.y += this.vy;
        
        if (this.x < 0 || this.x > canvas.width) this.vx = -this.vx;
        if (this.y < 0 || this.y > canvas.height) this.vy = -this.vy;
    }
    
    draw() {
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
        ctx.fillStyle = 'rgba(255, 180, 0, 0.7)';
        ctx.fill();
    }
}

for (let i = 0; i < nodeCount; i++) {
    nodes.push(new Node());
}

function animate() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    
    // Update and draw nodes
    nodes.forEach(node => {
        node.update();
        node.draw();
    });
    
    // Draw connections
    for (let i = 0; i < nodes.length; i++) {
        for (let j = i + 1; j < nodes.length; j++) {
            const dx = nodes[i].x - nodes[j].x;
            const dy = nodes[i].y - nodes[j].y;
            const distance = Math.sqrt(dx * dx + dy * dy);
            
            if (distance < connectionDistance) {
                ctx.beginPath();
                ctx.moveTo(nodes[i].x, nodes[i].y);
                ctx.lineTo(nodes[j].x, nodes[j].y);
                ctx.strokeStyle = `rgba(255, 180, 0, ${0.2 * (1 - distance / connectionDistance)})`;
                ctx.stroke();
            }
        }
    }
    
    requestAnimationFrame(animate);
}

animate();
```

## Form Validation
```javascript
// Form validation
function validateForm(formId) {
    const form = document.getElementById(formId);
    
    form.addEventListener('submit', function(e) {
        e.preventDefault();
        
        let isValid = true;
        const inputs = form.querySelectorAll('input[required], textarea[required]');
        
        inputs.forEach(input => {
            if (!input.value.trim()) {
                isValid = false;
                input.classList.add('error');
                
                // Show error message
                const errorMsg = input.nextElementSibling;
                if (errorMsg && errorMsg.classList.contains('error-message')) {
                    errorMsg.style.display = 'block';
                }
            } else {
                input.classList.remove('error');
                
                // Hide error message
                const errorMsg = input.nextElementSibling;
                if (errorMsg && errorMsg.classList.contains('error-message')) {
                    errorMsg.style.display = 'none';
                }
            }
        });
        
        if (isValid) {
            form.submit();
        }
    });
}
```

## Loading Spinner
```javascript
// Show loading spinner
function showLoading() {
    const loader = document.createElement('div');
    loader.id = 'loading-spinner';
    loader.innerHTML = `
        <div class="spinner-overlay">
            <div class="spinner"></div>
        </div>
    `;
    document.body.appendChild(loader);
}

function hideLoading() {
    const loader = document.getElementById('loading-spinner');
    if (loader) {
        loader.remove();
    }
}
```

---

# 🔒 SECURITY BEST PRACTICES

## SQL Injection Prevention
```php
<?php
// GOOD: Using prepared statements
$stmt = $conn->prepare("SELECT * FROM users WHERE email = ?");
$stmt->bind_param("s", $email);
$stmt->execute();
$result = $stmt->get_result();

// BAD: Direct concatenation (DON'T DO THIS)
// $query = "SELECT * FROM users WHERE email = '$email'";
?>
```

## XSS Prevention
```php
<?php
// Always escape output
echo htmlspecialchars($user_input, ENT_QUOTES, 'UTF-8');

// For HTML content
function cleanHTML($html) {
    return htmlspecialchars($html, ENT_QUOTES, 'UTF-8');
}
?>
```

## CSRF Protection
```php
<?php
// Generate CSRF token
function generateCSRFToken() {
    if (!isset($_SESSION['csrf_token'])) {
        $_SESSION['csrf_token'] = bin2hex(random_bytes(32));
    }
    return $_SESSION['csrf_token'];
}

// Verify CSRF token
function verifyCSRFToken($token) {
    return isset($_SESSION['csrf_token']) && hash_equals($_SESSION['csrf_token'], $token);
}

// In form
?>
<input type="hidden" name="csrf_token" value="<?= generateCSRFToken() ?>">
```

## File Upload Security
```php
<?php
function secureFileUpload($file, $uploadDir = 'uploads/') {
    // Check if file is uploaded
    if (!isset($file) || $file['error'] !== UPLOAD_ERR_OK) {
        return ['success' => false, 'message' => 'Upload error'];
    }
    
    // Validate file type
    $allowedTypes = ['image/jpeg', 'image/png', 'image/gif', 'application/pdf'];
    if (!in_array($file['type'], $allowedTypes)) {
        return ['success' => false, 'message' => 'Invalid file type'];
    }
    
    // Validate file size (max 5MB)
    if ($file['size'] > 5 * 1024 * 1024) {
        return ['success' => false, 'message' => 'File too large'];
    }
    
    // Generate unique filename
    $extension = pathinfo($file['name'], PATHINFO_EXTENSION);
    $filename = uniqid() . '.' . $extension;
    $destination = $uploadDir . $filename;
    
    // Move file
    if (move_uploaded_file($file['tmp_name'], $destination)) {
        return ['success' => true, 'filename' => $filename];
    }
    
    return ['success' => false, 'message' => 'Upload failed'];
}
?>
```

---

# 📊 PERFORMANCE OPTIMIZATION

## Image Optimization
```html
<!-- Lazy loading images -->
<img src="image.jpg" loading="lazy" alt="Description">

<!-- Responsive images -->
<img 
    src="image-small.jpg"
    srcset="image-small.jpg 480w, image-medium.jpg 800w, image-large.jpg 1200w"
    sizes="(max-width: 600px) 480px, (max-width: 900px) 800px, 1200px"
    alt="Description"
>
```

## CSS Optimization
```css
/* Use efficient selectors */
.class-name { } /* Good */
#id-name { } /* Good */
div.class-name { } /* Avoid */
div > div > span { } /* Avoid */

/* Minimize repaints and reflows */
.element {
    transform: translateX(10px); /* Better than left: 10px */
    opacity: 0.5; /* Better than visibility */
}
```

## JavaScript Optimization
```javascript
// Debounce function for scroll/resize events
function debounce(func, wait) {
    let timeout;
    return function executedFunction(...args) {
        const later = () => {
            clearTimeout(timeout);
            func(...args);
        };
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
    };
}

// Usage
window.addEventListener('scroll', debounce(function() {
    // Expensive operation here
}, 100));
```

---

# 📱 RESPONSIVE DESIGN

## Media Queries
```css
/* Mobile First Approach */
/* Base styles (mobile) */
.container {
    padding: 1rem;
}

/* Tablet */
@media (min-width: 768px) {
    .container {
        padding: 2rem;
    }
}

/* Desktop */
@media (min-width: 1024px) {
    .container {
        padding: 3rem;
    }
}

/* Large Desktop */
@media (min-width: 1440px) {
    .container {
        padding: 4rem;
    }
}
```

## Responsive Typography
```css
/* Fluid typography */
h1 {
    font-size: clamp(2rem, 5vw, 4rem);
}

p {
    font-size: clamp(1rem, 2vw, 1.125rem);
}
```

---

# ✅ CHECKLIST DEPLOYMENT

## Pre-Launch Checklist
- [ ] All forms tested and working
- [ ] Database backup created
- [ ] SSL certificate installed
- [ ] .htaccess configured
- [ ] Error pages (404, 500) created
- [ ] robots.txt configured
- [ ] Sitemap.xml generated
- [ ] Google Analytics installed
- [ ] Meta tags verified
- [ ] Social media meta tags added
- [ ] Favicon added
- [ ] Mobile responsive tested
- [ ] Cross-browser tested
- [ ] Page speed optimized
- [ ] Security headers configured
- [ ] Contact forms tested
- [ ] Email delivery tested
- [ ] Payment gateway tested (if applicable)

---

**© 2025 SITUNEO DIGITAL - All Rights Reserved**  
**NIB**: 20250-9261-4570-4515-5453

---

*END OF BATCH 5 (FINAL)*
*END OF ALL DOCUMENTATION*

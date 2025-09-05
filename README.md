Daily Expense Tracker System Documentation

1. Project Overview

The Daily Expense Tracker System is a web-based application designed to help users manage their personal finances by tracking daily expenses, setting budgets, and generating reports. It includes role-based access control with Admin and User roles, ensuring admins can manage the system while users focus on their financial data. The system uses PHP for server-side logic, MySQL for data storage, Bootstrap for a responsive frontend, and Chart.js for visualizations. Modal alerts enhance user experience by providing real-time feedback for actions like expense addition, budget overruns, and user management.

 Objectives
- Enable users to log, categorize, and analyze expenses.
- Allow admins to manage users and global categories.
- Provide a secure, user-friendly, and responsive interface.
- Implement modal-based alerts for seamless interaction.

 Target Audience
- Users: Individuals tracking personal or household expenses.
- Admins: System administrators managing users and configurations.

 2. Features

 User Features
- Authentication: Register, login, and logout with email and password.
- Expense Management: Add, edit, delete, and view expenses with details (amount, category, date, description).
- Category Management: Create personal categories and use admin-defined global categories.
- Budget Tracking: Set monthly budgets per category with alerts for 50%, 80%, and 100% thresholds.
- Reports: View pie charts of expenses by category.
- Modal Alerts: Receive success, warning, error, or info alerts for actions (e.g., expense added, budget exceeded).

 Admin Features
- User Management: View, edit, delete, or suspend user accounts.
- Global Category Management: Create, edit, and delete global categories available to all users.
- System Analytics: View system-wide expense trends across all users.
- Audit Logs: Track admin actions (e.g., user deletion, category changes).
- Modal Alerts: Confirm critical actions (e.g., user suspension) and receive success/error feedback.

 General Features
- Responsive Design: Mobile-friendly interface using Bootstrap.
- Security: Password hashing, session management, and XSS prevention.
- Animations: Modal alerts with fade-in effects using Animate.css.
- Accessibility: ARIA attributes and keyboard navigation for modals.

 3. System Architecture

 Technology Stack
- Frontend: HTML, CSS (Bootstrap 4.6.2), JavaScript (jQuery 3.6.0, Chart.js, Animate.css).
- Backend: PHP 7.4+ for server-side logic and API handling.
- Database: MySQL 5.7+ for relational data storage.
- Dependencies:
  - Bootstrap: `https://cdn.jsdelivr.net/npm/bootstrap@4.6.2/dist/css/bootstrap.min.css`
  - jQuery: `https://code.jquery.com/jquery-3.6.0.min.js`
  - Chart.js: `https://cdn.jsdelivr.net/npm/chart.js`
  - Animate.css: `https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css`

 Project Structure
```
expense-tracker/
├── css/
│   └── styles.css (Custom styles)
├── js/
│   └── scripts.js (Custom JavaScript)
├── sounds/
│   ├── success.mp3
│   ├── warning.mp3
│   └── error.mp3
├── includes/
│   ├── db.php (Database connection)
│   ├── alerts.php (Alert handling)
│   ├── modals.php (Modal template)
│   ├── header.php (Navigation bar)
│   └── footer.php
├── auth/
│   ├── login.php
│   ├── register.php
│   └── logout.php
├── user/
│   ├── dashboard.php (User expense overview)
│   ├── add_expense.php
│   ├── edit_expense.php
│   ├── delete_expense.php
│   ├── view_expenses.php
│   ├── add_category.php
│   ├── edit_category.php
│   ├── delete_category.php
│   ├── set_budget.php
│   └── view_budget.php
├── admin/
│   ├── dashboard.php (System analytics)
│   ├── manage_users.php
│   ├── manage_categories.php
│   └── system_settings.php
├── index.php (Home page)
└── .htaccess (Optional: URL rewriting)
```

 Database Schema
```sql
CREATE DATABASE expense_tracker;
USE expense_tracker;

-- Users table
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    role ENUM('admin', 'user') DEFAULT 'user' NOT NULL,
    status ENUM('active', 'suspended') DEFAULT 'active' NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Categories table
CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NULL,
    name VARCHAR(50) NOT NULL,
    is_global BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- Expenses table
CREATE TABLE expenses (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    category_id INT,
    amount DECIMAL(10, 2) NOT NULL,
    description TEXT,
    expense_date DATE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE CASCADE
);

-- Budgets table
CREATE TABLE budgets (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    category_id INT,
    amount DECIMAL(10, 2) NOT NULL,
    month YEARMONTH NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE CASCADE
);

-- Audit Logs table
CREATE TABLE audit_logs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    admin_id INT,
    action VARCHAR(255) NOT NULL,
    details TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (admin_id) REFERENCES users(id)
);
```

 4. Installation

 Prerequisites
- Server: XAMPP/WAMP with PHP 7.4+ and MySQL 5.7+.
- Browser: Modern browser (Chrome, Firefox, Safari).
- Dependencies: Ensure internet access for CDN-hosted libraries (Bootstrap, jQuery, Chart.js, Animate.css).

 Setup Steps
1. Clone Repository:
   ```bash
   git clone <repository_url>
   cd expense-tracker
   ```
2. Configure Database:
   - Start XAMPP/WAMP and ensure MySQL is running.
   - Create the database and tables using the provided SQL schema.
   - Update `includes/db.php` with your database credentials:
     ```php
     $host = "localhost";
     $user = "root";
     $password = "";
     $dbname = "expense_tracker";
     ```
3. Setup Audio Files (Optional):
   - Place `success.mp3`, `warning.mp3`, and `error.mp3` in the `sounds/` folder.
   - Use short, low-volume audio clips (1-2 seconds).
4. Deploy Application:
   - Move the `expense-tracker` folder to `htdocs` (XAMPP) or `www` (WAMP).
   - Access via `http://localhost/expense-tracker`.
5. Create Admin Account:
   ```sql
   INSERT INTO users (username, email, password, role, status) 
   VALUES ('admin', 'admin@example.com', '$2y$10$<hashed_password>', 'admin', 'active');
   ```
   Generate a hashed password using PHP’s `password_hash()`.

 Deployment to Production
- Use a hosting provider supporting PHP/MySQL (e.g., Hostinger, Bluehost).
- Configure `.htaccess` for clean URLs:
  ```apache
  RewriteEngine On
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule ^(.*)$ index.php/$1 [L]
  ```
- Enable HTTPS for secure sessions.
- Set up a cron job for database backups.

 5. Usage Guide

 User Guide
1. Register:
   - Navigate to `auth/register.php`.
   - Enter username, email, and password.
   - Receive a success modal and redirect to login.
2. Login:
   - Go to `auth/login.php`.
   - Enter credentials; suspended users see an error modal.
   - Redirect to `user/dashboard.php`.
3. Manage Expenses:
   - Add expenses via `user/add_expense.php` with category, amount, date, and description.
   - Receive success or budget warning modals.
   - View, edit, or delete expenses in `user/view_expenses.php` or `user/delete_expense.php`.
4. Manage Categories:
   - Add personal categories in `user/add_category.php`.
   - Use global categories set by admins.
5. Set Budgets:
   - Set monthly budgets in `user/set_budget.php`.
   - Receive alerts for 50%, 80%, or 100% budget usage on `user/dashboard.php`.
6. View Reports:
   - See expense breakdowns via pie charts on `user/dashboard.php`.

 Admin Guide
1. Login:
   - Log in as an admin to access `admin/dashboard.php`.
2. Manage Users:
   - View, edit, suspend, or delete users in `admin/manage_users.php`.
   - Confirm suspensions/deletions via modals; actions are logged in `audit_logs`.
3. Manage Global Categories:
   - Add, edit, or delete global categories in `admin/manage_categories.php`.
   - Deletion is blocked if categories are used in expenses, with an error modal.
4. View System Analytics:
   - See system-wide expense trends via bar charts in `admin/dashboard.php`.

 6. Key Components

 Modal Alert System
- Purpose: Display success, warning, error, or info messages using Bootstrap modals.
- Implementation:
  - Defined in `includes/modals.php` with a reusable modal template.
  - JavaScript `showAlert` function handles queuing, animations (Animate.css), and optional sound effects.
  - PHP `setAlert` and `displayAlert` functions in `includes/alerts.php` manage alerts server-side.
- Scenarios:
  - User: Registration success, expense addition, budget creation, budget threshold alerts (50%, 80%, 100%), expense deletion confirmation.
  - Admin: User suspension/deletion confirmation, global category addition/deletion, error for invalid actions.
- Accessibility: ARIA attributes (`role="alertdialog"`, `aria-live="polite"`) and keyboard focus management.

 Security Features
- Password Hashing: Uses `password_hash` for secure storage.
- Session Management: Checks `$_SESSION['user_id']` and `$_SESSION['role']` for access control.
- Input Sanitization: Uses `mysqli_real_escape_string` and `.text()` in JavaScript to prevent XSS.
- Role-Based Access: `restrictToAdmin()` ensures admin-only access to admin pages.
- User Status: Suspended users are blocked from logging in.

 Performance Optimizations
- Database Indexes:
  ```sql
  CREATE INDEX idx_user_id ON expenses(user_id);
  CREATE INDEX idx_category_id ON expenses(category_id);
  ```
- Alert Queuing: Prevents modal overlap by processing alerts sequentially.
- Auto-Close Modals: Non-critical alerts close after 3 seconds.

 7. Maintenance and Extensibility

 Maintenance
- Database Backups: Schedule daily MySQL backups using cron jobs or hosting tools.
- Dependency Updates: Monitor CDN links for Bootstrap, jQuery, and Chart.js updates.
- Log Monitoring: Review `audit_logs` for admin activity.
- User Feedback: Collect feedback via a form (e.g., `user/feedback.php`) to identify issues.

 Future Enhancements
- Recurring Expenses: Add a `recurring_expenses` table for automatic expense logging.
- Multi-Language Support: Store translations in MySQL and use PHP’s `gettext`.
- Bank API Integration: Use Plaid or Yodlee for automatic transaction imports.
- Persistent Notifications: Store alerts in a `notifications` table for later review.
- Custom Icons: Add Font Awesome icons to modals for visual cues.

 8. Troubleshooting
- Modal Not Showing: Check JavaScript console for errors; ensure jQuery and Bootstrap JS are loaded.
- Database Errors: Verify `db.php` credentials and table schema.
- Session Issues: Ensure HTTPS is used in production to secure sessions.
- Budget Alerts Missing: Confirm budgets are set in the `budgets` table and dates match.

 9. Contact
For support, contact the admin at `admin@example.com` or submit feedback via the application.

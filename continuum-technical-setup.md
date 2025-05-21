# Continuum Technical Setup Guide

## Overview

This guide provides comprehensive instructions for setting up Continuum in both development and production environments. It covers server requirements, installation steps, configuration, database setup, and troubleshooting common issues.

## Server Requirements

### Minimum Requirements

- **Web Server**: Apache 2.4+ with mod_rewrite enabled
- **PHP**: Version 8.2 or higher
- **MySQL/MariaDB**: Version 8.0+ / 10.5+
- **Node.js**: Version 18.0+ (for frontend development)
- **npm**: Version 8.0+ (comes with Node.js)

### Recommended Server Specifications

- **CPU**: 2+ cores
- **RAM**: 4GB+ (2GB minimum)
- **Disk Space**: 20GB+ for application and database
- **Operating System**: Ubuntu 22.04 LTS or similar Linux distribution

## Development Environment Setup

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/continuum.git
cd continuum
```

### 2. Set Up the Backend

#### Starting Apache Server

On Ubuntu/Debian:
```bash
sudo systemctl start apache2
sudo systemctl enable apache2  # Start on boot
sudo systemctl status apache2  # Verify it is running
```

On CentOS/RHEL:
```bash
sudo systemctl start httpd
sudo systemctl enable httpd  # Start on boot
sudo systemctl status httpd  # Verify it is running
```

On macOS (using Homebrew):
```bash
brew services start httpd
```

On Windows (XAMPP):
```
Start XAMPP Control Panel and click Start next to Apache
```

#### Configure Apache Virtual Host

Create a new virtual host configuration in Apache:

```apache
<VirtualHost *:80>
    ServerName continuum.local
    DocumentRoot /path/to/continuum
    
    <Directory /path/to/continuum>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/continuum-error.log
    CustomLog ${APACHE_LOG_DIR}/continuum-access.log combined
</VirtualHost>
```

Add the following entry to your hosts file:

```
127.0.0.1 continuum.local
```

Restart Apache to apply changes:

On Ubuntu/Debian:
```bash
sudo systemctl restart apache2
```

On CentOS/RHEL:
```bash
sudo systemctl restart httpd
```

On macOS:
```bash
brew services restart httpd
```

On Windows (XAMPP):
```
Click Stop then Start for Apache in XAMPP Control Panel
```

#### Configure PHP

Ensure the following PHP extensions are enabled:

- pdo_mysql
- mbstring
- json
- curl
- fileinfo
- gd

You can check and enable them using:

```bash
php -m | grep extension_name
sudo phpenmod extension_name  # On Ubuntu/Debian
```

#### Set Up the Database

1. Start MySQL/MariaDB server:

On Ubuntu/Debian:
```bash
sudo systemctl start mysql
sudo systemctl enable mysql  # Start on boot
sudo systemctl status mysql  # Verify it is running
```

On CentOS/RHEL:
```bash
sudo systemctl start mariadb
sudo systemctl enable mariadb  # Start on boot
sudo systemctl status mariadb  # Verify it is running
```

On macOS:
```bash
brew services start mysql
```

On Windows (XAMPP):
```
Click Start next to MySQL in XAMPP Control Panel
```

2. Create a new MySQL/MariaDB database:

```sql
CREATE DATABASE continuum CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'continuum_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON continuum.* TO 'continuum_user'@'localhost';
FLUSH PRIVILEGES;
```

3. Configure the database connection in `api/config/database.php`:

```php
<?php
class Database {
    private $host = "localhost";
    private $db_name = "continuum";
    private $username = "continuum_user";
    private $password = "your_password";
    private $conn;
    
    public function getConnection() {
        $this->conn = null;
        
        try {
            $this->conn = new PDO("mysql:host=" . $this->host . ";dbname=" . $this->db_name, $this->username, $this->password);
            $this->conn->exec("set names utf8mb4");
        } catch(PDOException $exception) {
            echo "Connection error: " . $exception->getMessage();
        }
        
        return $this->conn;
    }
}
```

4. Initialize the database schema:

```bash
php init-db.php
```

### 3. Set Up the Frontend

1. Navigate to the client directory:

```bash
cd client
```

2. Install dependencies:

```bash
npm install
```

3. Configure the API base URL in `client/vite.config.js`:

```javascript
export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/AgmentW/api': {
        target: 'http://localhost',
        changeOrigin: true,
      }
    }
  },
  // ... other config
})
```

4. Start the development server:

```bash
npm run dev
```

This will start the Node.js development server, typically on port 5173.

5. Access the application at `http://localhost:5173`

## Running the Application

### Starting All Services

To run the complete application, you need to start both the backend (Apache + PHP + MySQL) and frontend (Node.js) servers.

#### Quick Start Script (Linux/macOS)

Create a `start.sh` script in your project root:

```bash
#!/bin/bash

# Start Apache (if not already running)
sudo systemctl start apache2

# Start MySQL (if not already running)
sudo systemctl start mysql

# Start frontend development server
cd client
npm run dev
```

Make it executable and run it:

```bash
chmod +x start.sh
./start.sh
```

#### Windows Batch Script

Create a `start.bat` file in your project root:

```batch
@echo off
echo Starting Continuum...

REM Start XAMPP services if not using start.bat from XAMPP
REM start /B C:\xampp\xampp_start.exe

REM Start frontend
cd client
npm run dev
```

## Production Environment Setup

### 1. Server Preparation

1. Update the server:

```bash
sudo apt update
sudo apt upgrade -y
```

2. Install required packages:

```bash
sudo apt install apache2 mysql-server php8.2 php8.2-mysql php8.2-mbstring php8.2-json php8.2-curl php8.2-fileinfo php8.2-gd git nodejs npm -y
```

3. Enable required Apache modules:

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

### 2. Application Deployment

1. Clone the repository to your server:

```bash
cd /var/www
sudo git clone https://github.com/yourusername/continuum.git
sudo chown -R www-data:www-data continuum
cd continuum
```

2. Set up the database (same as development environment).

3. Build the frontend for production:

```bash
cd client
npm install
npm run build
```

4. Configure Apache virtual host for production:

```apache
<VirtualHost *:80>
    ServerName your-domain.com
    DocumentRoot /var/www/continuum
    
    <Directory /var/www/continuum>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/continuum-error.log
    CustomLog ${APACHE_LOG_DIR}/continuum-access.log combined
</VirtualHost>
```

5. Enable the site and restart Apache:

```bash
sudo a2ensite continuum.conf
sudo systemctl restart apache2
```

### 3. Security Considerations

1. Set up HTTPS with Let's Encrypt:

```bash
sudo apt install certbot python3-certbot-apache
sudo certbot --apache -d your-domain.com
```

2. Configure secure headers in Apache:

```apache
<IfModule mod_headers.c>
    Header always set X-Content-Type-Options "nosniff"
    Header always set X-XSS-Protection "1; mode=block"
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set Referrer-Policy "strict-origin-when-cross-origin"
    Header always set Content-Security-Policy "default-src 'self';"
</IfModule>
```

3. Set proper file permissions:

```bash
sudo find /var/www/continuum -type f -exec chmod 644 {} \;
sudo find /var/www/continuum -type d -exec chmod 755 {} \;
```

## Environment Variables

Continuum uses the following environment variables that can be configured in `api/config/core.php`:

```php
// API version
define('API_VERSION', 'v1');

// JWT settings
define('JWT_SECRET', 'your_jwt_secret_key'); // Change this to a secure random string
define('JWT_ISSUER', 'continuum_api');
define('JWT_AUDIENCE', 'continuum_client');
define('JWT_EXPIRATION', 3600); // Token expiration in seconds (1 hour)

// CORS settings
define('ALLOW_ORIGIN', '*'); // Change to specific domains in production
define('ALLOW_METHODS', 'GET, POST, PUT, DELETE, OPTIONS');
define('ALLOW_HEADERS', 'Origin, Content-Type, X-Auth-Token, Authorization');
```

## Database Migrations

When updating Continuum to a new version, you may need to run database migrations:

```bash
php migrations/run.php
```

This will apply any pending database schema changes.

## Troubleshooting

### Common Issues

#### Apache or MySQL Won't Start

- Check for port conflicts:
  ```bash
  sudo netstat -tulpn | grep 80  # Check if port 80 is in use
  sudo netstat -tulpn | grep 3306  # Check if MySQL port is in use
  ```
- Check service status:
  ```bash
  sudo systemctl status apache2
  sudo systemctl status mysql
  ```
- Check error logs (see below)

#### Node.js Server Won't Start

- Check for port conflicts:
  ```bash
  sudo netstat -tulpn | grep 5173  # Check if port 5173 is in use
  ```
- Clear node_modules and reinstall:
  ```bash
  rm -rf node_modules
  npm install
  ```
- Check for JavaScript errors in the console

#### API Endpoints Return 404

- Ensure mod_rewrite is enabled in Apache
- Check that .htaccess files are being processed (AllowOverride All)
- Verify the API base URL in the frontend configuration

#### Database Connection Errors

- Verify database credentials in `api/config/database.php`
- Ensure the MySQL/MariaDB service is running
- Check that the database user has proper permissions

#### CORS Issues

- Check the CORS settings in `api/config/core.php`
- Ensure the frontend is making requests to the correct API URL
- Verify that the server is returning proper CORS headers

#### JWT Authentication Failures

- Check that the JWT_SECRET is properly set
- Ensure the token is being sent in the Authorization header
- Verify that the token has not expired

#### Frontend Build Errors

- Clear the node_modules directory and reinstall dependencies
- Ensure you're using compatible Node.js and npm versions
- Check for syntax errors in your JavaScript/JSX files

### Logs

Check the following logs for troubleshooting:

- Apache error logs: `/var/log/apache2/error.log`
- Application error logs: `/var/log/apache2/continuum-error.log`
- PHP error logs: `/var/log/php/php_errors.log`
- MySQL/MariaDB logs: `/var/log/mysql/error.log`
- Node.js logs: Check the terminal where you ran `npm run dev`

## Support

If you encounter issues not covered in this guide, please:

1. Check the [GitHub Issues](https://github.com/yourusername/continuum/issues) for similar problems
2. Create a new issue with detailed information about your environment and the problem
3. Contact the development team at support@continuum.dev

## Maintenance

### Backup Procedures

1. Database backup:

```bash
mysqldump -u continuum_user -p continuum > continuum_backup_$(date +%Y%m%d).sql
```

2. Application backup:

```bash
tar -czvf continuum_app_backup_$(date +%Y%m%d).tar.gz /path/to/continuum
```

### Updates

To update Continuum to the latest version:

```bash
cd /path/to/continuum
git pull
php migrations/run.php
cd client
npm install
npm run build
```
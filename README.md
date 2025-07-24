## Full Deployment Guide: React + Node.js App deployed on  AWS EC2 Ubuntu Instance and WordPress on a different environment.

---

### README: Project Overview & Deployment Instructions

This project includes:

1. **Nairobi Restaurant Finder App** (React + Node.js + Google Maps API)
2. **Custom WordPress Blog-Theme**

The Restaurant Finer is deployed on  **AWS EC2 Ubuntu instance**.The WordPress Blog is deployed on a different enviornment due to web server port conflicts.

---

### 🚀 Deployment Summary

#### 🖥️ Requirements

* Ubuntu 22.04 EC2 instance
* Node.js & npm
* Nginx
* Apache, MySQL, PHP (for WordPress)
* Google Maps & Places API Key
* SSH access to EC2 instance

---

### 1. Backend Setup (Node.js + TypeScript)

```bash
# SSH into EC2 and set up project directories
mkdir -p ~/projects/restaurant-finder/backend
cd ~/projects/restaurant-finder/backend

# Initialize Node project
npm init -y
npm install express axios cors dotenv
npm install --save-dev typescript ts-node-dev @types/node @types/express
npx tsc --init
```

Add the API code to `src/index.ts`, and create a `.env` with your Google API key.

```bash
npm run build
node dist/index.js &
```

---

### 2. Frontend Setup (React + TypeScript)

```bash
cd ~/projects/restaurant-finder
npx create-react-app frontend --template typescript
cd frontend
npm install @react-google-maps/api
```

Build the frontend:

```bash
npm run build
sudo mv build /var/www/html/restaurant
```

---

### 3. NGINX Configuration

Edit `/etc/nginx/sites-available/default` to serve the React app and proxy API calls:

```nginx
server {
    listen 80;
    server_name your_domain_or_ip;

    location / {
        root /var/www/html/restaurant;
        index index.html;
        try_files $uri /index.html;
    }

    location /api/ {
        proxy_pass http://localhost:3001/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
    }
}
```

```bash
sudo nginx -t && sudo systemctl reload nginx
```

---

### 4. WordPress Micro-Theme Setup

Install Apache, MySQL, PHP, and WordPress:

```bash
sudo apt install apache2 mysql-server php php-mysql libapache2-mod-php php-cli unzip -y
cd /var/www/html
sudo curl -O https://wordpress.org/latest.zip
sudo unzip latest.zip
sudo chown -R www-data:www-data wordpress
```

Create your custom theme in:

```bash
cd /var/www/html/wordpress/wp-content/themes/micro-theme
```

Add `style.css`, `functions.php`, and `index.php` as described above.
Activate the theme in the WordPress admin dashboard.

---

### ✅ Access Instructions

* React frontend: http://16.171.148.40/restaurant
* WordPress site: https://aurison-technologies.co.ke/simpleblog/
* Node backend: runs on port 3001 (proxied via Nginx)

---

### 🧾 Author & Credits

* Developed by: \Michieka Omete
* GitHub Repos: \[restaurant-finder],

---

This project demonstrates full-stack deployment on a single cloud instance, combining modern JavaScript frameworks.

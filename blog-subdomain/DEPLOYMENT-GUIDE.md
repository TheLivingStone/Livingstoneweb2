# Blog Subdomain Deployment Guide
## blog.thelivingstonefoundation.com

This guide provides step-by-step instructions for deploying the blog subdomain.

---

## 📋 Pre-Deployment Checklist

- [ ] Domain ownership confirmed for thelivingstonefoundation.com
- [ ] Access to DNS management panel
- [ ] Web server access (Apache/Nginx)
- [ ] SSL certificate ready or Let's Encrypt configured
- [ ] FTP/SFTP or Git deployment method set up

---

## 🚀 Deployment Methods

### Method 1: Subdomain Configuration (Recommended for blog.thelivingstonefoundation.com)

#### Step 1: DNS Configuration

**Option A: A Record (if using dedicated IP)**
```
Type: A
Name: blog
Value: YOUR_SERVER_IP_ADDRESS
TTL: 3600 (or default)
```

**Option B: CNAME Record (if pointing to main domain)**
```
Type: CNAME
Name: blog
Value: thelivingstonefoundation.com
TTL: 3600 (or default)
```

**DNS Propagation**: Allow 1-24 hours for DNS changes to propagate globally.

#### Step 2: Upload Files

Upload the entire `blog-subdomain` directory to your server:

**Via FTP/SFTP:**
```bash
# Local path
/Users/oliyaddeyasa/Desktop/The Livingstone Foundation Website/blog-subdomain/

# Upload to server path
/var/www/thelivingstonefoundation.com/blog-subdomain/
# OR
/home/username/public_html/blog-subdomain/
```

**Via Git:**
```bash
# On server
cd /var/www/thelivingstonefoundation.com/
git clone YOUR_REPO_URL blog-subdomain
```

#### Step 3: Apache Configuration

Create/edit virtual host configuration:

```apache
<VirtualHost *:80>
    ServerName blog.thelivingstonefoundation.com
    ServerAlias www.blog.thelivingstonefoundation.com
    DocumentRoot /var/www/thelivingstonefoundation.com/blog-subdomain

    <Directory /var/www/thelivingstonefoundation.com/blog-subdomain>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        # Enable clean URLs
        <IfModule mod_rewrite.c>
            RewriteEngine On
            RewriteBase /
            RewriteCond %{REQUEST_FILENAME} !-f
            RewriteCond %{REQUEST_FILENAME} !-d
            RewriteRule ^(.*)$ index.html [L]
        </IfModule>
    </Directory>

    # Logging
    ErrorLog ${APACHE_LOG_DIR}/blog-error.log
    CustomLog ${APACHE_LOG_DIR}/blog-access.log combined
</VirtualHost>

# SSL Configuration (Port 443)
<VirtualHost *:443>
    ServerName blog.thelivingstonefoundation.com
    ServerAlias www.blog.thelivingstonefoundation.com
    DocumentRoot /var/www/thelivingstonefoundation.com/blog-subdomain

    SSLEngine on
    SSLCertificateFile /path/to/certificate.crt
    SSLCertificateKeyFile /path/to/private.key
    SSLCertificateChainFile /path/to/ca-bundle.crt

    <Directory /var/www/thelivingstonefoundation.com/blog-subdomain>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/blog-ssl-error.log
    CustomLog ${APACHE_LOG_DIR}/blog-ssl-access.log combined
</VirtualHost>
```

**Enable the site:**
```bash
sudo a2ensite blog.thelivingstonefoundation.com.conf
sudo systemctl restart apache2
```

#### Step 4: Nginx Configuration

Create/edit server block:

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name blog.thelivingstonefoundation.com www.blog.thelivingstonefoundation.com;

    # Redirect to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name blog.thelivingstonefoundation.com www.blog.thelivingstonefoundation.com;

    root /var/www/thelivingstonefoundation.com/blog-subdomain;
    index index.html;

    # SSL Configuration
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    # Gzip compression
    gzip on;
    gzip_types text/css application/javascript image/svg+xml;
    gzip_comp_level 6;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache static assets
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Logging
    access_log /var/log/nginx/blog-access.log;
    error_log /var/log/nginx/blog-error.log;
}
```

**Enable and reload:**
```bash
sudo ln -s /etc/nginx/sites-available/blog.thelivingstonefoundation.com /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

#### Step 5: SSL Certificate (Let's Encrypt)

```bash
# Install Certbot
sudo apt update
sudo apt install certbot python3-certbot-apache  # For Apache
# OR
sudo apt install certbot python3-certbot-nginx   # For Nginx

# Obtain certificate
sudo certbot --apache -d blog.thelivingstonefoundation.com -d www.blog.thelivingstonefoundation.com
# OR
sudo certbot --nginx -d blog.thelivingstonefoundation.com -d www.blog.thelivingstonefoundation.com

# Auto-renewal (should be automatic, but verify)
sudo certbot renew --dry-run
```

---

### Method 2: Subdirectory Configuration (Alternative: /blog/)

If you prefer `thelivingstonefoundation.com/blog/` instead of a subdomain:

1. **Upload files to:**
   ```
   /var/www/thelivingstonefoundation.com/blog/
   ```

2. **Update all links in files:**
   ```bash
   cd /var/www/thelivingstonefoundation.com/blog/

   # Update navigation links
   find . -name "*.html" -exec sed -i 's|href="/"|href="/blog/"|g' {} +
   find . -name "*.html" -exec sed -i 's|href="https://thelivingstonefoundation.com|href="..|g' {} +
   ```

3. **No DNS changes needed** - works immediately

---

## 🔧 Post-Deployment Configuration

### 1. File Permissions
```bash
cd /var/www/thelivingstonefoundation.com/blog-subdomain/
sudo chown -R www-data:www-data .
sudo find . -type d -exec chmod 755 {} \;
sudo find . -type f -exec chmod 644 {} \;
```

### 2. Test the Deployment

**Check DNS:**
```bash
nslookup blog.thelivingstonefoundation.com
dig blog.thelivingstonefoundation.com
```

**Test in browser:**
- http://blog.thelivingstonefoundation.com
- https://blog.thelivingstonefoundation.com
- https://blog.thelivingstonefoundation.com/post/ai-agents-enterprise-future.html

### 3. Verify Features

- [ ] Homepage loads correctly
- [ ] Category filtering works
- [ ] Search functionality works
- [ ] Individual blog posts load
- [ ] All links work (main site ↔ blog)
- [ ] Images load from main site
- [ ] SSL certificate is valid
- [ ] Mobile responsive layout works

---

## 📊 Analytics & Monitoring

### Google Analytics Setup

Add to `<head>` section of index.html and all blog post templates:

```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

### Google Search Console

1. Add `blog.thelivingstonefoundation.com` as a new property
2. Verify ownership via HTML file or DNS
3. Submit sitemap: `https://blog.thelivingstonefoundation.com/sitemap.xml`

---

## 🔍 SEO Optimization

### Create Sitemap

Create `sitemap.xml` in root:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://blog.thelivingstonefoundation.com/</loc>
    <changefreq>daily</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://blog.thelivingstonefoundation.com/post/ai-agents-enterprise-future.html</loc>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
  <!-- Add all blog posts -->
</urlset>
```

### Create robots.txt

```
User-agent: *
Allow: /
Sitemap: https://blog.thelivingstonefoundation.com/sitemap.xml
```

---

## 🔄 Updating Blog Content

### Adding New Posts

1. **Create HTML file** in `/post/` directory using existing template
2. **Update index.html** - add card to articles grid
3. **Update sitemap.xml** - add new URL
4. **Upload to server** via FTP/Git
5. **Clear CDN cache** if using one

### Quick Content Update Script

```bash
#!/bin/bash
# update-blog.sh

cd /var/www/thelivingstonefoundation.com/blog-subdomain/
git pull origin main
sudo chown -R www-data:www-data .
echo "Blog updated successfully!"
```

---

## 🐛 Troubleshooting

### Issue: 404 Not Found
- Check DNS propagation: `dig blog.thelivingstonefoundation.com`
- Verify DocumentRoot path in Apache/Nginx config
- Check file permissions: `ls -la /var/www/thelivingstonefoundation.com/blog-subdomain/`

### Issue: CSS/Images Not Loading
- Check paths in HTML files (should use `https://thelivingstonefoundation.com/...`)
- Verify CORS headers if needed
- Check browser console for errors

### Issue: SSL Certificate Errors
- Verify certificate includes both `blog.thelivingstonefoundation.com` and `www.blog.thelivingstonefoundation.com`
- Check certificate expiration: `openssl s_client -connect blog.thelivingstonefoundation.com:443`
- Renew with: `sudo certbot renew`

### Issue: Search/Filtering Not Working
- Check JavaScript console for errors
- Verify script.js loads from main site
- Test JavaScript execution: `console.log('test')`

---

## 📞 Support

**Technical Issues:**
- Email: dev@thelivingstonefoundation.com
- Documentation: See README.md

**Content Updates:**
- Email: content@thelivingstonefoundation.com

---

## ✅ Go-Live Checklist

Final checks before announcing the blog:

- [ ] DNS resolving correctly
- [ ] SSL certificate valid
- [ ] All pages load without errors
- [ ] Navigation works both ways (main site ↔ blog)
- [ ] Search functionality tested
- [ ] Category filters tested
- [ ] Mobile responsive on all devices
- [ ] Analytics tracking verified
- [ ] Newsletter signup tested
- [ ] Contact forms work
- [ ] Page load speed optimized (<3s)
- [ ] SEO meta tags complete
- [ ] Sitemap submitted to Google
- [ ] 404 page configured
- [ ] Security headers configured
- [ ] Backup system in place

---

## 🎉 You're Done!

Your blog is now live at: **https://blog.thelivingstonefoundation.com**

Promote it by:
- Adding link to main website navigation
- Social media announcement
- Email newsletter
- Press release
- Update business cards/materials

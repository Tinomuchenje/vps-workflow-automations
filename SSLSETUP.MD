# SSL Setup Documentation for dexruptivetech.com

## Prerequisites

- **Domain Name**: Ensure your domain (e.g., `dexruptivetech.com` and its subdomains) is registered (e.g., with Namecheap).
- **VPS**: A running VPS (e.g., Contabo) with Nginx installed.
- **Certbot**: Installed to obtain SSL certificates.

## Step-by-Step SSL Setup

### 1. Install Certbot

If you haven't installed Certbot, do so using the following commands:

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx
```

### 2. Configure Nginx

Ensure Nginx is set up to handle HTTP requests and to serve the `.well-known` directory for ACME challenges.

#### Edit Nginx Configuration

Open your default configuration file:

```bash
sudo nano /etc/nginx/sites-available/default
```

#### Sample Default Nginx Configuration

Here’s how your `default` configuration might look, referencing the main domain and `www.` while redirecting to HTTPS:

```nginx
server {
    listen 80;
    server_name dexruptivetech.com www.dexruptivetech.com;

    location /.well-known/acme-challenge/ {
        root /var/www/html;  # Ensure this directory exists for Certbot challenges
    }

    # Redirect HTTP to HTTPS
    return 301 https://$host$request_uri;
}
```

#### Separate Subdomain Configuration Files

For each subdomain, create a separate configuration file in `/etc/nginx/sites-available/`. Here’s an example for `docextractionapi.dexruptivetech.com`:

1. **Create a Subdomain Configuration File**:

   ```bash
   sudo nano /etc/nginx/sites-available/docextractionapi.dexruptivetech.com
   ```

2. **Sample Subdomain Configuration**:

   ```nginx
   server {
       listen 443 ssl;
       server_name docextractionapi.dexruptivetech.com;

       ssl_certificate /etc/letsencrypt/live/dexruptivetech.com/fullchain.pem;  # Adjust path as necessary
       ssl_certificate_key /etc/letsencrypt/live/dexruptivetech.com/privkey.pem;  # Adjust path as necessary

       location / {
           # Your application configuration here
       }
   }
   ```

3. **Repeat for Other Subdomains**:
   Create similar configuration files for other subdomains (e.g., `doclens.dexruptivetech.com`).

#### Enable Subdomain Configurations

Make sure to create symbolic links in the `sites-enabled` directory for each subdomain:

```bash
sudo ln -s /etc/nginx/sites-available/docextractionapi.dexruptivetech.com /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/doclens.dexruptivetech.com /etc/nginx/sites-enabled/
```

### 3. Test Nginx Configuration

Before applying the changes, test the Nginx configuration:

```bash
sudo nginx -t
```

### 4. Restart Nginx

Restart Nginx to apply the changes:

```bash
sudo systemctl restart nginx
```

### 5. Obtain SSL Certificates

Run Certbot to automatically obtain and configure SSL certificates:

```bash
sudo certbot --nginx -d dexruptivetech.com -d www.dexruptivetech.com -d docextractionapi.dexruptivetech.com -d doclens.dexruptivetech.com
```

### 6. Set Up Auto-Renewal

Certbot automatically sets up a cron job for renewing certificates, but you can check with:

```bash
sudo certbot renew --dry-run
```

### 7. Verify SSL Configuration

After installation, verify the SSL setup using:

- [SSL Labs](https://www.ssllabs.com/ssltest/) to check your SSL certificate status.

## Notes

- Ensure that port 80 (HTTP) and port 443 (HTTPS) are open in your VPS firewall settings.
- Regularly check the status of your SSL certificates and renew as necessary.

## Troubleshooting

- If Certbot fails, check the Nginx error logs for detailed errors:
  ```bash
  tail -f /var/log/nginx/error.log
  ```

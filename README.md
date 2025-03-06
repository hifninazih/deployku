### **Deploy Next.js App with PM2 and SSL (Certbot) on Linux Server**

This guide walks you through deploying a **Next.js** app with **PM2** and securing it with **Certbot SSL** on a Linux server.

---

## **1. Upload Your Next.js Project to the Server**

If your Next.js project is on your local machine, transfer it to the server using **SSH/SCP** or **Git**:

```sh
scp -r your-nextjs-app/ user@your-server-ip:/home/user/
```

Or clone it directly on the server:

```sh
git clone https://github.com/yourusername/your-nextjs-app.git
cd your-nextjs-app
```

---

## **2. Install Node.js and PM2**

Ensure **Node.js** and **PM2** are installed. If not, install them:

```sh
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
```

Install **PM2** globally:

```sh
sudo npm install -g pm2
```

---

## **3. Install Dependencies and Build Next.js App**

Go into your project directory and install dependencies:

```sh
cd your-nextjs-app
npm install
```

Build your Next.js app:

```sh
npm run build
```

---

## **4. Start the Next.js App with PM2**

Run the Next.js app using PM2:

```sh
pm2 start npm --name "nextjs-app" -- start
```

To ensure it restarts on server reboot, run:

```sh
pm2 save
pm2 startup
```

Follow the instructions given to enable auto-start on reboot.

---

## **5. Install and Configure Nginx**

Install Nginx:

```sh
sudo apt install nginx -y
```

Create an Nginx config file:

```sh
sudo nano /etc/nginx/sites-available/yourdomain.com
```

Paste the following configuration (replace `yourdomain.com`):

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Save and exit (`CTRL + X`, then `Y`, then `Enter`).

Enable the configuration:

```sh
sudo ln -s /etc/nginx/sites-available/yourdomain.com /etc/nginx/sites-enabled/
```

Test Nginx:

```sh
sudo nginx -t
```

If the test is successful, restart Nginx:

```sh
sudo systemctl restart nginx
```

---

## **6. Secure with SSL using Certbot**

Install Certbot and Nginx plugin:

```sh
sudo apt install certbot python3-certbot-nginx -y
```

Run Certbot to obtain SSL certificates:

```sh
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

When prompted, select **option 2** to **redirect HTTP to HTTPS**.

Verify SSL is working by visiting:

```
https://yourdomain.com
```

---

## **7. Auto-Renew SSL**

Check if auto-renewal is set up:

```sh
sudo certbot renew --dry-run
```

Certbot should automatically renew SSL certificates. If necessary, add a cron job:

```sh
sudo crontab -e
```

Add this line at the end:

```
0 0 * * * certbot renew --quiet && systemctl restart nginx
```

This will renew the SSL certificate every day at midnight.

---

## **8. Monitor and Manage PM2**

View running processes:

```sh
pm2 list
```

Restart the app:

```sh
pm2 restart nextjs-app
```

Stop the app:

```sh
pm2 stop nextjs-app
```

Delete the app:

```sh
pm2 delete nextjs-app
```

---

### ✅ **Deployment Completed!**

Your **Next.js app** is now deployed with **PM2** and secured with **SSL (Certbot)** on your Linux server. 🚀

Ya, **subdomain** bisa digunakan untuk **deploy Next.js** dengan **Nginx + PM2 + Certbot SSL**.

Misalnya, jika domain utama adalah **`hifninazih.my.id`**, Anda bisa menggunakan subdomain seperti:

- `propix.hifninazih.my.id`
- `app.hifninazih.my.id`
- `dashboard.hifninazih.my.id`

---

## ✅ **Cara Deploy Next.js dengan Subdomain di Linux Server**

Berikut langkah-langkah lengkapnya:

### **1️⃣ Pastikan Next.js Sudah Dideploy dengan PM2**

Masuk ke direktori proyek:

```sh
cd /path/to/your-nextjs-app
```

Build aplikasi Next.js:

```sh
npm run build
```

Jalankan dengan PM2:

```sh
pm2 start npm --name "nextjs-app" -- start
```

Pastikan PM2 berjalan:

```sh
pm2 list
```

---

### **2️⃣ Konfigurasi Nginx untuk Subdomain**

Buka file konfigurasi Nginx:

```sh
sudo nano /etc/nginx/sites-available/propix.hifninazih.my.id
```

Tambahkan konfigurasi berikut:

```nginx
server {
    listen 80;
    server_name propix.hifninazih.my.id;

    location / {
        proxy_pass http://localhost:3000; # Sesuaikan port jika Next.js berjalan di port lain
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

**Simpan** dengan `CTRL + X`, lalu tekan `Y`, dan `Enter`.

---

### **3️⃣ Aktifkan Konfigurasi Subdomain**

Buat symbolic link ke `sites-enabled`:

```sh
sudo ln -s /etc/nginx/sites-available/propix.hifninazih.my.id /etc/nginx/sites-enabled/
```

Cek apakah konfigurasi Nginx benar:

```sh
sudo nginx -t
```

Jika hasilnya **"test is successful"**, restart Nginx:

```sh
sudo systemctl restart nginx
```

---

### **4️⃣ (Opsional) Gunakan SSL dengan Certbot**

Pastikan **domain/subdomain sudah diarahkan ke server (DNS A Record)**.  
Kemudian jalankan:

```sh
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d propix.hifninazih.my.id
```

Ikuti instruksi, lalu restart Nginx:

```sh
sudo systemctl restart nginx
```

---

### **5️⃣ Cek Status dan Akses Website**

- **Cek apakah PM2 berjalan**:
  ```sh
  pm2 list
  ```
- **Cek apakah Nginx berjalan**:
  ```sh
  sudo systemctl status nginx
  ```
- **Akses website** dengan membuka `https://propix.hifninazih.my.id` di browser.

---

### **🎯 Kesimpulan**

✅ **Bisa menggunakan subdomain** untuk deploy Next.js dengan **Nginx, PM2, dan Certbot**.  
✅ Pastikan **subdomain sudah diarahkan ke IP server** sebelum menggunakan SSL.

Jika masih ada error, beri tahu saya! 🚀

Jika subdomain **tidak diarahkan ke SSL** setelah menggunakan **Certbot**, coba langkah berikut:

---

### **1️⃣ Cek Status SSL Certbot**

Jalankan:

```sh
sudo certbot certificates
```

Pastikan domain/subdomain Anda (`propix.hifninazih.my.id`) ada dalam daftar. Jika tidak, ulangi perintah ini:

```sh
sudo certbot --nginx -d propix.hifninazih.my.id
```

Kemudian, restart Nginx:

```sh
sudo systemctl restart nginx
```

---

### **2️⃣ Periksa Konfigurasi Nginx (HTTPS Redirect)**

Buka file konfigurasi Nginx untuk subdomain:

```sh
sudo nano /etc/nginx/sites-available/propix.hifninazih.my.id
```

Pastikan ada **dua blok server**:

1. **Redirect HTTP ke HTTPS**
2. **Konfigurasi HTTPS dengan SSL**

```nginx
server {
    listen 80;
    server_name propix.hifninazih.my.id;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name propix.hifninazih.my.id;

    ssl_certificate /etc/letsencrypt/live/propix.hifninazih.my.id/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/propix.hifninazih.my.id/privkey.pem;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

**Simpan dan keluar** (`CTRL + X`, lalu tekan `Y` dan `Enter`).

---

### **3️⃣ Pastikan Nginx Dapat Mengakses Sertifikat**

Cek apakah file sertifikat tersedia:

```sh
ls -l /etc/letsencrypt/live/propix.hifninazih.my.id/
```

Jika tidak ada file, **hapus konfigurasi lama dan buat ulang sertifikat**:

```sh
sudo certbot delete --cert-name propix.hifninazih.my.id
sudo certbot --nginx -d propix.hifninazih.my.id
```

---

### **4️⃣ Restart Nginx dan Cek Status**

Jalankan:

```sh
sudo nginx -t
sudo systemctl restart nginx
```

Cek apakah Nginx berjalan dengan benar:

```sh
sudo systemctl status nginx
```

---

### **5️⃣ Coba Akses Website**

- **Coba akses HTTPS**:  
  👉 **https://propix.hifninazih.my.id**
- **Cek Redirect HTTP → HTTPS**:
  ```sh
  curl -I http://propix.hifninazih.my.id
  ```
  Harus menampilkan:
  ```
  HTTP/1.1 301 Moved Permanently
  Location: https://propix.hifninazih.my.id/
  ```

Jika masih tidak berfungsi, beritahu saya **error spesifiknya**. 🚀

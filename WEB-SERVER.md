# WEB SERVER

Panduan ini menjelaskan cara menginstal, mengonfigurasi, dan mengamankan **Web Server Apache2** dengan berbagai fitur seperti **Basic Authentication**, **Virtual Host**, **Keamanan Apache2**, dan **Redirection**.

---

## Instalasi Web Server (Apache2)
```
sudo apt update
sudo apt install apache2 apache2-utils -y
sudo ufw allow 'Apache Full'
```
Pastikan service apache2 berjalan
```
sudo systemctl status apache2
# Jika statusnya inactive (dead) maka jalankan perintah dibawah ini.
sudo systemctl enable --now apache2
```
> Letak file konfigurasi apache2 ada di direktori `/etc/apache2`. Letak file web ada di direktori `/var/www/html`.

## Basic Authentication
Buat file `.htaccess` di direktori file web.
```
sudo nano /var/www/html/.htaccess
```
```
AuthName "Private"
AuthType Basic
AuthBasicProvider file
AuthUserFile "/etc/apache2/.users"
Require valid-user
```
Setelah itu edit file `apache2.conf` di direktori file konfigurasi.
```
sudo nano /etc/apache2/apache2.conf
```
Di line sekitar 170, cari konfigurasi seperti dibawah ini, ubah `AllowOverride` yang awalnya `None` menjadi `All`.
```
<Directory /var/www/>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
</Directory>
```
Setelah itu buat user authenticationnya menggunakan command `htpasswd`.
```
sudo htpasswd -c /etc/apache2/.users
```
Jika sudah restart service apache2.
```
sudo systemctl restart apache2
```

## Virtual Host, Secure Apache2, Redirect
VirtualHost adalah sebuah konsep dalam dunia web hosting yang memungkinkan satu server fisik untuk melayani lebih dari satu situs web (domain) menggunakan alamat IP yang sama atau berbeda.
Buat self sign certificate SSL.
```
openssl genrsa -out server.key 2048
openssl rsa -in server.key -out server.key
openssl req -new -days 3650 -key server.key -out server.csr
```
```
Country Name (2 letter code) [AU]:ID
State or Province Name (full name) [Some-State]:DKI JAKARTA
Locality Name (eg, city) []:JAKARTA TIMUR
Organization Name (eg, company) [Internet Widgits Pty Ltd]:SMK DP 1 JAKARTA
Organizational Unit Name (eg, section) []:TKJ
Common Name (e.g. server FQDN or YOUR name) []:peserta-xx.id
Email Address []:admin@peserta-xx.id
```
```
openssl x509 -in server.csr -out server.crt -req -signkey server.key -days 3650
```
Download script game flappy-bird.
```
git clone https://github.com/achmadalifn4/flappy-bird
sudo mv flappy-bird /var/www/
```
Buat file `/etc/apache2/sites-available/game.conf`.
```
sudo nano /etc/apache2/sites-available/game.conf
```
```
<VirtualHost *:80>
        ServerName game.peserta-XX.id
        Redirect / https://game.peserta-xx.id
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/flappy-bird
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:443>
        ServerName game.peserta-xx.id
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/flappy-bird
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        SSLEngine on
        SSLCertificateFile      /etc/ssl/server.crt
        SSLCertificateKeyFile   /etc/ssl/server.key
</VirtualHost>
```
Setelah itu aktifkan module ssl dan restart service apache2.
```
sudo a2enmod ssl
sudo systemctl restart apache2
```

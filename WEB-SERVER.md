# WEB SERVER

Panduan ini menjelaskan cara menginstal, mengonfigurasi, dan mengamankan **Web Server Apache2** dengan berbagai fitur seperti **Basic Authentication**, **Virtual Host**, **Keamanan Apache2**, dan **Redirection**.

---

## Instalasi Web Server (Apache2)
```
sudo apt update
sudo apt install apache2 apache2-utils -y
```
Pastikan service apache2 berjalan
```
sudo systemctl status apache2
# Jika statusnya inactive (dead) maka jalankan perintah dibawah ini.
sudo systemctl enable --now apache2
```
> Letak file konfigurasi apache2 ada di direktori "/etc/apache2". Letak file web ada di direktori "/var/www/html".

## Basic Authentication
```
sudo nano /etc/apache2/sites-available/000-default.conf
```


```
<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html
    
    <Directory "/var/www/html">
        <span style="color:red;">AuthName "Private"</span>
        <span style="color:red;">AuthType Basic</span>
        <span style="color:red;">AuthBasicProvider file</span>
        <span style="color:red;">AuthUserFile "/etc/apache2/.users"</span>
        <span style="color:red;">Require valid-user</span>
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

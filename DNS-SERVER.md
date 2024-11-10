# DNS SERVER

Panduan ini menjelaskan cara menginstal dan mengonfigurasi **DNS Server Bind9** dengan berbagai fitur seperti **Recursive**, **Forwarders**, **Authoritative**, dan **Reverse**.

---

## Instalasi DNS Server (Bind9)
```
sudo apt update
sudo apt install bind9 bind9utils dnsutils -y
sudo ufw allow 'Bind9'
```
Pastikan service bind9 berjalan
```
sudo systemctl status bind9
# Jika statusnya inactive (dead) maka jalankan perintah dibawah ini.
sudo systemctl enable --now bind9
```
> Letak file konfigurasi bind9 ada di direktori `/etc/bind`.

## Recursive & Forwarders
Edit file `named.conf.options` di direktori konfigurasi bind9.
```
sudo nano /etc/bind/named.conf.options
```
```
options {
	directory "/var/cache/bind";
	forwarders {
		8.8.8.8;
	};
	dnssec-validation yes;
	listen-on-v6 { any; };
};
```

## Authoritative & Reverse
Masuk ke direktori konfigurasi bind9 dan edit file `named.conf.local`.
```
cd /etc/bind
sudo nano named.conf.local
```
```
zone "peserta-xx.id" {
        type master;
        file "/etc/bind/db.peserta-xx.id";
};

zone "13.16.172.in-addr.arpa" {
        type master;
        file "/etc/bind/db.reverse";
};
```
Setelah itu salin file `db.local` dan `db.127`.
```
sudo cp db.local db.peserta-xx.id
sudo cp db.127 db.reverse
```
Edit file `db.peserta-xx.id`.
```
sudo nano db.peserta-xx.id
```
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     peserta-xx.id. root.peserta-xx.id. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      peserta-xx.id.
@       IN      A       172.16.13.43
game    IN      A       172.16.13.43
```
Edit file `db.reverse`.
```
sudo nano db.reverse
```
```
;
; BIND reverse data file for local loopback interface
;
$TTL    604800
@       IN      SOA     peserta-xx.id. root.peserta-xx.id. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      peserta-xx.id.
43      IN      PTR     peserta-xx.id.
```
Setelah itu restart service bind9.
```
sudo systemctl restart bind9
```


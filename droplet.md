# SSH Kurulumu

Linux User:

```sh
$ adduser --gecos "" ubuntu (gecos parametresi kullanıcı hakkında bilgi tutmak için)
$ usermod -aG sudo ubuntu (ubuntu kullanıcısını sudo grubuna dahil eder.)
$ su ubuntu (ubuntu kullanıcısına geçer.)
```

## Linux

Droplete bağlanacak pc'nin SSH keyleri yoksa oluşturulur.

```sh
$ ls ~/.ssh
$ ssh-keygen (enter ile ilerlenir)
```

Oluşan Public Key Dropletin authorized_keys dosyasına eklenir.

```sh
$ echo <puclic anahtar yapıştırılır> >> ~/.ssh/authorized_keys (key kopyalama 1.yol)
$ ssh-copy-id ubuntu@167.71.34.43 (key kopyalama 2.yol)
$ chmod 600 ~/.ssh/authorized_keys
```

Sunucuya SSH ile bağlantı:

```sh
$ ssh ubuntu@167.71.34.43 (kullanıcı@ip)

```

## PuTTY

Droplete bağlanacak pc'nin SSH keyleri yoksa oluşturulur.

- PuTTy Key Generator ile keyler oluşturulur ve Private key Save'lenir.
- Oluşan Public Key Dropletin authorized_keys dosyasına eklenir.
- Category->Connection->SSH->Auth Ekranında: Private key file for authentication: Save'lenen Private Key'in yolu girilir.
- Category->Session Ekranında Host Name (or IP address): kullanıcı@ip adres girilip Open Tuşuna Basılır.

## FileZilla İle SSH

- Dosya-> Site Yöneticisi ekranında Yeni site tuşuna basılır.
- Genel sekmesinde İletişim Kuralı: SFTP SSH ile File Transfer Protokol seçilir.
- Yine Genel sekmesinde Sunucu: 167.71.34.43 (ip) girilir.
- Yine Genel sekmesinde Kapı Numarası: 22 (SSH portu) girilir.
- Yine Genel sekmesinde Anahtar dosyası: Save'lenen Private Key'in yolu girilir.
- Bağlan Tuşuna Basılır.

## Config ayarları

/etc/ssh/sshd_config

- PermitRootLogin no (root login’i kapatma)
- PasswordAuthentication no (şifre ile girişi engelleme.)
- PubkeyAuthentication yes (ön tanımli değer)
- ChallengeResponseAuthentication no (ön tanımli değer)

```sh
$ sudo systemctl reload sshd (konfigurasyon devreye girmesi için)

```

# Nginx Kurulumu

cd .
Kurulum:

```sh
$ sudo apt-get update
$ sudo apt-get install nginx -y
$ curl -4 icanhazip.com (ip adresini döner)

```

/etc/nginx/nginx.conf:
server_names_hash_bucket_size 64;(sever name uzunluğunu ifade eder)

example.com devreye almak.

```sh
$ sudo mkdir -p /var/www/example.com/html (gerekli dizinleri oluşturur)
$ sudo chown -R www-data:www-data /var/www/example.com/html (dizinlerin sahibi Nginx olarak atanır.)
$ sudo chmod -R 755 /var/www (Kullanıcılara yetki verilir)
```

/var/www/example.com/html/index.html

```sh
<html>
    <head>
        <title>Welcome to Example.com!</title>
    </head>
    <body>
        <h1>Success!  The example.com server block is working!</h1>
    </body>
</html>
```

```sh
$ grep -R default_server /etc/nginx/sites-enabled/ (bir tane default sever olması lazım)
```

konfigurasyon ayarları defult'tan kopyalanır.

```sh
$ sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/example.com
```

/etc/nginx/sites-available/example.com: Aşağıdaki hale getirilir.

```sh
server {
        listen 80;
        listen [::]:80;

        root /var/www/example.com/html;
        index index.html;

        server_name example.com www.example.com;

        location / {
                try_files $uri $uri/ =404;
        }
}
```

Nginx' e konfigurasyon test ettirilir.

```sh
$ sudo nginx -t
```

Site yayındakilere kayıt edilir.

```sh
$ sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
```

Servis reload edilir.

```sh
$ sudo systemctl reload nginx
```

Firewall Ayarları

```sh
$ sudo apt-get install -y ufw
$ sudo ufw allow ssh
$ sudo ufw allow http
$ sudo ufw allow 443/tcp
$ sudo ufw --force enable
$ sudo ufw status
```

[Server Block](https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms)

# Postgresql Kurulumu

[Ubuntu_18.04_Postgresql_12](https://www.postgresql.org/download/linux/ubuntu/)

Dosyasını oluştur
/etc/apt/sources.list.d/pgdg.list

Dosyasına satırı ekle:
deb http://apt.postgresql.org/pub/repos/apt/ bionic-pgdg main

Reposito key'ini indir ve update et.

```sh
$ wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
$ sudo apt-get update
```

İndir

```sh
$ sudo apt-get install postgresql-12

```

Türkeçe Postgresql İçin

```sh
postgres=# CREATE DATABASE test_db ENCODING='UTF-8' LC_COLLATE='tr_TR.UTF-8' TEMPLATE=template0;
postgres=# SELECT * FROM pg_collation;
```

Mevcut paketleri listeliyoruz.

```sh
$ locale -a

```

Yukarıdaki listede tr_TR veya tr_TR.UTF-8 yoksa ekliyoruz.

```sh
$ sudo locale-gen tr_TR
$ sudo locale-gen tr_TR.UTF-8
$ sudo update-locale
$ sudo systemctl restart postgresql
```

```sh
postgres=# create collation "tr_TR.UTF-8" (LOCALE="tr_TR.UTF-8");

```

# .Net Core Kurulumu

wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb

sudo add-apt-repository universe
sudo apt-get update
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install aspnetcore-runtime-3.1

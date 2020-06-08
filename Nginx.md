# Kurulum:

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

[Server Block](https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms)

default server

```sh
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

bütün 80 portu https yönlendirir.

```sh
server {
    listen     *:80;
    add_header Strict-Transport-Security max-age=15768000;
    return     301 https://$host$request_uri;
}
```

Microsoft Örnek:

/etc/nginx/proxy.conf configuration standart proxy ayarları

```sh
proxy_redirect          off;
proxy_set_header        Host $host;
proxy_set_header        X-Real-IP $remote_addr;
proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header        X-Forwarded-Proto $scheme;
client_max_body_size    10m;
client_body_buffer_size 128k;
proxy_connect_timeout   90;
proxy_send_timeout      90;
proxy_read_timeout      90;
proxy_buffers           32 4k;
```

```sh
http {
    include        /etc/nginx/proxy.conf;
    limit_req_zone $binary_remote_addr zone=one:10m rate=5r/s;
    server_tokens  off;

    sendfile on;
    keepalive_timeout   29; # Adjust to the lowest possible value that makes sense for your use case.
    client_body_timeout 10; client_header_timeout 10; send_timeout 10;

    upstream helloapp{
        server localhost:5000;
    }

    server {
        listen     *:80;
        add_header Strict-Transport-Security max-age=15768000;
        return     301 https://$host$request_uri;
    }

    server {
        listen                    *:443 ssl;
        server_name               example.com;
        ssl_certificate           /etc/ssl/certs/testCert.crt;
        ssl_certificate_key       /etc/ssl/certs/testCert.key;
        ssl_protocols             TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
        ssl_ciphers               "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
        ssl_ecdh_curve            secp384r1;
        ssl_session_cache         shared:SSL:10m;
        ssl_session_tickets       off;
        ssl_stapling              on; #ensure your cert is capable
        ssl_stapling_verify       on; #ensure your cert is capable

        add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
        add_header X-Frame-Options DENY;
        add_header X-Content-Type-Options nosniff;

        #Redirects all traffic
        location / {
            proxy_pass http://helloapp;
            limit_req  zone=one burst=10 nodelay;
        }
    }
}
```

//performans için log
access_log off;

[performan linki](https://gist.github.com/denji/8359866)

[How To Secure Nginx with Let's Encrypt on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04)

[cerboot](https://certbot.eff.org/lets-encrypt/ubuntuxenial-nginx)

Konfigurasyon kontrol edilir.

```sh
$ sudo nginx -t
```

Hata varsa düzeltilir. Ve Nginx'e ayarlar yüklenir.

```sh
$ sudo systemctl reload nginx
```

```sh
$ sudo apt install certbot python3-certbot-nginx
```

```sh
$ sudo certbot --nginx -d example.com -d www.example.com
```

ekran çıktısı

```sh
Output
Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel):
```

certbot yenileme servisi kontrol ediliyor.

```sh
$ sudo systemctl status certbot.timer
```

certbot sertifika yenileme.

```sh
$ sudo certbot renew --dry-run
```

[SSL TEST](https://www.ssllabs.com/ssltest/)

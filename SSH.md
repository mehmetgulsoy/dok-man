```sh
$  ssh-keygen -f ~/.ssh/ubuntu-rsa -t rsa
```

Burada kullandığımız "-f" parametresi dosya çıktısının bulunduğu yer ve ismini, "-t" parametresi algoritma tipini(dsa|ecdsa|ed25519|rsa) belirlediğimiz parametrelerdir.

Sunucumuza anahtarımızı eklemek

```sh
$ ssh-copy-id -i ubuntu-rsa.pub ubuntu@ip
```

Gizli anahtar yetkisi:

```sh
$ chmod 600 ~/.ssh/ubuntu-rsa
```

Açık anahtar yetkisi:

```sh
$ chmod 644 ~/.ssh/ubuntu-rsa.pub
```

Sunucumuzdaki "~/.ssh/authorized_keys" dosyasının yetkisi:

```sh
$ chmod 600 ~/.ssh/authorized_keys
```

Eğer anahtarımız "~/.ssh" dizini içindeyse sunucuya bağlanmak için:

```sh
$ ssh user@ip
```

Eğer gizli anahtar "~/.ssh" dizininde değil ise bağlantıyı kurmak için gizli anahtarın bulunduğu dizini "-i" parametresi ("-i" parametresi "identity_file") ile göstermeliyiz.

```sh
$ ssh user@ip -i  ubuntu-rsa
```

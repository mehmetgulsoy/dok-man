# Postgresql Ubuntu Kurulumu

[Ubuntu_18.04_Postgresql_12](https://www.postgresql.org/download/linux/ubuntu/)
[Kurulum Türkçe](https://www.sinaneldem.com.tr/macos-centos-ve-ubuntu-uzerinde-postgresql-kurulumu-ve-turkce-karakter-hatasinin-giderilmesi)

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

## Türkçe karakter ayarları

Türkeçe Postgresql İçin

```sh
postgres=# CREATE DATABASE test_db ENCODING='UTF-8' LC_COLLATE='tr_TR.UTF-8' TEMPLATE=template0;
```

Çıktı hata verirse

```sh
$ ERROR:  invalid locale name: "tr_TR.UTF-8"
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

Türkçe karakterlerimizin testini yapalım:

```sh
postgres=# SELECT
    harf, upper(harf)
FROM (
VALUES ('ş'), ('t'), ('u'), ('a'), ('n'), ('o'), ('ö'), ('p'), ('f'), ('g'), ('ğ'), ('h'), ('ı'), ('i'), ('j'), ('k'), ('l'), ('m'), ('ü'), ('v'), ('r'), ('s'), ('b'), ('c'), ('ç'), ('d'), ('e'), ('y'), ('z')) AS
alfabe(harf)
ORDER BY 1;

```

# En çok ihtiyaç duyacağınız PostgreSQL komutları şunlardır:

```sh
\q                      : Çıkış
\c __veritabanı__       : veritabanı isimli veritabanına bağlantı yapar
\d __table__            : table isimli tablo hakkında açıklama (triggerları içerir)
\d+ __table__           : table isimli tablo hakkında daha fazla açıklamal (disk boyutu kullanımı)
\l                      : Veritabanlarını listeler
\dy                     : Olayları listeler
\df                     : Fonksiyonları listeler
\di                     : Indexleri listeler
\dn                     : Şemaları listeler
\dt *.*                 : Tüm şemaların tüm tablolarını listeler
\dT+                    : Tüm veri tipleri listeler
\dv                     : Görünümleri listeler
\df+ __function__       : Fonksiyonun SQL kodunu gösterir.
\x                      : Sorgu sonuçlarını, çirkin ASCII tablolar yerine güzelleştirerek döker
\du                     : Kullanıcıları listeler
\du __kullanıcı__       : Eğer mevcutsa kullanıcıyı listeler

```

GRANT ALL PRIVILEGES ON DATABASE "database_name" to my_username;

böyle daha güvenli
\password user

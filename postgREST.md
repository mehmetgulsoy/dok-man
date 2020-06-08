# Kurulum

```sh
$ wget https://github.com/PostgREST/postgrest/releases/download/v7.0.1/postgrest-v7.0.1-linux-x64-static.tar.xz
$ tar xfJ postgrest-<version>-<platform>.tar.xz
# Try running it
$ ./postgrest

```

/etc/systemd/system/kermes_rest.service

```sh
[Unit]
Description=REST API for Kermes
After=postgresql.service

[Service]
ExecStart=/bin/postgrest /etc/postgrest/kermes.conf
ExecReload=/bin/kill -SIGUSR1 $MAINPID

[Install]
WantedBy=multi-user.target
```

systemctl enable kermes_rest
systemctl start kermes_grest

db-uri = "postgres://<your_user>:<your_password>@localhost:5432/<your_db>"
db-schema = "<your_exposed_schema>"
db-anon-role = "<your_anon_role>"
db-pool = 10

server-host = "127.0.0.1"
server-port = 3000

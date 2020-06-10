# Kurulum

```sh
$ wget https://github.com/PostgREST/postgrest/releases/download/v7.0.1/postgrest-v7.0.1-linux-x64-static.tar.xz
$ tar xfJ postgrest-<version>-<platform>.tar.xz
# Try running it
$ ./postgrest

```

## Deamon olarak ayarlama

/etc/postgrest/kermes.conf

```sh
db-uri = "postgres://<your_user>:<your_password>@localhost:5432/<your_db>"
db-schema = "<your_exposed_schema>"
db-anon-role = "<your_anon_role>"
db-pool = 10
server-host = "127.0.0.1"
server-port = 3000
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

```sh
$ sudo systemctl enable kermes_rest
$ sudo systemctl start kermes_grest
```

## pgcrypto extension kurulumu

```sh
postgres=# CREATE EXTENSION pgcrypto schema api;
```

## pgjwt extension kurulumu

```sh
$ sudo apt install make
$ git clone https://github.com/michelp/pgjwt.git
$ sudo make install
postgres=# CREATE EXTENSION pgjwt schema api;
```

## jwt için kullanıcıların ayarlanması

```sh
postgres=# create role webuser NOLOGIN;
postgres=# create role anon noinherit;
postgres=# create role authenticator noinherit;
postgres=# grant anon    to authenticator;
postgres=# grant webuser to authenticator;
postgres=# grant execute on function login(text,text) to anon;
```

## Veri tabanında değişken saklama

```sh
-- run this once
ALTER DATABASE mydb SET "app.jwt_secret" TO 'reallyreallyreallyreallyverysafe';
-- then all functions can refer to app.jwt_secret
SELECT sign(
  row_to_json(r), current_setting('app.jwt_secret', true)
) AS token
FROM ...
```

## login function

```sh
 -- add type
CREATE TYPE basic_auth.jwt_token AS (
  token text
);

-- login should be on your exposed schema
create or replace function
login(email text, pass text) returns basic_auth.jwt_token as $$
declare
  _role name;
  result basic_auth.jwt_token;
begin
  -- check email and password
  select basic_auth.user_role(email, pass) into _role;
  if _role is null then
    raise invalid_password using message = 'invalid user or password';
  end if;

  select sign(
      row_to_json(r), current_setting('app.jwt_secret',true)
    ) as token
    from (
      select _role as role, login.email as email,
         extract(epoch from now())::integer + 60*60 as exp
    ) r
    into result;
  return result;
end;
$$ language plpgsql security definer; (sahibinin yetkisi ile çağrılır.)
```

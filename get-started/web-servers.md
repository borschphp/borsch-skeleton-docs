# Web servers

Borsch uses the front-controller pattern to funnel HTTP requests received by your web server to a single PHP file:
`./public/index.php`.

## PHP built-in server

> [!WARNING]
> It is not intended to be a full-featured web server. It should not be used on production.

Run the following command in terminal to start a localhost web server:

```shell
php -S 0.0.0.0:8080 -t ./public/ ./public/index.php
# Or via composer script
composer serve
```

In the meantime, Borsch also provides 2 other method to spin up a local development server:

- [docker-compose](https://docs.docker.com/compose/)
- [lando](https://lando.dev/)

Both provide a development environment with a database and PHPMyAdmin.

#### Docker Compose

```shell
docker-compose up -d
```

#### Lando

```shell
lando start
```
## Apache

Make sure the server root points to `./public/index.php`, then make sure a `.htaccess` file exists in the same folder.  
By default there is one but if you deleted it then you can recreate one with this inside :

```apacheconf
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^ index.php [QSA,L]
```

Make sure to enable Apache's `mod_rewrite` module and your virtual host is configured with the `AllowOverride` option so
that the `.htaccess` rewrite rules can be used:

```apacheconf
AllowOverride All
```

## Nginx

Here is a basic virtual host configuration:

```nginx
server {
    listen 80;

    # If your app use SSL, and it should, comment the line above and configure the one below
    # listen 443 ssl;
    # ssl_certificate /etc/nginx/ssl/certificate.bundle.crt;
    # ssl_certificate_key /etc/nginx/ssl/certificate.key;

    # Replace with your values
    server_name borsch-app.domain.tld;
    access_log /var/log/nginx/borsch_access_log;
    error_log /var/log/nginx/borsch_error_log;

    # Indicate your project path
    root /var/www/html/borsch/public;

    index index.php;

    # To modify according to your needs
    add_header X-Frame-Options "ALLOWALL";
    add_header Access-Control-Allow-Origin "*";
    add_header Access-Control-Allow-Headers "*";
    add_header Access-Control-Allow-Credentials "*";

    location ~ \.php$ {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    location / {
             try_files $uri $uri/ /index.php?$query_string;
    }
}
```

## Other web servers

If you have configuration for other web server then let us know so we can add it here (with a big thank you and a link
to your page).
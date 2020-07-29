# Nginx Configurations

A compilation of best practices of nginx server configurations.

Features: 
- HTTP for local development, HTTPS with/without FastCGI Cache;
- HTTP/2, IPv6;
- Certbot, HSTS, security headers, SSL profiles, OCSP resolvers;
- FastCGI caching, gzip, fallback routing, www/non-www redirect; 
- Comprehensive WordPress support

## Installation

Assuming you have nginx installed, backup your `/etc/nginx` folder

```bash
tar -czvf /etc/nginx_$(date +'%F_%H-%M-%S').tar.gz /etc/nginx
```

Download the new configuration into `/etc/nginx`

```bash
git clone https://github.com/azadcreative/nginx-configs /etc/nginx
```

## Usage

You have three templates in the `templates` folder. `default.conf` is the default_server that returns 444 to all requests.

- `no-ssl.conf` is a simple http with no-www configs. Great for development
- `ssl.conf` is the one you should be using (provided you have an SSL certificate)
- `ssl-fastcgi.conf` is configured with nginx fastcgi cache

Start by copying a template file to `sites-available`

```bash
cp /etc/nginx/templates/ssl.conf /etc/nginx/sites-available/mysite.com.conf
```

Replace `website.com` with your hostname

```bash
sed -i 's/website.com/mysite.com/g' /etc/nginx/sites-available/mysite.com.conf
```

- Open the file and check the path to document root
- Enable the Wordpress server rules if you are using Wordpress. Otherwise discard/ignore.
- If you want site-specific logs, uncomment the `access_log` and `error_log` configs (make sure your log files exist)
- Provide path to your SSL certificates

Create symlinks of the `default` server and your custom server into `sites-enabled`

```bash
ln -s /etc/nginx/templates/default.conf /etc/nginx/sites-enabled/default.conf
ln -s /etc/nginx/sites-available/mysite.com.conf /etc/nginx/sites-enabled/mysite.com.conf
```

Finally, test your nginx configuration before you (re)start your service

```bash
nginx -t
```

## What's Inside

The configurations have the following structure: 

```text
./
├── conf.d/
│   ├── performance/
│   │   ├── cache-file-descriptors.conf
│   │   ├── cache-global.conf
│   │   ├── content-transformation.conf
│   │   ├── gzip.conf
│   ├── security/
│   │   ├── cors.conf
│   │   ├── policies.conf
│   ├── server/
│   │   ├── cache-expires.conf
│   │   ├── exclusions.conf
│   │   ├── fastcgi-cache.conf
│   │   ├── wordpress-cache.conf
│   │   ├── wordpress-security.conf
│   │   ├── wordpress-yoast.conf
│   └── ssl/
│   │   ├── ocsp-stapling.conf
│   │   ├── policy-deprecated.conf
│   │   ├── policy-intermediate.conf
│   │   ├── policy-modern.conf
│   │   ├── ssl-engine.conf

├── sites-available/
├── sites-enabled/
├── templates/
│   ├── default.conf
│   ├── no-ssl.conf
│   ├── ssl.conf
│   ├── ssl-fastcgi.conf
├── mime.types
└── nginx.conf
```

* **`sites-available/` folder** should contain your working server blocks and drafts. Edit your server block files here because some test editors create temp files. 

* **`sites-enabled/` folder** should only contain symlinks to server blocks that are live.

* **`conf.d/` folder**

  Contains all the config snippets and are loaded automatically. Do not change anything inside unless you know what you are doing.

  If you need to change the PHP version, edit `php-fpm.conf`.

  * **`performance/` subfolder** contains files that improve performance of the web server blocks.

  * **`security/` subfolder** headers for cross-origin requests and security policies. Check out [securityheaders.com](https://securityheaders.com) for details.

    If you are using a no-ssl config with a `.test` domain for local development, disable HSTS in `policies.conf`

  * **`server/` subfolder** contains rules that are added to the server blocks. This includes server block level cache headers, file exclusions, fcgi cache rules and plenty of rules for securing Wordpress.

  * **`ssl/` subfolder** contains SSL rules: 

    - `ssl-engine.conf` contains the generic SSL rules

    - You have a choice between three SSL profiles: 
    
      `policy-deprecated.conf` supports TLS v1, v1.1 and 1.2. It is not recommended.

      `policy-intermediate.conf` supports only TLS v1.2

      `policy-modern.conf` supports TLS v1.2 and v1.3. This is the default in the provided templates.

    - `ocsp-stapling.conf` contains resolvers for CloudFlare, Google and OpenDNS.

* **`mime-types.conf` file** is responsible for mapping file extensions to mime types.

* **`nginx.conf` file** is the main configuration file.

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## TODO

- Brotli support
- mod_pagespeed support
- ZStandard support

## Support

Nginx 1.8.0+

## Credits

This project would not have been possible without the following. In fact, much of the code snippets are borrowed from these projects.

- [H5BP server configs](https://github.com/h5bp/server-configs-nginx) boilerplate
- DigitalOcean [nginx config generator](https://do.co/nginxconfig)
- Many others


## License
[MIT](https://choosealicense.com/licenses/mit/)
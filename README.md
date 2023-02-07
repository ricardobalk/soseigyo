# Soseigyo: A nginx set-up focused on simplicity, security and performance.

Soseigyo is a set-up that I use for my own servers. I have been using it for a while and I have been improving it over time. I decided to share it with the community because I think it is a good set-up for a nginx server. It is simple, secure and performant. It is also easy to use and to customize. I hope you find it useful.

It comes with a docker-compose file that you can use to set up a nginx server with a single command: `docker-compose up -d`, after changing the configuration files to your needs.

## Why Soseigyo?

The three main features of Soseigyo are simplicity, security and performance. I will explain each of them in the following sections.

### Simplicity

Soseigyo is simple to set-up, customize and maintain. It uses multiple, small configuration files instead of a single, big configuration file. This makes it easier to to understand. You can use the various configuration snippets or omit them when you don't need them. Need gzip compression? Include `snippets/compression-gzip.conf`. Don't want gzip for a specific sub-domain? Possible. Don't want HTTPS? Well, omit linking `sites-available/...-https.conf` to `sites-enabled/...-https.conf`, etc.

The docker-compose file makes it simple to run it with a single command: `docker-compose up -d`.

### Security

The TLS configuration included in Soseigyo is based on strong, modern ciphers and protocols. It also includes a few security headers, like `Expect-CT`, `Permissions-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options`, `X-Frame-Options`, `X-XSS-Protection` and `Referrer-Policy`, as recommended by Scott Helme's [Security Headers](https://securityheaders.com/) tool.

This set-up also contains a container for certbot, which is used to obtain and renew TLS certificates.

### Performance

It uses HTTP/2 and also includes a few headers like `Cache-Control` and `Expires`. It comes with optimizations, like `gzip` compression, `keepalive` connections and SSL session caching.

## How to use Soseigyo

The usual way to use Soseigyo is to clone this repository and then to customize it to your needs. You can also use it as a template for your own repository. :tada:

1. Customize the configuration files to your needs.
2. Run `docker-compose up -d` to start the server.
3. Enjoy!

## Things to keep in mind

### Obtaining TLS certificates

I have included a container for certbot, which is used to obtain and renew TLS certificates. However, you will need to configure it to your needs. Upon the first start of the containers, it will not request certificates, as it is only configured to renew certificates. You will need to obtain a certificate manually.

Besides that, `config/nginx/000-default-https.conf` is not linked to `config/nginx/sites-enabled/000-default-https.conf`, because otherwise nginx would not start.

So here's what you need to do:

1. Start the containers with `docker-compose up` or `docker-compose up -d`.

2. Obtain a TLS certificate with certbot. You can do this by running `docker-compose exec letsencrypt certbot certonly --dry-run --webroot --elliptic-curve secp384r1 --agree-tos --email john.doe@example.com -w /var/www/example.com -d example.com -d www.example.com`.

Replacing the email address and domain names with your own values, of course.

If that succeeds, you can remove the `--dry-run` flag and run the command again:

`docker-compose exec letsencrypt certbot certonly --quiet --webroot --elliptic-curve secp384r1 --agree-tos --email john.doe@example.com -w /var/www/example.com -d example.com -d www.example.com`.

3. Edit `config/nginx/sites-available/000-default-https.conf` so it contains the correct domain names, e.g. `example.com` and `www.example.com` and it points to the correct certificate and key files.

4. Link `config/nginx/sites-available/000-default-https.conf` to `config/nginx/sites-enabled/000-default-https.conf`, by running `ln -s '../sites-available/000-default-https.conf' config/nginx/sites-enabled/000-default-https.conf`.

5. Edit `config/nginx/sites-available/000-default.conf` so it redirects to HTTPS.

6. Restart the nginx container with `docker-compose restart nginx`.

## How to contribute

If you find a bug or have an idea for a new feature, please open an issue. If you want to contribute code, please open a pull request. Always happy to receive contributions. :smile:
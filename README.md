# Configuring Apache for SSL termination on a load balancer

> Implementing SSL termination on a load balancer allows multiple servers to receive both encrypted and unencrypted traffic.

> For Apache web server nodes, distinguishing between the two requires filtering the X-Forwarded-Proto HTTP header using the RequestHeader directive in the protocol’s respective VirtualHost block:

```<VirtualHost *:80>
    RequestHeader set X-Forwarded-Proto "http"
    …
</VirtualHost>

<VirtualHost *:443>
    RequestHeader set X-Forwarded-Proto "https"
    …
</VirtualHost>
```

> Encrypting all traffic requires a rewrite rule within the HTTP VirtualHost block:
```<VirtualHost *:80>
    RequestHeader set X-Forwarded-Proto "http"

    RewriteEngine On
    RewriteCond %{HTTP:X-Forwarded-Proto} !https
    RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI} [R,L]
    …
</VirtualHost>
```

> The first example filters client request based on the address visited at the load balancer, with the second forwarding all non-HTTPS traffic to the appropriate URI.
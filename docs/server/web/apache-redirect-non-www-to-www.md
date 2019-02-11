# Redirecting non-www to www with .htaccess
To redirect a **non www** site to a **www** site we can do it with **.htaccess**:

```apache
RewriteEngine On
RewriteCond %{HTTP_HOST} !^www\.
RewriteRule ^(.*)$ http://www.%{HTTP_HOST}/$1 [R=301,L]
```

**HTTPS:**

```apache
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule .* https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteRule .* https://www.%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```
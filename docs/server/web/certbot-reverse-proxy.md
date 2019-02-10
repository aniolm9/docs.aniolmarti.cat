# Let's Encrypt on a reverse proxy

Brief notes about the problems found when configuring Let's Encrypt (Certbot) with a reverse proxy and different machines. Renewal files are located in `/etc/letsencrypt/renewal`.

## Virtualhosts in the same machine
Nothing special, just use Certbot webroot renew.

**Configuration example:**
```
# renew_before_expiry = 30 days
version = 0.10.2
archive_dir = /etc/letsencrypt/archive/example.com
cert = /etc/letsencrypt/live/example.com/cert.pem
privkey = /etc/letsencrypt/live/example.com/privkey.pem
chain = /etc/letsencrypt/live/example.com/chain.pem
fullchain = /etc/letsencrypt/live/example.com/fullchain.pem

# Options used in the renewal process
[renewalparams]
authenticator = webroot
installer = None
account = <ACCOUNT>

[[webroot_map]]                
www.example.com = /var/www/example.com
example.com = /var/www/example.com
```
## Virtualhosts in another machine
First we need to create a subdomain pointing to the device that has the reverse proxy installed. The root for this domain is **/var/www/example**. Taking that into account, the renewal file:
```
# renew_before_expiry = 30 days
version = 0.10.2
archive_dir = /etc/letsencrypt/archive/example.com
cert = /etc/letsencrypt/live/example.com/cert.pem
privkey = /etc/letsencrypt/live/example.com/privkey.pem
chain = /etc/letsencrypt/live/example.com/chain.pem
fullchain = /etc/letsencrypt/live/example.com/fullchain.pem

# Options used in the renewal process
[renewalparams]
authenticator = webroot
installer = None
account = <ACCOUNT>

[[webroot_map]]
www.example.com = /var/www/machine
blog.example.com = /var/www/machine
example.com = /var/www/machine
machine.example.com = /var/www/machine
```

Now we have two options:
### Option 1
**Add a line in the `.htaccess` where the web is located:**
```
Redirect 301 /.well-known http://machine.example.com/.well-known
```

### Option 2 (^^)
**Modify the nginx site in the reverse proxy server:**
```
location ^~ /.well-known/acme-challenge/ {
    root /var/www/machine;
}
```

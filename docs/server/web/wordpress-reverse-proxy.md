# Wordpress reverse proxy
When using a nginx reverse proxy to Apache and accessing the Wordpress site using HTTPS it returns a `Redirect Error`. Adding some lines to `wp-config.php` fixes it.

**After the line `define('WP_DEBUG', false)` add:**
```php
// Reverse proxy patch
if ( $_SERVER['HTTP_X_FORWARDED_PROTO'] == 'https' ) {
    $_SERVER['HTTPS'] = 'on';
    $_SERVER['SERVER_PORT'] = 443;
}
```
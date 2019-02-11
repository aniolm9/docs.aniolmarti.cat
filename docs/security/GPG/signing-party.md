# Signing party
1. Download the key.
2. Check the fingerprint.
3. Sign the key.
4. Export and send the key.
5. Assign trust.

We'll be exploring two options.

## Option 1: By hand
### Download the key
```bash
gpg --recv-keys <ANOTHER_ID>
```
### Check the fingerprint
```bash
gpg --fingerprint <ANOTHER_ID>
```
### Sign the key
```bash
gpg --sign-key <ANOTHER_ID>
```
### Export the key
```bash
gpg --armor --export <ANOTHER_ID> | gpg --encrypt -r <ANOTHER_ID> --armor --output <ANOTHER_ID>-signedBy-<KEY_ID>.asc
```
Then, send `<ANOTHER_ID>-signedBy-<KEY_ID>.asc` to the <KEY_ID> owner.
### Assign trust
```bash
gpg --edit-key <ANOTHER_ID>
gpg> trust
gpg> save
```
### Import the key
Once you receive your signed key you have to import it and make it public.
```bash
gpg --import <KEY_ID>-signedBy-<ANOTHER_ID>.asc
gpg --send-key <KEY_ID>
```

## Option 2: Using caff (^^)
### Install the needed packages
```bash
sudo apt install signing-party msmtp
```
### Configure msmtp
**Encrypt your e-mail password:**
```bash
gpg --encrypt -o ~/.msmtp/bob.gpg -r <KEY_ID> -
```
After typing your password press Enter and then Ctrl-D.

**Edit `~/.msmtprc`:**
```text
# Set default values for all following accounts.
defaults
auth on
tls on
tls_trust_file /etc/ssl/certs/ca-certificates.crt
logfile ~/.msmtp.log

account bob
host mail.example.com
port 587
from bob@example.com
user bob@example.com
passwordeval "gpg --quiet --for-your-eyes-only --no-tty --decrypt ~/.msmtp/bob.gpg"

# Set a default account
account default : bob
```

You can try it with:
```bash
msmtp <RECIPIENT_ADDRESS>
```

### Configure caff
**Edit `~/.caffrc`:**
```text
$CONFIG{'owner'} = 'Bob Morris';
$CONFIG{'email'} = 'bob@example.com';
$CONFIG{'keyid'} = [qw{<KEY_ID>}];
$ENV{'PERL_MAILERS'} = 'sendmail:/usr/bin/msmtp';
```

**Edit `~/.caff/gnupghome/gpg.conf`:**
```text
personal-digest-preferences SHA512
cert-digest-algo SHA512
default-preference-list SHA512 SHA384 SHA256 SHA224 AES256 AES192 AES CAST5 ZLIB BZIP2 ZIP Uncompressed
```

### Sign a key
From now on, when you need to sign a key just run:
```bash
caff <ANOTHER_ID>
```
It will ask you if you want to sign the UIDs and if you want to e-mail the signed keys.
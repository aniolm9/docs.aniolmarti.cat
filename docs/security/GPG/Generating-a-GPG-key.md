# Generating a GPG key

## Install GnuPG

```bash
sudo apt install gnupg dirmngr
```

## Increase security
**Edit the file `~/.gnupg/gpg.conf`:**
```text
require-cross-certification
keyserver hkp://keys.gnupg.net

# 32 bit IDs are too weak and have collisions
keyid-format long

personal-digest-preferences SHA512
cert-digest-algo SHA512
default-preference-list SHA512 SHA384 SHA256 SHA224 AES256 AES192 AES CAST5 ZLIB BZIP2 ZIP Uncompressed
no-emit-version 
no-comments 
personal-cipher-preferences AES AES256 AES192 CAST5
ignore-time-conflict 
allow-freeform-uid 
```

## Generate a key

**Generate the key:**
```bash
gpg --full-gen-key # 4096 bits and SHA-2 hashing is recommended.
```

**Generate the revoking certificate:**
```bash
gpg --gen-revoke <KEY_ID> > ~/.gnupg/revoke-<KEY_ID>.crt
```

**Make our key public:**
```bash
gpg --send-key <KEY_ID>
```

## Signing and encrypting

**Sign:**
```bash
gpg -[a]s <FILE>
```
* -a → Armor: The output is in ASCII.
* -s → Sign: Signs the given text (ends with Ctrl-D) or a file.

**Encrypt:**
```bash
gpg -[a]e -r <ANOTHER_ID> <FILE>
```
* -e → Encrypt: Encrypts the given text (ends with Ctrl-D) or a file.
* -r → Recipient: The ID of the person who will receive the encrypted message.

## Subkeys
### Generating the subkeys

**Backup the ~/.gnupg directory:**
```bash
umask 077 && tar -cvzf ~/gnupg-backup-pre.tgz ~/.gnupg
```
**Edit our key:**
```bash
gpg --edit-key <KEY_ID>
```
**Add the subkey:**
```bash
gpg> addkey
gpg> save
```

### Copying the subkeys
**Make a copy of the keyring:**
```bash
tar cvzf ~/gpg-backup-post.tgz ~/.gnupg
```

Copy `gpg-backup-post.tgz` to an encrypted USB device.

**Delete both local backups:**
```bash
shred -u ~/gpg-backup-post.tgz ~/gpg-backup-pre.tgz
```

### Importing the subkeys
The following commands are run in the laptop.

```bash
cp /media/encrypted-usb ~/gpg-backup-post.tgz
gpg --export-secret-subkeys <KEY_ID> > /media/encrypted-usb/subkeys
gpg --delete-secret-key <KEY_ID>
gpg --import /media/encrypted-usb/subkeys
shred -u /media/encrypted-usb/subkeys
```

We keep the backup of the master key in the encrypted USB device. Though, this device should be hidden somewhere.
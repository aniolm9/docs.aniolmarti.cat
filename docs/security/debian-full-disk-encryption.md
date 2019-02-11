# Debian full disk encryption

**Partition scheme:**
```text
Device Start End Sectors Size Type
/dev/nvme0n1p1 2048 1050623 1048576 512M EFI System
/dev/nvme0n1p2 1050624 1460223 409600 200M Linux filesystem
/dev/nvme0n1p3 1460224 500118158 498657935 237.8G Linux filesystem
```

Where the 2nd and 3rd partitions are encrypted using LUKS and the 3rd also has a LVM system

## Installing Debian

The first step is to install Debian as usual until it asks to **partition** the disk. There, we have to create a 512M (or less) partition for EFI, a **200M one for `/boot`** (ext4) and with the remaining space a **crypt-luks** one. Inside this last one we have to implement **LVM**. We can do it from the same Debian installer.

## Encrypting /boot

Once Debian is installed (and we have done a `/boot` backup) we boot with a Live of, for example, Arch Linux, in order to encrypt the `/boot` partition.

**Format the `/boot` partition:**
```bash
cryptsetup luksFormat /dev/nvm0n1p2
```

**Mount the partitions and chroot:**

```bash
cryptsetup open /dev/nvme0n1p3 cryptroot
cryptsetup open /dev/nvme0n1p2 cryptboot
mkfs.ext4 /dev/mapper/cryptboot
mount /dev/ziggy/root /mnt
mount /dev/mapper/cryptboot /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot/efi
arch-chroot /mnt
```

From now on, commands are executed inside the chroot.

**Load the PATH:**
```bash
source /etc/profile
```

**Enable support for encrypted GRUB editing `/etc/default/grub`:**
```bash
GRUB_CMDLINE_LINUX="cryptdevice=/dev/nvme0n1p3:lvm"
GRUB_ENABLE_CRYPTODISK=y
```

**Edit `/etc/crypttab`:**
```bash
cryptboot UUID=<uuid-boot> none luks
cryptroot UUID=<uuid-lvm>  none luks
```

**Edit `/etc/fstab`:**  
We comment the line that mounts `/boot` and we add:
```bash
/dev/mapper/cryptboot    /boot    ext4    errors=remount-ro    0    1
```

**Regenerate `/boot`:**
```bash
apt install --reinstall linux-image-4.9.0-8-amd64 # Or whatever version
mkdir /boot/grub
grub-mkconfig -o /boot/grub/grub.cfg
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=debian --recheck
```

Now `/boot` is encrypted. We can get out from the chroot, umount partition and reboot the system.

## Bonus: Avoid having to introduce the password 4 times

After this setup you have to introduce 4 times the password. Twice for GRUB (once for each partition) and twice to boot the kernel (also once for each partition).

### Kernel

To solve the kernel problem, we can use a _keyfile_.

**We can generate it using `dd`:**
```bash
dd if=/dev/random of=/crypto_keyfile.bin bs=1 count=4096
cryptsetup luksAddKey /dev/nvme0n1p2 /crypto_keyfile.bin
cryptsetup luksAddKey /dev/nvme0n1p3 /crypto_keyfile.bin
chmod 000 /crypto_keyfile.bin
chmod -R g-rwx,o-rwx /boot
```

**Edit `/etc/crypttab`:**
```bash
cryptboot UUID=<uuid-boot> /crypto_keyfile.bin luks,keyscript=/bin/cat
cryptroot UUID=<uuid-lvm>  /crypto_keyfile.bin luks,keyscript=/bin/cat
```

**Create the file `/etc/initramfs-tools/hooks/crypto_keyfile:`**
```bash
#!/bin/sh
cp /crypto_keyfile.bin "${DESTDIR}"
```

**We give it execution permissions and we update initramfs:**
```bash
chmod +x /etc/initramfs-tools/hooks/crypto_keyfile
update-initramfs -u
```

Now, if we reboot the system it should only ask for GRUB's passphrase.

### GRUB
GRUB asks to **decrypt both partitions** in order to load the backgroun image. I only see the GRUB screen a few seconds a day, so **I don't care if I don't have a background image**. I rather prefer having to **introduce the passphrase just once**.

**Remove execution permissions:**
```bash
chmod -x /etc/grub.d/05_debian_theme
```

**Update GRUB:**
```bash
update-grub
```

And that's all.

## Other links
**Avoind having to reboot the computer when missing the passphrase (grub rescue):** [https://blog.stigok.com/post/decrypt-and-mount-luks-disk-from-grub-rescue-mode](https://blog.stigok.com/post/decrypt-and-mount-luks-disk-from-grub-rescue-mode)  
**Solving error "volume not found":** [https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=671037](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=671037)

## Sources
[https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system)  
[http://www.pavelkogan.com/2014/05/23/luks-full-disk-encryption/](http://www.pavelkogan.com/2014/05/23/luks-full-disk-encryption/)
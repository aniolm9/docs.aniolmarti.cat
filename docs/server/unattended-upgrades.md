# Unattended Upgrades

**unattended-upgrades** is a package that allows to perform automatic system upgrades. A very simple configuration is shown below.

**Install the package:**
```bash
sudo apt install unattended-upgrades
```

**Edit the file `/etc/apt/apt.conf.d/20auto-upgrades`:**

```text
// Enable the update/upgrade script (0=disable)
APT::Periodic::Enable "1";

// Do "apt-get update" automatically every n-days (0=disable)
APT::Periodic::Update-Package-Lists "1";

// Do "apt-get upgrade --download-only" every n-days (0=disable)
APT::Periodic::Download-Upgradeable-Packages "1";

// Run the "unattended-upgrade" security upgrade script
// every n-days (0=disabled)
// Requires the package "unattended-upgrades" and will write
// a log in /var/log/unattended-upgrades
APT::Periodic::Unattended-Upgrade "1";

// Do "apt-get autoclean" every n-days (0=disable)
APT::Periodic::AutocleanInterval "7";
```

**Uncomment some lines in `/etc/apt/apt.conf.d/50unattended-upgrades`:**

```text
"o=Debian,a=stable";
"o=Debian,a=stable-updates";
//"o=Debian,a=proposed-updates";
"origin=Debian,codename=${distro_codename},label=Debian-Security";
```

**Restart the service:**

```bash
sudo systemctl restart unattended-upgrades
```

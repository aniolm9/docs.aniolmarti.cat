# Basic configuration
Obviously, using non-free and contrib repos and installing Java and the kernel from backports is not needed, but may be useful, so I'm keeping it here.

## Sudo
**Install the package:**

```bash
su
apt install sudo
```

**Add a user:**

```bash
adduser <user> sudo
```

## Repositories

**Install vim:**

```bash
sudo apt install vim
```

```bash
sudo vim /etc/apt/sources.list
```

**Delete everything and add:**

```text
## Official
deb http://ftp.caliu.cat/debian/ stretch main contrib non-free
deb-src http://ftp.caliu.cat/debian/ stretch main contrib non-free

## Updates
deb http://security.debian.org/ stretch/updates main
deb-src http://security.debian.org/ stretch/updates main
deb http://ftp.caliu.cat/debian/ stretch-updates main
deb-src http://ftp.caliu.cat/debian/ stretch-updates main

## Backports
deb http://http.debian.net/debian stretch-backports main
```

I'm using Caliu.cat repos as they are the nearest to my location.

**Update:**

```bash
sudo apt update
```

**Install build-essentials and git:**

```bash
sudo apt install linux-headers-$(uname -r) build-essential checkinstall make automake cmake autoconf git git-core
```

**Compressor:**

```bash
sudo apt install rar unrar p7zip-full p7zip-rar
```

**Update and upgrade:**

```bash
sudo apt update
sudo apt upgrade
```

## Installing the kernel from backports

**Check for new versions:**

```bash
sudo apt-cache search linux-image
```

**Install the kernel:**

```bash
sudo apt -t stretch-backports install <linux-image>
```

## Installing Java
**Add the repos:**

```bash
echo "## Java" >> /etc/apt/sources.list.d/java
echo "deb http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" >> /etc/apt/sources.list.d/java
echo "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" >> /etc/apt/sources.list.d/java
```

**Install the key:**

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys EEA14886
```

**Update:**

```bash
sudo apt update
```

**Install Java:**

```bash
sudo apt install oracle-java8-installer
```

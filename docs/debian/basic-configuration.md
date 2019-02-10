# Basic configuration
Obviously, using non-free and contrib repos and installing Java and the kernel from backports is not needed, but may be useful, so I'm keeping it here.

## Sudo
**Install the package:**

```
$ su
# apt install sudo
```

**Add a user:**

```
# adduser <user> sudo
```

## Repositories

**Install vim:**

```
$ sudo apt install vim
```

```
$ sudo vim /etc/apt/sources.list
```

**Delete everything and add:**

```
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

```
$ sudo apt update
```

**Install build-essentials and git:**

```
$ sudo apt install linux-headers-$(uname -r) build-essential checkinstall make automake cmake autoconf git git-core
```

**Compressor:**

```
$ sudo apt install rar unrar p7zip-full p7zip-rar
```

**Update and upgrade:**

```
$ sudo apt update
$ sudo apt upgrade
```

## Installing the kernel from backports

**Check for new versions:**

```
$ sudo apt-cache search linux-image
```

**Install the kernel:**

```
$ sudo apt -t stretch-backports install <linux-image>
```

## Installing Java
**Add the repos:**

```
echo "## Java" >> /etc/apt/sources.list.d/java
echo "deb http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" >> /etc/apt/sources.list.d/java
echo "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" >> /etc/apt/sources.list.d/java
```

**Install the key:**

```
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys EEA14886
```

**Update:**

```
$ sudo apt update
```

**Install Java:**

```
$ sudo apt install oracle-java8-installer
```

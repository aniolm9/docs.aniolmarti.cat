# Building a package
Implementing a chroot with Sid: [https://perl-team.pages.debian.net/autopkgtest.html#SETUP](https://perl-team.pages.debian.net/autopkgtest.html#SETUP)

## In the chroot

**Install:**

```bash
sudo apt install --no-install-recommends devscripts debhelper pbuilder git vim build-essential dh-make dh-python python3-setuptools lintian quilt autopkgtest
```

**Set an alias for Lintian:**
```
echo 'alias lintian="lintian --pedantic --display-info"' > ~/.bashrc
```

**File `~/.pbuilderrc` and `/root/.pbuilderrc`:**

```text
APTCACHE=/var/cache/pbuilder/aptcache
ARCHITECTURE=$(dpkg --print-architecture)
AUTO_DEBSIGN=no
BUILDRESULT=/build
DEBEMAIL="Nom Cognom <elmeuemail@elmeudomini.com>"
DISTRIBUTION=sid
MIRRORSITE=http://deb.debian.org/debian
EATMYDATA=yes
```

**Create the environment:**

```bash
sudo pbuilder create
```

From now on, when we need to build a package we will just have to enter in a chroot.

**Inside the directory with the source code of the package:**

```bash
sudo pbuilder update && pdebuild
```

The resulting files will be in `/build`.  
`/build` can be found in the host system in `/var/lib/sbuild/build`.

## Export Display

**In the host:**

```bash
xhost +local:
```

**In the chroot:**

```
export DISPLAY=:0
```

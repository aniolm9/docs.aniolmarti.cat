# Building a package
Implementing a chroot with Sid: [https://perl-team.pages.debian.net/autopkgtest.html#SETUP](https://perl-team.pages.debian.net/autopkgtest.html#SETUP)

## In the host:
**File `~/.sbuildrc`:**
```text
$maintainer_name='Name Surname <email@example.com>';
$distribution = "unstable";
$chroot = "sid-amd64-sbuild";
$build_arch_all = 1;
$purge_build_directory = 'successful';
$purge_session = 'successful';
$purge_build_deps = 'successful';
$build_source = 1;
$source_only_changes = 1;
1;
```

**File `~/.devscripts:**
```text
DEBSIGN_KEYID="0x40-CHARS-GPG-KEY-ID"
```

## In the chroot

**Install:**

```bash
sudo apt install --no-install-recommends devscripts debhelper pbuilder git vim build-essential dh-make dh-python python3-setuptools lintian quilt autopkgtest eatmydata blhc
```

**File ~/.lintianrc:**
```text
LINTIAN_PROFILE=debian
pedantic=yes
display-experimental=yes
verbose=yes
display-info=yes
```

**File `~/.pbuilderrc` and `/root/.pbuilderrc`:**

```text
APTCACHE=/var/cache/pbuilder/aptcache
ARCHITECTURE=$(dpkg --print-architecture)
AUTO_DEBSIGN=no
BUILDRESULT=/build
DEBEMAIL="Name Surname <email@example.com>"
DISTRIBUTION=sid
MIRRORSITE=http://deb.debian.org/debian
EATMYDATA=yes
```

**Export `DEBEMAIL`:**
```bash
echo 'DEBEMAIL="Name Surname <email@example.com>"' > /etc/profile.d/01-debemail.sh
echo "export DEBEMAIL" >> /etc/profile.d/01-debemail.sh
```

** Set Quilt patches path:**
```bash
echo "QUILT_PATCHES=debian/patches" > /home/<user>/.quiltrc
chown <user>:<user> /home/<user>/.quiltrc
```

**Create the environment:**

```bash
pbuilder create
```

From now on, when we need to build a package we will just have to enter in a chroot running `schroot -c sid-amd64-sbuild`.

**Inside the directory with the source code of the package:**

```bash
sudo pbuilder update && pdebuild
```

The resulting files will be in `/build`.  
`/build` can be found in the host system in `/var/lib/sbuild/build`.

## Run a test
If there are tests configured in `debian/tests` you can test them with:
```bash
sudo autopkgtest -- null
```

## Export Display

**In the host:**

```bash
xhost +local:
```

**In the chroot:**

```
export DISPLAY=:0
```

## Configure Salsa CI
First read the Salsa CI Team documentation to activate it: [https://salsa.debian.org/salsa-ci-team/pipeline](https://salsa.debian.org/salsa-ci-team/pipeline)

### Setting up a pristine-tar branch
1. Create a `upstream/<version>` branch with the content of the upstream file (usually a .tar.gz).
2. Rename the upstream package to `<package>_<version>.orig.tar.gz`.
3. Create and push the pristine-tar branch:
```bash
pristine-tar commit <package>_<version>.orig.tar.gz upstream/<version>
git push -u origin pristine-tar
```

### Creating the Git Build Package configuration file
**Create the file `debian/gbp.conf` with the following content:**
```text
[DEFAULT]
pristine-tar = True
```
Commit and push the changes.

## Forward patches to upstream
Based on the Perl Team documentation: [https://perl-team.pages.debian.net/forward.html](https://perl-team.pages.debian.net/forward.html)

**Install Perl Team packaging tools:**
```bash
sudo apt install pkg-perl-tools
```

**Create the file `debian/upstream/metadata` with the following content:**
```yaml
Name: <PROJECT NAME>
Archive: GitHub
Repository: https://github.com/<AUTHOR>/<REPO>.git
Repository-Browse: https://github.com/<AUTHOR>/<REPO>
Bug-Database: https://github.com/<AUTHOR>/<REPO>/issues
Bug-Submit: https://github.com/<AUTHOR>/<REPO>/issues/new
```

**Create the file `~/.dpt.conf` with the following content:**
```text
DPT_GITHUB_ORGNAME=<ORGANIZATION NAME>
DPT_GITHUB_OAUTH=<PERSONAL ACCESS TOKEN>
DPT_GITHUB_BRANCH=pkg-debian-$(date +%s)
```

To forward the patch just run:
```bash
dpt forward debian/patches/<PATCH FILE>
```

### Uploading a package
**In your host run the following command inside the package directory:**
```bash
sbuild -s -d unstable -c sid-amd64-sbuild --source-only-changes
```

**Sign the package:**
```bash
debsign <package>_<version>_source.changes
```

**Upload the package:**
```bash
dput <package>_<version>_source.changes
```

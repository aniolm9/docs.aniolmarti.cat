# Updating a package
This document pretends to be a checklist of the tasks to perform when updating a Debian package.

## New upstream version
1. Create a new branch `upstream/<version>`.
2. Delete all the files in that branch.
3. Download the last upstream release (i.e.: `origtargz`).
4. Uncompress the upstream file to the new branch.
5. Merge the new branch with the development branch.
6. Add the upstream package to the `pristine-tar` branch:

## General procedure
1. Create a new entry to `d/changelog`:
2. Make all the changes.
3. Build the package (make sure you have downloaded the upstream package).
4. Run Lintian to check the quality of the package.
5. Run autopkgtest. 
6. Change **UNRELEASED** to **unstable** in `d/changelog`.

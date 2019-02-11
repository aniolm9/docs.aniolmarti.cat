# Listing packages from a given origin

Just run:
```bash
ORIGIN=deb-multimedia # For example
dpkg -l | awk '/^.i/ {print $2}' | xargs apt-cache policy | awk '/^[a-z0-9.-]+:/ {pkg=$1}; /***/ {OFS="\t"; ver=$2; getline; print pkg,ver,$2,$3}'|grep -v /var/lib/dpkg/status| sed -e 's/://'|awk '{printf "%-40s %-36s %-36s %-16s \n",$1,$2,$3, $4}' | grep -i $ORIGIN | cut -d" " -f1
```

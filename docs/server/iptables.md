# iptables cheatsheet
## List rules
```bash
sudo iptables -L
```
## Flush rules
```bash
sudo iptables -F
```
## Block ports
### Keep established connections
```bash
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
```
1. `-A` adds the rule.
2. `INPUT` defines the rule as part of the input string.
3. `-m conntrack` with `--ctstate ESTABLISHED,RELATED`  ensures that only applies to established and related connections.
4. `-j ACCEPT` accepts the packages.

### Accept desired ports
```bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
```
1. `-p` sets the connection protocol.
2. `--dport` sets the connection port.

### Block other connections
```bash
sudo iptables -P INPUT DROP
```

### Allow loopback connections
```bash
sudo iptables -I INPUT 1 -i lo -j ACCEPT
```
1. `-I INPUT 1` places the rule first in the table.
2. `-i lo` loopback interface.

## Saving the rules
```bash
sudo apt install iptables-persistent
sudo iptables-save > /etc/iptables/rules.v4
```
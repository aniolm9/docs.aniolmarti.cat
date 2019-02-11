# SSH fingerprint
To get the fingerprint from a server you just need to run:
```bash
ssh-keygen -lf /etc/ssh/ssh_host_*_key.pub
```

It will print the fingerprint of every key (in modern Linux systems: ECDSA, RSA and ed25519).

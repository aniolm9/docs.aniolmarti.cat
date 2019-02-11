# Using Yubikey
We will be using a GPG key as a SSH one with the Yubikey. I guess that the Yubikey already contains a sign, an encrypt and an authorize subkeys. See [the Yubikey official site](https://support.yubico.com/support/solutions/articles/15000006420-using-your-yubikey-with-openpgp) for further details.

**Install some packages:**
```bash
sudo apt install gnupg2 pcscd scdaemon
```

**Edit `~/.gnupg/gpg-agent.conf`:**
```text
enable-ssh-support
default-cache-ttl-ssh 10800
max-cache-ttl-ssh 10800
```

**Edit `~/.bashrc`:**
```bash
# Set SSH to use gpg-agent
unset SSH_AGENT_PID
if [ "${gnupg_SSH_AUTH_SOCK_by:-0}" -ne $$ ]; then
  export SSH_AUTH_SOCK="$(gpgconf --list-dirs agent-ssh-socket)"
fi

# Set GPG TTY
export GPG_TTY=$(tty)

# Refresh gpg-agent tty in case user switches into an X session
gpg-connect-agent updatestartuptty /bye >/dev/null
```

**To check if it works:**
```bash
ssh-add -L
ssh-rsa AAAAB3NzaC ... pdqtlwX6m1 cardno:000123457915 # Output
```
# KVM NAT script

**Create the file `/etc/libvirt/hooks/qemu`:**
```bash
#!/bin/bash
#
# Adaptat de l'orginal disponible a:
# http://wiki.libvirt.org/page/Networking#Forwarding_Incoming_Connections
#

DEBUG="/etc/libvirt/hooks/qemu: $0 $*"
logger "$DEBUG"
echo "$DEBUG"

Guest_name="${1}"
VIRSH_CMD="${2}"
PUBLIC_IP="192.168.1.30"

# afegir linies al final amb els redireccionaments.
redirecciona() {
    Host_port="${1}"
    Guest_ipaddr="${2}"
    Guest_port="${3}"
    Protocol="${4}"

    if [ "$VIRSH_CMD" = "stopped" -o "$VIRSH_CMD" = "reconnect" ]; then
        iptables -t nat -D PREROUTING -d ${PUBLIC_IP} -p ${Protocol} --dport ${Host_port} -j DNAT \
            --to ${Guest_ipaddr}:${Guest_port}
        iptables -D FORWARD -d ${Guest_ipaddr}/32 -p ${Protocol} -m state --state NEW \
            -m ${Protocol} --dport ${Guest_port} -j ACCEPT
    fi

    if [ "$VIRSH_CMD" = "start" -o "$VIRSH_CMD" = "reconnect" ]; then
        iptables -t nat -A PREROUTING -d ${PUBLIC_IP} -p ${Protocol} --dport ${Host_port} -j DNAT \
            --to ${Guest_ipaddr}:${Guest_port}
        iptables -I FORWARD -d ${Guest_ipaddr}/32 -p ${Protocol} -m state --state NEW \
            -m ${Protocol} --dport ${Guest_port} -j ACCEPT
    fi
}

# afegir el case de cada equip, i a dins una linia per cada servei.
# redirecciona  <host_port> <guest_address> <guest_port> <protocol>
case "$Guest_name" in
    vm1)
        redirecciona 2210 192.168.122.77 2210 tcp
        redirecciona 25826 192.168.122.77 25826 udp
        ;;

    vm2)
        redirecciona 2220 192.168.122.98 2220 tcp
        ;;
esac
```

**Set exec permissions:**
```bash
chmod +x /etc/libvirt/hooks/qemu
```

**Edit `/etc/apparmor.d/usr.sbin.libvirtd` under the line `/usr/sbin/* PUx,`:**
```text
/etc/libvirt/hooks/* PUx,
```
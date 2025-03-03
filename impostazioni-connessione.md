# Impostazioni di connessione di paperaccio

## IP address
    192.168.1.1
## subnet 
    255.255.255.0
## gateway
    192.168.1.254
## DNS server
    192.168.1.254
## nome
    paperaccio.io

## accesso da ssh
Comando:

    ssh root@192.168.1.1
    
Per uscire da ssh:

    exit
    Oppure CTRL+D

## wake on lan
Comando:

    wakeonlan 90:1b:0e:87:dd:f7

## troubleshooting
### problema iniziale
La scheda di rete (enp1s0) non ha un indirizzo IP
### soluzione
Commento 9 su
https://forum.proxmox.com/threads/network-is-unreachable-after-installation-unable-to-access-the-web-gui.124187/
Modificare il file /etc/network/interfaces con nano:

    auto lo
    iface lo inet loopback

    auto enp3s0f0 # edit this like its on "ip a" could also be eth0 or similar (QUI IMPORTANTE!)
    iface enp3s0f0 inet manual
    dns-nameservers 8.8.8.8

    auto vmbr0
    iface vmbr0 inet static
    address 10.0.0.10/16 # the ip your proxmox should have
    gateway 10.0.0.1
    bridge-ports enp3s0f0 # the network interface where to bridge to
    bridge-stp off
    bridge-fd 0
Salvare e riavviare il server oppure "systemctl restart networking.service"
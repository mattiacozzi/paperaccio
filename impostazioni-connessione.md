# Impostazioni di connessione di paperaccio

## IP address
    192.168.1.1
## Subnet 
    255.255.255.0
## gateway
    192.168.1.254
## DNS server
    192.168.1.254
Meglio lasciare il router e non impostare Pi-hole per PVE.
## Nome
    paperaccio.io

## Accesso da SSH
Comando:

    ssh root@192.168.1.1
    
Per uscire da ssh:

    exit
    Oppure CTRL+D

## Wake On LAN
Comando:

    wakeonlan 90:1b:0e:87:dd:f7

## Troubleshooting
### Problema iniziale
La scheda di rete (enp1s0) non ha un indirizzo IP
#### Soluzione
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

Salvare e riavviare il server oppure 
    
    systemctl restart networking.service

### Dopo un aggiornamento e un riavvio, host irraggiungibile
Entrare in paperaccio e dare

    ip a

Noto che il nome della scheda di rete (NIC) è cambiato. 
Per risolvere, ho dovuto:

    nano /etc/network/interfaces

e modificare il file con il nuovo nome della scheda di rete. Era "enp3s0f0", poi è diventato "eth0". Sostituire tutte le occorrenze del vecchio nome con il nuovo. Attenzione anche a farlo nella seconda parte del file, quella del bridge (vmbr0).
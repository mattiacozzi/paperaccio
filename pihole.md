# Impostazioni Pi-hole

## Indirizzo IP del container (LXC, Linux Container)
    192.168.1.198

## Guida su Pi-hole
- https://www.naturalborncoder.com/2023/07/installing-pi-hole-on-proxmox/

- https://www.youtube.com/watch?v=rDNWhwWCk2k

## Guida generale sui container
- https://www.youtube.com/watch?v=5I2aw_yVcRM

## Impostazioni
    RAM: 512 Mb
    HDD: 8 Gb
    Cores: 1
    Template: last Debian

## Aggiornamento
Nella console del container:

    pihole -up

## Troubleshooting

### Non parte la shell del container

    do you have network ipv6 = DHCP but you don't have ipv6? that hangs containers sometimes.
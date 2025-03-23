# Impostazione qBittorrent LXC
## Script
https://community-scripts.github.io/ProxmoxVE/scripts?id=qbittorrent

## Impostazione della cartella condivisa (data)
Fare come qui: https://youtu.be/Uzqf0qlcQlo?t=2646

Dalla shell del server:

    cd /etc/pve/lxc

E faccio "cat" sull'impostazione del vault (vedi storage):

    cat 102.conf

Copio la riga del mountpoint0, in modo da poterla poi copiare nel container che sto creando e dare anche a lui l'accesso alla stessa cartella.

    mp0: data:102/vm-102-disk-0.raw,mp=/data,backup=1,size=800G

Attenzione: decidere se si vuole includere nel backup il punto montaggio, ovviamente è meglio di no.

Ora, restando nella stessa cartella:

    nano 104.conf

e aggiungo la riga precedente sotto a net0. Adesso il container per i torrent ha accesso allo share.

## Indirizzo IP

    http://192.168.1.184:8080/

## Cartelle download
Temp

    /data/tempdowload

Finiti

    /data/download

## Avvio automatico
Per avviare in automatico "qbittorrent-nox" uso un crontab, come descritto in https://www.simplified.guide/linux/automatically-run-program-on-startup#running-a-program-automatically-on-linux-startup-via-rclocal

## Plugin di ricerca torrent
https://github.com/qbittorrent/search-plugins/wiki/Install-search-plugins#steps-to-install-search-plugins-qbittorrent-version-3110-or-more-recent

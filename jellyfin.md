#Installazione e impostazione Jellyfin
## Guida video
https://www.youtube.com/watch?v=Uzqf0qlcQlo

## Guida alternativa
https://www.youtube.com/watch?v=AMhTLldaX54

## Collezione di scripts
https://community-scripts.github.io/ProxmoxVE/scripts

## Informazioni sul container
Root password

    solitalunga

Container ID

    103

Indirizzo IP

    192.168.1.135

Porta

    8096

Link di accesso

    http://192.168.1.135:8096

Jellyfin username

    paperjelly

Password

    solita

## Aggiungere storage al container
Dalla shell del server:

    cd /etc/pve/lxc

E faccio "cat" sull'impostazione del vault (vedi storage):

    cat 102.conf

Copio la riga del mountpoint0, in modo da poterla poi copiare nel container di Jellyfin e dare anche a lui l'accesso alla stessa cartella.

    mp0: data:102/vm-102-disk-0.raw,mp=/data,backup=1,size=800G

Ora, restando nella stessa cartella:

    nano 103.conf

e aggiungo la riga precedente sotto a net0. Adesso il container di Jellyfin ha accesso allo share.

## Spostare files

Uso il samba share che ho impostato.
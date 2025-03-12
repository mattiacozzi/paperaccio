#Installazione e impostazione Jellyfin
## Guida video
https://www.youtube.com/watch?v=AMhTLldaX54

## Collezione di scripts
https://community-scripts.github.io/ProxmoxVE/scripts

## Informazioni sul container
Container ID

    102

Username

    paperjelly

Password

    solita

Indirizzo IP

    192.168.1.142

Porta

    8096

Link di accesso

    http://192.168.1.142:8096

## Aggiungere storage al container
Clic sul container, poi "Risorse" e "Aggiungi punto di montaggio". Seleziono la dimensione e "Path" è dove vedrò montato il disco nel container.

Per Jellyfin, imposto:

    /mnt/storage

Posso togliere la spunta da "Backup", perché non mi interessa fare il backup dei film!

Perché qui non posso aggiungere i dataset che possiedo???

Adesso posso accedere al disco virtuale dal container. Mi loggo nel container e 

## Spostare files

https://forum.proxmox.com/threads/help-moving-files-from-pc-into-an-lxc.142713/

Meglio:

https://www.reddit.com/r/Proxmox/comments/1d3xz04/copy_file_from_pc_to_lxc_storage/


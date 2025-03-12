# Impostazione dei dischi
## Tipo di RAID
RAID 1, o Mirroring, che copia i dati su due dischi. Richiede due dischi della stessa dimensione.

Altri tipi di RAID richiedono un numero maggiore di dischi.

## Filesystem
Usare ZFS.

## Cosa è un pool?
È un insieme di uno o più dischi virtuali (vdevs) che figurano come un unico disco di archiviazione accessibile al file system. 

Il Zpool è il contenitore di livello più alto in un sistema ZFS.

Ogni vdev consiste poi in uno o più dischi fisici.

I mirror vdevs sono dischi in cui ogni blocco di dati è salvato su ogni disco.

https://arstechnica.com/information-technology/2020/05/zfs-101-understanding-zfs-storage-and-performance/

https://cdn.arstechnica.net/wp-content/uploads/2020/05/fullpooldiagram-updated-vdevnames.png

## Dataset
Un dataset ZFS appare al sistema operativo come un normale dispositivo montato, cioè come una cartella.

## Dettagli del mio pool
Il nome del pool è:

    storage

Il tipo di ridondanza è:

    mirror

La capacità massima è di 1 TB, perché utilizzo, per via del mirroring, il 50% dello spazio.

Una volta impostato il pool, lo vedo in paperaccio come "storage (paperaccio)".

Ora posso utilizzare il mio pool per le VM, i container, e vederlo come cartella.

Per vedere gli storage configurati in PVE, uso:

    nano /etc/pve/storage.cfg

Ottengo:

    zfspool: storage
        pool storage
        content images,rootdir
        mountpoint /storage
        nodes paperaccio

Da notare il mountpoint, che inizia con uno slash.

## Comandi di controllo pool
Posso anche vedere i pool ZFS con:

    zpool list

Se uso:

    zfs list

Ottengo:

    NAME             USED  AVAIL  REFER  MOUNTPOINT
    storage         1.25G   898G   104K  /storage
    storage/data    1.25G   898G  1.25G  /data
    storage/foto      96K   898G    96K  /storage/foto
    storage/musica    96K   898G    96K  /storage/musica
    storage/video     96K   898G    96K  /storage/video


"storage" è il nome del mio primo dataset, "data" è il nome del secondo (interno al primo), eccetera.

## Creazione dataset
Posso creare nuovi dataset con:

    zfs create storage/NOME

## Impostare storage su PVE
Dall'interfaccia web, "Datacenter", e dal menu "Storage" clicco su "Add" e "Directory".

Imposto la directory per PVE, ovvero:

    /storage/NOME

e imposto il tipo di contenuto (controllo nel caso dalla shell quali cartelle ho a disposizione).

Adesso il mio dataset è accessibile a PVE.

## Aggiungere un dataset a un LXC
Creo un container, poi clic su "Risorse" ???

## Impostare i backup
Creo un dataset per i backup (vedi sopra) sul mio pool e da "Datacenter" imposto il backup.

## Guida in italiano
https://www.youtube.com/watch?v=RjutVOG-vGE

## Guida grezza
https://technonagib.com/sata-usb-disks-proxmox-ve/

## Guida specifica
https://www.youtube.com/watch?v=HqOGeqT-SCA

## Guida con tutto USA LEI!!!
https://www.youtube.com/watch?v=zLFB6ulC0Fg

Guarda anche il video successivo per impostare jellyfin come fa lui

## Guida molto chiara
https://www.youtube.com/watch?v=oSD-VoloQag
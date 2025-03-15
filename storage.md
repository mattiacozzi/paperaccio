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

    vault

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
    vault            1.25G   898G   104K  /vault

"vault" è il nome del mio primo dataset.

## Creazione dataset
Posso creare nuovi dataset con:

    zfs create storage/NOME

## Impostare storage su PVE
Dall'interfaccia web, "Datacenter", e dal menu "Storage" clicco su "Add" e "Directory".

Imposto la directory per PVE, ovvero:

    /vault/data

e imposto il tipo di contenuto (controllo nel caso dalla shell quali cartelle ho a disposizione).

Adesso il mio dataset è accessibile a PVE.

## Creazione di un container per le condivisioni di rete con Cockpit
Creo un LXC con Ubuntu come base, lo chiamo "vault". Username root, pass solitalunga

Aggiungo poi il mountpoint per il pool "data".

Per gestire i network share install COCKPIT, molto semplice e comodo, con GUI.
    
    apt install --no-install-recommends cockpit

Per accedere da Windows:

    apt install wsdd 

Per raggiungere Cockpit (trova nel caso l'IP con "ip a" nella shell del container):

    192.168.1.64:9090

Faccio login con le credenziali del container (vedi sopra).

Installazione di diversi servizi di cockpit: file-sharing navigator, identities (vedi video).

Adesso posso creare i miei utenti per accedere alle condivisioni di rete.

Creo un nuovo gruppo chiamato "data-share".

Creo un nuovo utente "mattia" e lo assegno ai gruppo "data-share", "users" e "sudo". Pass solita corta.

Imposto la pass di samba (solita) e creo un server samba chiamato "Vault".

Adesso posso accedere allo share da 

    smb://192.168.1.64

Username mattia pass solita corta.

## Impostare i backup
Creo un dataset per i backup (vedi sopra) sul mio pool e da "Datacenter" imposto il backup.

## Guida
https://www.youtube.com/watch?v=zLFB6ulC0Fg
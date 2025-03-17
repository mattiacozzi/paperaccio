# Backup di PVE, VM, LXC

## Guida video
https://www.youtube.com/watch?v=NnKQnqoEm-I

## Snapshot VS backup
Uno snapshot è un clone di una parte di una VM, salvato all'interno della VM.
Possono essere utili se sto testando del software in una VM e poi decido di tornare indietro.

Un backup è un clone esatto di una VM, salvato all'esterno della VM.
Possono essere utili quando una VM è impostata come voglio e salvare quelle impostazioni.

## Gestione snapshot
Tutto via GUI.
Posso anche includere la RAM nello snapshot.

## Gestione backup
Via GUI, posso decidere dove salvare il backup.
Nel mio caso, conviene salvarlo sui dischi rigidi. Ho già creato un pool per i backups, che posso vedere da:

    nano /etc/pve/storage.cfg

Nella GUI, lo vedo dal "Datacenter".

Quando creo un backup, devo pensare alla modalità. Se mi posso permettere del downtime, meglio spegnere la VM e usare la modalità STOP, che garantisce una maggiore integrità dei dati.

Se sto facendo il backup di una VM (non di un LXC) e ho installato l'agent di Qemu, benissimo la modalità snapshot.

## Backup dell'host
https://youtu.be/kcpu4z5eSEU?t=480

Da https://forum.proxmox.com/threads/official-way-to-backup-proxmox-ve-itself.126469/#post-552579

    /var/lib/pve-cluster/config.db  OK
    /etc/corosync/*                 vuota
    /etc/network/interfaces         OK
    /etc/hostname                   OK
    /etc/hosts                      OK

Sono nella cartella "backups".

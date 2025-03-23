# Installazione di Immich per il backup delle foto

## LXC e storage
Crea un container con Docker (uso uno script) e aggiungere al container lo storage di vault, come fatto per altri.

Dalla shell del server:

    cd /etc/pve/lxc

E faccio "cat" sull'impostazione del vault (vedi storage):

    cat 102.conf

Copio la riga del mountpoint0, in modo da poterla poi copiare nel container di Docker e dare anche a lui l'accesso alla stessa cartella.

    mp0: data:102/vm-102-disk-0.raw,mp=/data,backup=1,size=800G

Attenzione: decidere se si vuole includere nel backup il punto montaggio, ovviamente è meglio di no.

Ora, restando nella stessa cartella:

    nano 104.conf

e aggiungo la riga precedente sotto a net0. Adesso il container per Immich ha accesso allo share.

## Installazione di Immich
https://immich.app/docs/install/docker-compose

La location per le foto è

    /data/photos

La location per il db è

    /data/photos/postgres

La pass è la solitacorta. Nome utente e nome db sono il default.

Ho dovuto aumentare a 8 gb lo storage del container, mi dava un errore di spazio finito.

Ho dovuto anche commentare commentare la riga per lo start_interval nella sezione database del file docker-compose.yml, come riportato dalla guida.

## Dati accesso
Email admin

    cozzimattia@gmail.com

Password amministratore solitacorta.

## Struttura files

    /photos/library/[user]/[year]/[month]-[day]

## Esportazione foto Google
Usare il servizio "Google Takeout".
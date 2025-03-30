# Container aMule
## Installazione
Installo un container con lo script per Docker. Nome root pass solitalunga. Installo Docker:

    apt install docker docker-compose

Aggiungo lo storage come al solito.

Utilizzo questa guida: https://hub.docker.com/r/minnyres/amule-dlp e https://github.com/t-chab/dockerfiles/tree/master/amule

Pull dell'immagine:

    docker pull minnyres/amule-dlp

docker-compose.yml

    version: '2.1'
    services:
    amule:
        container_name: amule
        image: minnyres/amule-dlp:latest
        restart: unless-stopped
        network_mode: host
        environment:
        - UID=1000
        - GID=1000
        - WEBUI=reloaded
        - ECPASSWD=dsc
        - TIMEZONE=Europe/Rome
        - RECURSIVE_SHARE=yes
        volumes:
        - /conf:/amule/config
        - /data:/data/download
        - /tmp:/data/tempdownload

Cartella per i file temporanei:

    /data/tempdownload

Cartella per i download:

    /data/download

Pass webui: amuleweb-passwd


## Avvio automatico
Per avviare in automatico "docker start amule" uso un crontab, come descritto in https://www.simplified.guide/linux/automatically-run-program-on-startup#running-a-program-automatically-on-linux-startup-via-rclocal

Comando:

    crontab -e

Scelgo nano e al file aggiungo

    @reboot docker start amule

# Bind mount
https://docs.docker.com/engine/storage/bind-mounts/

Dove sono i miei file scaricati? Trovato col comando:

    find . -name *.cbz

Risultato:

    ./var/lib/docker/volumes/27725d6e8ecb27b3ed465fe146fd8f9543bf70097c37d8be5a2045b5622c5940/_data/IRON MAN - 43 Director de Shield (by Fito) [MQ][DI].cbz

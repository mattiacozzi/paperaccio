# Comunicazione di Proxmox con l'UPS

## Guide
https://www.youtube.com/watch?app=desktop&v=vyBP7wpN72c&t=6m30s&ab_channel=TechnoTim
Appena iniziata

https://www.youtube.com/watch?v=h8QE0Eva_m8&ab_channel=TT%28M%29O

https://www.alanbonnici.com/2024/08/proxmox-nut-homelab-howto-step-5.html

https://www.kreaweb.be/diy-home-server-2021-software-proxmox-ups/

https://chribonn.medium.com/proxmox-nut-homelab-howto-step-5-install-nut-ups-solution-configure-nut-for-email-alerts-and-be74838fdccb

## Installazione di NUT (Network UPS Tools)
    apt install nut nut-client nut-server

Controllo i dispositivi USB

    lsusb

Output:

    Bus 003 Device 002: ID 0001:0000 Fry's Electronics MEC0003

Controllo le informazioni sull'UPS connesso con:

    nut-scanner -U

Output:

    [nutdev1]
        driver = "nutdrv_qx"
        port = "auto"
        vendorid = "0001"
        productid = "0000"
        product = "MEC0003"
        vendor = "MEC"
        bus = "003"

Apro il file di configurazione (dopo aver fatto un backup):

    nano /etc/nut/ups.conf

Vedi i commenti nel file per le impostazioni:
- nome: myUPS
- driver: nutdrv-qx
- il resto è come dall'output dello scanner

Ora configuro ups mon, dopo aver fatto un backup:

    nano /etc/nut/upsmon.conf

Vedo: 

    RUN_AS_USER root
    MONITOR myUPS@localhost 1 upsadmin dsc master

Dati:
- account: upsadmin
- pass: solitacorta
- master perché è fisicamente connesso alla macchina

Ci sono un sacco di altre cose che avevo copiato che non capisco, cancellato ma rimangono nel backup.

Ora modifico un altro file:

    nano /etc/nut/upsd.conf

Un'unica linea:

    LISTEN 0.0.0.0 3493

L'IP 0.0.0.0 serve per "ascoltare" da tutti gli indirizzi IP, non solo dall server (altrimenti sarebbe stato 127.0.0.1).

Modifico:

    nano /etc/nut/nut.conf

Come modalità indico:

    netserver

In modo che possa comunicare lo stato dell'UPS su altre macchine.

Modifico:

    nano /etc/nut/upsd.users

L'utente con cui mi connetto all'ups può essere qualsiasi nome. Imposto:
- nome: [upsadmin]
- password: solitacorta
- "upsadmin master" questo è l'utente che ho impostato prima

Riavvio tutto riavviando la macchina, oppure con questi comandi.

    service nut-server restart
    service nut-client restart
    systemctl restart nut-monitor
    upsdrvctl stop
    upsdrvctl start

Controllo la connessione con:

    upsc myUPS@localhost 

Funziona, ma non vedo la carica della batteria! Sembra che la batteria sia morta.
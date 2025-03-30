# Batteria

## Dettagli UPS

- Brand/Model: Safire UPS600VA-2
- Capacity: 660 VA, 360 W
- Input: 220 Vac, 50/60 Hz, 3.9 A max
- Output: 220 Vac, 50/60 Hz
- Battery: Dc 12 V/7 A Hx1
- Sockets: 2x Schuko (CEE7/4)

## Andamento della carica

L'UPS ha tre livelli di batteria. Dopo aver staccato la corrente, con batteria nuova, la prima tacca dura più di 25 minuti.

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

L'IP 0.0.0.0 serve per "ascoltare" da tutti gli indirizzi IP, non solo dal server (altrimenti sarebbe stato 127.0.0.1).

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

Messaggi ricevuti:

    root@paperaccio:~# upsc myUPS@localhost
    Init SSL without certificate database
    battery.voltage: 13.70
    device.type: ups
    driver.name: nutdrv_qx
    driver.parameter.pollfreq: 30
    driver.parameter.pollinterval: 15
    driver.parameter.port: auto
    driver.parameter.productid: 0000
    driver.parameter.synchronous: auto
    driver.parameter.vendorid: 0001
    driver.version: 2.8.0
    driver.version.data: Q1 0.07
    driver.version.internal: 0.32
    driver.version.usb: libusb-1.0.26 (API: 0x1000109)
    input.frequency: 50.1
    input.voltage: 225.9
    input.voltage.fault: 226.4
    output.voltage: 225.9
    ups.beeper.status: enabled
    ups.delay.shutdown: 30
    ups.delay.start: 180
    ups.load: 3
    ups.productid: 0000
    ups.status: OL      ATTENZIONE A QUESTA LINEA!
    ups.type: offline / line interactive
    ups.vendorid: 0001

Quando a batteria:

    root@paperaccio:~# upsc myUPS@localhost
    Init SSL without certificate database
    battery.voltage: 13.80
    device.type: ups
    driver.name: nutdrv_qx
    driver.parameter.pollfreq: 30
    driver.parameter.pollinterval: 15
    driver.parameter.port: auto
    driver.parameter.productid: 0000
    driver.parameter.synchronous: auto
    driver.parameter.vendorid: 0001
    driver.version: 2.8.0
    driver.version.data: Q1 0.07
    driver.version.internal: 0.32
    driver.version.usb: libusb-1.0.26 (API: 0x1000109)
    input.frequency: 50.1
    input.voltage: 226.4
    input.voltage.fault: 226.4
    output.voltage: 226.4
    ups.beeper.status: enabled
    ups.delay.shutdown: 30
    ups.delay.start: 180
    ups.load: 3
    ups.productid: 0000
    ups.status: OB      ATTENZIONE A QUESTA LINEA!
    ups.type: offline / line interactive
    ups.vendorid: 0001


Posso richiedere lo status dell'UPS con:

    upsc myUPS@localhost ups.status

o semplicemente con:

    upsc myUPS ups.status

Ottengo:

    Init SSL without certificate database
    OL

oppure:

    Init SSL without certificate database
    OB

## Spegnimento automatico
### Fonti
https://unix.stackexchange.com/questions/766747/nut-shutdown-script-does-not-run-when-ups-switches-to-battery-power-returned-1
https://gist.github.com/peterlavelle/5e5adb42f2c8a017ee5879aa8647d1a5
https://community.ipfire.org/t/nut-ups-howto-shut-down-client-after-2-min-on-battery/9096/2 !!!!!

GUIDA UFFICIALE, LEGGI!
https://networkupstools.org/docs/user-manual.chunked/Advanced_usage_scheduling_notes.html

### Script
Modifico upsmon.conf con il comando da usare per lo spegnimento automatico:

    nano /etc/nut/upsmon.conf

e aggiungo:

    SHUTDOWNCMD "/sbin/shutdown -h +0"


File da modificare per gli eventi:

    nano /etc/nut/upssched.conf

Contenuto di /etc/nut/upssched-cmd, ovvero CMDSCRIPT

    #!/bin/sh
    case $1 in          #$1 indica l'argomento che è stato passato allo script!
        onbatt)
            logger -t upssched-cmd "UPS running on battery"
            ;;
        earlyshutdown)
            logger -t upssched-cmd "UPS on battery too long, early shutdown" #fai un log con tag "upssched-cmd" che dice "..."
            /usr/sbin/upsmon -c fsd     #FORCE SHUTDOWN, funziona!
            ;;
        shutdowncritical)
            logger -t upssched-cmd "UPS on battery critical, forced shutdown"
            /usr/sbin/upsmon -c fsd
            ;;
        upsgone)
            logger -t upssched-cmd "UPS has been gone too long, can't reach"
            ;;
        *)
            logger -t upssched-cmd "Unrecognized command: $1"
            ;;
    esac

Per vedere i log di sistema in ordine inverso:

    journalctl -r

Early Shutdowns
One thing that gets requested a lot is early shutdowns in upsmon. With upssched, you can now have this functionality. Just set a timer for some length of time at ONBATT which will invoke a shutdown command if it elapses. Just be sure to cancel this timer if you go back ONLINE before then.

The best way to do this is to use the upsmon callback feature. You can make upsmon set the "forced shutdown" (FSD) flag on the upsd so your secondary systems shut down early too. Just do something like this in your CMDSCRIPT:

/sbin/upsmon -c fsd

Note
the path to upsmon must be provided. The default for an installation built from sources is /usr/local/ups (so /usr/local/ups/sbin/upsmon), while packaged installations will generally comply to FHS — Filesystem Hierarchy Standard (so /sbin/upsmon).

It’s not a good idea to call your system’s shutdown routine directly from the CMDSCRIPT, since there’s no synchronization with the secondary systems hooked to the same UPS. FSD is the primary’s way of saying "we’re shutting down now like it or not, so you’d better get ready".


### Comandi
Spegnimento tra N minuti:

    /sbin/shutdown -h +N

Annulla lo spegnimento programmato:

    shutdown -c
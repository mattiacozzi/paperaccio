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

Lo script funziona se attivato manualmente con i suoi vari parametri, ma non viene attivato automaticamente come invece è indicato in /etc/nut/upssched.conf.

Perché?? Sembra la stessa situazione descritta in https://forum.netgate.com/topic/174526/nut-upssched

Ho sminchiato i gruppi delle cartelle, ho messo /etc/nut con gruppo root, prima era nut



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


LEGGI QUA!!!!!!!!
7.2. The advanced approach, using upssched
upssched is a helper for upsmon that will invoke commands for you at some interval relative to a UPS event. It can be used to send pages, mail out notices about things, or even shut down the box early.

There will be examples scattered throughout. Change them to suit your pathnames, UPS locations, and so forth.

How upssched works relative to upsmon
When an event occurs, upsmon will call whatever you specify as a NOTIFYCMD in your upsmon.conf, if you also enable the EXEC in your NOTIFYFLAGS. In this case, we want upsmon to call upssched as the notifier, since it will be doing all the work for us. So, in the upsmon.conf:

NOTIFYCMD /usr/local/ups/sbin/upssched

ATTENZIONE! Per me sta in /sbin/upssched



Then we want upsmon to actually use it for the notify events, so again in the upsmon.conf we set the flags:

NOTIFYFLAG ONLINE SYSLOG+EXEC
NOTIFYFLAG ONBATT SYSLOG+WALL+EXEC
NOTIFYFLAG LOWBATT SYSLOG+WALL+EXEC
... and so on.
For the purposes of this document I will only use those three, but you can set the flags for any of the valid notify types.

Setting up your upssched.conf
Once upsmon has been configured with the NOTIFYCMD and EXEC flags, you’re ready to deal with the upssched.conf details. In this file, you specify just what will happen when a given event occurs on a particular UPS.

First you need to define the name of the script or program that will handle timers that trigger. This is your CMDSCRIPT, and needs to be above any AT defines. There’s an example provided with the program, so we’ll use that here:

CMDSCRIPT /usr/local/ups/bin/upssched-cmd
Then you have to define the variables PIPEFN and LOCKFN; the former sets the file name of the FIFO that will pass communications between processes to start and stop timers, while the latter sets the file name for a temporary file created by upssched in order to avoid a race condition under some circumstances. Please see the relevant comments in upssched.conf for additional information and advice about these variables.

Now you can tell your CMDSCRIPT what to do when it is called by upsmon.

The big picture
The design in a nutshell is:

upsmon ---> calls upssched ---> calls your CMDSCRIPT
Ultimately, the CMDSCRIPT does the actual useful work, whether that’s initiating an early shutdown with upsmon -c fsd, sending a page by calling sendmail, or opening a subspace channel to V’ger.

Establishing timers
Let’s say that you want to receive a notification when any UPS has been running on battery for 30 seconds. Create a handler that starts a 30 second timer for an ONBATT condition.

AT ONBATT * START-TIMER onbattwarn 30
This means "when any UPS (the *) goes on battery, start a timer called onbattwarn that will trigger in 30 seconds". We’ll come back to the onbattwarn part in a moment. Right now we need to make sure that we don’t trigger that timer if the UPS happens to come back before the time is up. In essence, if it goes back on line, we need to cancel it. So, let’s tell upssched that.

AT ONLINE * CANCEL-TIMER onbattwarn
Note
Timers are pure in-memory mechanisms, specific to upssched. Conversely to other mechanisms found in NUT, such as upsmon→POWERDOWNFLAG, there is no file created on the filesystem.

Executing commands immediately
As an example, consider the scenario where a UPS goes onto battery power. However, the users are not informed until 30 seconds later — using a timer as described above. Whilst this may let the logged in users know that the UPS is on battery power, it does not inform any users subsequently logging in. To enable this we could, at the same time, create a file which is read and displayed to any user trying to login whilst the UPS is on battery power. If the UPS comes back onto utility power within 30 seconds, then we can cancel the timer and remove the file, as described above. However, if the UPS comes back onto utility power say 5 minutes later then we do not want to use any timers but we still want to remove the file. To do this we could use:

AT ONLINE * EXECUTE ups-back-on-power
This means that when upsmon detects that the UPS is back on utility power it will signal upssched. Upssched will see the above command and simply pass ups-back-on-power as an argument directly to CMDSCRIPT. This occurs immediately, there are no timers involved.

Writing the command script handler
OK, now that upssched knows how the timers are supposed to work, let’s give it something to do when one actually triggers. The name of the example timer is onbattwarn, so that’s the argument that will be passed into your CMDSCRIPT when it triggers. This means we need to do some shell script writing to deal with that input.

        #! /bin/sh

        case $1 in
                onbattwarn)
                        # Send a notification mail
                        echo "The UPS has been on battery for awhile" \
                        | mail -s"UPS monitor" bofh@pager.example.com
                        # Create a flag-file on the filesystem, for your own processing
                        /usr/bin/touch /some/path/ups-on-battery
                        ;;
                ups-back-on-power)
                        # Delete the flag-file on the filesystem
                        /bin/rm -f /some/path/ups-on-battery
                        ;;
                *)
                        logger -t upssched-cmd "Unrecognized command: $1"
                        ;;
        esac
This is a very simple script example, but it shows how you can test for the presence of a given trigger. With multiple ATs creating various timer names, you will need to test for each possibility and handle it according to your desires.

Note
You can invoke just about anything from inside the CMDSCRIPT. It doesn’t need to be a shell script, either — that’s just an example. If you want to write a program that will parse argv[1] and deal with the possibilities, that will work too.

Early Shutdowns
One thing that gets requested a lot is early shutdowns in upsmon. With upssched, you can now have this functionality. Just set a timer for some length of time at ONBATT which will invoke a shutdown command if it elapses. Just be sure to cancel this timer if you go back ONLINE before then.

The best way to do this is to use the upsmon callback feature. You can make upsmon set the "forced shutdown" (FSD) flag on the upsd so your secondary systems shut down early too. Just do something like this in your CMDSCRIPT:

/sbin/upsmon -c fsd
Note
the path to upsmon must be provided. The default for an installation built from sources is /usr/local/ups (so /usr/local/ups/sbin/upsmon), while packaged installations will generally comply to FHS — Filesystem Hierarchy Standard (so /sbin/upsmon).

It’s not a good idea to call your system’s shutdown routine directly from the CMDSCRIPT, since there’s no synchronization with the secondary systems hooked to the same UPS. FSD is the primary’s way of saying "we’re shutting down now like it or not, so you’d better get ready".


SISTEMATO!!!!! AGGIUSTARE QUESTO FILE E FARE BACKUP DEI FILE DI CONFIGURAZIONE DI NUT
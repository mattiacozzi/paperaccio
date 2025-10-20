# LAN-server

## Info
- OS: Debian 13 Trixie
- CPU: Intel Atom N455@1.66GHz
- Memory: 2 Gib
- Disk: 128 GB SSD
- IP: `192.168.1.200/24`
- Access Point: NetGear **QUALCOSA** `192.168.1.254`

## SSH
Installare SSH **IMPO!!! aggiungere guida!**

    ssh mattia@192.168.1.200

Se l'utente è lo stesso:

    ssh 192.168.1.200

## Lista di pacchetti utili

    sudo apt install sudo git unzip rsync 

Forse:

    python3
    mysql-common

## Apache

## MariaDB

    mariadb-common

Recupera video (?) per lo script di messa in sicurezza del database

## phpMyAdmin

## HTTPS

## pihole

Installazione di base:

    curl -sSL https://install.pi-hole.net bash

Per installare saltando il controllo di compatibilità del sistema:

    curl -sSL https://install.pi-hole.net | sudo PIHOLE_SKIP_OS_CHECK=true bash

**Problema.** 

Dopo aver installato pihole e apache2, la pagina di login di pihole viene mostrata come index di file perché entrambi usano la porta 80. Bisogna lavorare sulle porte di pihole.

Modifico il file `/etc/pihole/pihole.toml` alla sezione `webserver.port`:

    port = "81o,444os,[::]:81o,[::]:444os"

Alzo tutte le porte di 1. 

> Utile una sessione di debug: `pihole -d`.

L'interfaccia web è ora disponibile a: 

    http://192.168.1.200:81/admin/login

Se non ho segnato la pass di primo accesso:

    sudo pihole setpassword

> Da *Settings / Local DNS Records* posso impostare il rerouting da un indirizzo a mia scelta ad un indirizzo IP o altro.

L'interfaccia web di pihole è in `/var/www/html/admin/`.

## Cockpit

Semplicemente:

    sudo apt install cockpit

Nel dettaglio [qui](https://cockpit-project.org/running#debian).

## Copiare da locale a remoto

    scp -r /home/mattia/Desktop/welcome-page mattia@192.168.1.200:/home/mattia

e poi, sul server:

    sudo mv /home/mattia/welcome-page /var/www/html

rinomina la cartella:

    cd /var/www/html
    sudo mv welcome-page/ home/

## Server FTP 

Usare `vsftpd`. [Video](https://www.youtube.com/watch?v=t50UfbGvWhw)

Installa i pacchetti:

    sudo apt install vsftpd ftp

Controlla se `vsftpd` è in esecuzione:

    systemctl status vsftpd 

Creazione di un utente FTP:

    sudo useradd -m [username]
    sudo passwd [username]

Io non lo faccio perché uso il mio utente `mattia`. Controllo del funzionamento da locale:

    ftp localhost

Config file di default:

    /etc/vsftpd.conf

Faccio un backup:

    sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.backup

Modifico il file originale, se ne ho bisogno:

    sudo nano /etc/vsftpd.conf

Una modifica da fare:

    write_enable=YES

Riavviare il server ftp:

    sudo systemctl restart vsftpd

Per accesso, porta vuota oppure 20, 21.

 Per impostare correttamente i permessi:

    cd /var/www/
    sudo chown -R www-data: html/
    sudo find html -type f -exec chmod 664 {} + -o -type d -exec chmod 775 {} +

assegna i permessi 664 ai file e i permessi 775 alle cartelle in html. capire meglio!

Altre fonti:
- https://wiki.debian.org/vsftpd#User_access_control
- https://security.appspot.com/vsftpd.html
- http://vsftpd.beasts.org/vsftpd_conf.html
- https://askubuntu.com/questions/354178/what-is-ftp-username-and-password-for-vsftpd

## Comandi interessanti

Per vedere gli **ultimi pacchetti installati**:

    ls -tl /var/lib/dpkg/info/*.list

Per vedere gli ultimi 10:

    ls -tl /var/lib/dpkg/info/*.list | head -n 10

[Fonte](https://unix.stackexchange.com/questions/510811/how-to-uninstall-or-remove-recently-installed-packages)





https://stackoverflow.com/questions/49095046/where-is-httpd-conf

## Programmi utili

**Editor di testo: `nano`**

Contenuto di `~/.nanorc`:

    set linenumbers
    set mouse
    set softwrap

Per avere le scorciatoie moderne, in `~/.profile`:

        export EDITOR="nano --modernbindings"

**Gestione file: `nnn`**

Scorciatoie:

- `e` per modificare un file
- `spazio` per selezionare un file
- `p` per copiare *coPy* i file selezionati
- `v` per spostare *moVe* i file selezionati
- `/` per filtrare/type to nav
- `x` per eliminare
- `w` per spostare/copiare cambiando il nome del file
- `d` toggle dettagli
- `.` toggle file nascosti

Opzioni interessanti:
- `n` start in type to nav mode
- `o` open files only on Enter key
- `d` detail mode
- `U` show user and group names in status bar

Per attivarle, in `~/.profile`:

        export NNN_OPTS="nodU"

**Network manager**
- https://networkmanager.dev/docs/api/latest/nmcli.html https://man.archlinux.org/man/nmcli-examples.7.en
- https://docs.oracle.com/it/learn/nmcli_ip_linux8/index.html
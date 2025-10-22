# LAN-server

## Info
- OS: *Debian 13 Trixie*
- CPU: *Intel Atom N455@1.66GHz*
- Memory: *2 GiB*
- Disk: *128 GB SSD*
- IP: `192.168.1.200/24`
- Access Point: *NetGear **QUALCOSA*** `192.168.1.254`

## TODO
- [ ] impostare openEMR
- [ ] scrivere guida https
- [ ] scrivere guida cockpit
- [ ] scrivere guida scp
- [ ] scrivere guida rsync
- [ ] capire la questione dei permessi di /var/www/html/
- [ ] scrivere guida openEMR
- [ ] aggiornare materiali.html
- [ ] aggiornare risorse.html
- [ ] gestione schermo
- [ ] controllare pihole
- [ ] impostare network manager
- [ ] fare un backup di tutto (timeshift?)
- [x] scrivere guida pacchetti utili
- [x] scrivere guida apache
- [x] scrivere guida mariadb
- [x] scrivere guida phpmyadmin

---

## SSH

Fonte: [debian wiki](https://wiki.debian.org/SSH).

Per installare il server:

    apt install openssh-server

Per collegarsi al server:

    ssh mattia@192.168.1.200

Se l'utente è lo stesso basta:

    ssh 192.168.1.200

---

## SUDO

Mi autentico come root e installo `sudo`:

    su -l # per avere tutta la lista dei binari e poter eseguire adduser
    apt install sudo

Aggiungo l'utente normale al gruppo `sudo`:

    adduser mattia sudo

Torno utente normale (`exit` o `CTRL+D`), **logout** per applicare la modifica.

## Pacchetti utili

All'inizio conviene installare alcuni pacchetti, se non già presenti:

    sudo apt install git unzip rsync curl bat

---

## LAMP Stack

Guida video: [LAMP Stack](https://www.youtube.com/watch?v=d7Kkbyb1TjQ).

### Linux

Partendo da una nuova installazione di Debian **con SSH**, aggiorniamo i repository e se necessario aggiorniamo i pacchetti:

    sudo apt update
    sudo apt upgrade

### Apache

Installo `apache2` e le sue dipendenze:

    sudo apt install apache2

Controllo se Apache sta funzionando:

    sudo systemctl status apache2.service

`enabled` mi informa che Apache si attiva al boot. `q` per uscire.

Per controllare che funzioni, posso anche visitare con un browser l'indirizzo IP della macchina (`ip a` per avere tutte le info sulla connessione). Dovrei vedere la pagina di benvenuto di Apache.

Vedi anche come gestire l'[upload di grandi files](#upload-database-precedenti).

#### Firewall

Se Apache funziona, potrebbe essere necessario aprire le porte del firewall `ufw` (**non preinstallato** in Debian 13):

    sudo ufw status

Per aprire le porte per HTTP (80) e HTTPS (443) devo aggiungere delle regole al firewall.

Per vedere i servizi possibili:

    sudo ufw app list

E poi:

    sudo ufw allow Apache # se sono due parole uso le "virgolette così"

Ricontrollo lo status per confermare che Apache può attraversare il firewall.

### MariaDB

MariaDB è un fork completamente open source di MySQL. Vedi qui una [guida video](https://www.youtube.com/watch?v=sfJe5tWtsaA).

Se non è installato (`mysql --version`) installo il server:

    sudo apt install mariadb-server

Per avviare il server:

    sudo systemctl start mysql.service

Controllo dello stato del server:

    sudo systemctl status mysql.service

Per eseguire lo script post installazione:

    sudo mysql_secure_installation

> Il file di configurazione di Apache non si chiama `httpd.conf` in Debian, come è invece ad esempio per XAMPP. Si trova in `/etc/apache2/apache2.conf`. Leggere il file con attenzione.

Digitando `mysql` apro il prompt SQL di MariaDB.

### PHP

Guida [qui](https://computingforgeeks.com/how-to-install-php-8-2-on-debian/).

Installo:

    sudo apt install php php-mysql

Assicurarsi che venga installato anche `libapache2-mod-php8.4` per comunicare con il server web.

Abilito, se già non lo è, il modulo Apache per processare codice PHP:

    sudo a2enmod php8.4

Riavvio il server Apache:

    sudo systemctl restart apache2.service

### phpMyAdmin

Guida [qui](https://iegri.com/installare-phpmyadmin-su-debian/).

Mi posiziono nella cartella che preferisco e scarico phpMyAdmin:

    wget https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.tar.gz

Entro nel *docroot* di Apache e creo la cartella per PMA:

    sudo mkdir /var/www/html/phpmyadmin

Estraggo l'archivio nella cartella creata:

    sudo tar xvf phpMyAdmin-latest-all-languages.tar.gz --strip-components=1 -C /var/www/html/phpmyadmin

Creo il file di configurazione a partire da quello di esempio:

    sudo cp /var/www/html/phpmyadmin/config.sample.inc.php /var/www/html/phpmyadmin/config.inc.php

Modifico il file:

    sudo nano /var/www/html/phpmyadmin/config.inc.php
    
Cerco la stringa:

    $cfg[‘blowfish_secret’] = ''

e inserisco una [password complessa](https://www.motorsportdiesel.com/tools/blowfish-salt/pma/).

Devo fare in modo che la cartella `phpmyadmin` sia di proprietà dell’utente `www-data` (che è l'owner dei servizi httpd) e che il file modificato sia leggibile dall'utente proprietario.

    sudo chown -R www-data:www-data /var/www/html/phpmyadmin
    sudo chmod 660 /var/www/html/phpmyadmin/config.inc.php

> Leggi [qui](https://supporthost.com/it/comando-chown-linux/) e [qui](https://www.andreaminini.com/linux/come-cambiare-i-permessi-su-linux).

Riavvio Apache:

    sudo systemctl restart apache2.service

Controllo se ci sono errori in Apache e apro la pagina:

    http://IP-del-server/phpmyadmin

Se mi dice che manca l'estensione `php-mysqli` risolvo installando il pacchetto `php-mysql`.

> Dei temi sono disponibili [qui](https://www.phpmyadmin.net/themes/). `unzip` l'archivio e lo sposto nella cartella `themes` di phpmyamin.

---

## HTTPS


---

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

---

## Cockpit

Semplicemente:

    sudo apt install cockpit

Nel dettaglio [qui](https://cockpit-project.org/running#debian).

---

## Server FTP 

Usare `vsftpd` ([video](https://www.youtube.com/watch?v=t50UfbGvWhw)).

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

 Per impostare correttamente i **permessi**:

```bash
cd /var/www/
sudo chown -R www-data: html/
sudo find html -type f -exec chmod 664 {} + -o -type d -exec chmod 775 {} +
```
assegna i permessi 664 ai file e i permessi 775 alle cartelle in html. capire meglio! https://www.andreaminini.com/linux/permessi-numerici-su-linux e altre guide sul sito, tipo https://www.andreaminini.com/linux/come-cambiare-i-permessi-su-linux e anche https://supporthost.com/it/comando-chown-linux/

Altre fonti:
- https://wiki.debian.org/vsftpd#User_access_control
- https://security.appspot.com/vsftpd.html
- http://vsftpd.beasts.org/vsftpd_conf.html
- https://askubuntu.com/questions/354178/what-is-ftp-username-and-password-for-vsftpd



https://www.reddit.com/r/linuxquestions/comments/phkwtf/granting_access_for_ftp_user_to_upload_files_to/

Services don't run under the root account for security reasons (e.g.: Because those services don't need to be able to access/modify the whole system) adding your user to the root group completely blasts that security measure.

In addition, if you reaaaallly need to use FTP over SFTP (FTP over SSH), then:

Make sure everything in /var/www/ is owned by the www-data user and group.

Make sure your user an group permissions are correct (e.g.: 775 for directories and 660 for files)

Make sure your FTP service either:

- Runs under the www-data group. (You can modify the service for this)

- The user the FTP server runs as is part of the www-data group.

---

## Copiare file sul server

In alternativa a [FTP](#server-ftp).

### scp

Posso usare `scp`:

    scp -r /home/mattia/Desktop/welcome-page mattia@192.168.1.200:/home/mattia

e poi, sul server:

    sudo mv /home/mattia/welcome-page /var/www/html

rinomina la cartella:

    cd /var/www/html
    sudo mv welcome-page/ home/

### rsync

Uno strumento più potente è `rsync`, tra i [pacchetti raccomandati](#acchetti-utili).

Video:
- [LearnLinuxTV](youtube.com/watch?v=KG78O53u8rY&pp=ygUFcnN5bmPSBwkJBgoBhyohjO8%3D)
- [bread](https://www.youtube.com/watch?v=eifQI5uD6VQ&t=447s)
- [Veronica](https://www.youtube.com/watch?v=QKCIi-NxJEo)

### sftp

La home dell'utente ftp è accessibile anche da un file manager grafico tramite `sftp`:

    sftp://192.168.1.200

---

## Convertire file da markdown a html

Usare `pandoc`:

    sudo apt install pandoc

Comando di esempio per convertire tutti gli md in una cartella in corrispondenti html:

```bash
for i in *.md ; do echo "$i.md --> $i.html " && pandoc -s $i -o $i.html ; done
```

Migliorabile, ad esempio togliendo l'estensione .md nel salvare il file html.

Fonte: [itsfoss.com](https://itsfoss.com/convert-markdown-files/).

---

## Comandi interessanti

### Ultimi pacchetti installati

Per vedere gli ultimi pacchetti installati:

```bash
ls -tl /var/lib/dpkg/info/*.list
```

Per vedere gli ultimi 10:

```bash
ls -tl /var/lib/dpkg/info/*.list | head -n 10
```

[Fonte](https://unix.stackexchange.com/questions/510811/how-to-uninstall-or-remove-recently-installed-packages)


### ...

---

## Programmi utili

**Editor di testo: `nano`**

Contenuto di `~/.nanorc`:

    set linenumbers
    set mouse
    set softwrap

Posso impostare lo stesso anche per l'utente root creando il file in `/root/.nanorc`.

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


## openEMR

Guida all'installazione [qui](https://www.open-emr.org/wiki/index.php/OpenEMR_7.0.0_Linux_Installation).

### Dipendenze

Installazione delle dipendenze ([fonte](https://www.open-emr.org/wiki/index.php/OpenEMR_System_Architecture#OpenEMR_Dependencies)):

    sudo apt-get install apache2 mariadb-server libapache2-mod-php libtiff-tools php php-mysql php-cli php-gd php-xsl php-curl php-soap php-json php-gettext imagemagick php-mbstring php-zip php-ldap

Molte dovrebbero essere già installate. Non trova candidati per `php-gettext`: provo a installare `php-gettext-languages`, non credo sia giusto ma poi l'installazione parte comunque.

Riavvia i server:

    sudo service apache2 restart
    sudo service mariadb restart

### Download e spostamento file

Scarica il file (link diretto estratto da sourceforge):

    wget https://downloads.sourceforge.net/project/openemr/OpenEMR%20Current/7.0.0.2/openemr-7.0.0.tar.gz

Estrai l'archivio:

    tar -pxvzf openemr-7.0.0.tar.gz

Sposta tutto nella root del webserver:

    mv openemr-7.0.0 /var/www/html/openemr

La procedura di installazione via web è ora disponibile su [192.168.1.200/openemr](192.168.1.200/openemr).

Prima di iniziare l'installazione posso caricare il backup di un database già esistente.

        NOTA SUI PERMESSI: la procedura guidata dice 

        Ensuring the /var/www/html/openemr/sites/default/documents directory and its subdirectories have proper permissions...

        /var/www/html/openemr/sites/default/documents directory and its subdirectories are ready.

        quindi queste directories hanno i permessi corretti!!

### Upload database precedenti

Prima di iniziare l'installazione, provo a caricare il vecchio database (+backup) sul server. L'ho salvato come unico file sql, molto grande.

Se necessario, bisogna aumentare il **limite massimo di upload** e post di Apache, che ha effetto sul limite mostrato da phpmyadmin. Modifico `php.ini`:

    sudo nano /etc/php/8.4/apache2/php.ini

Modifico le impostazioni a riga `419`, `699` e `851`:

    max_input_time = 300        # in secondi; def 60
    post_max_size = 200M        # maggiore di upload_max_size; def 8MB
    upload_max_filesize = 100M  # funziona per la dimensione dei miei db; def 2MB

**Riavvio** il server apache:

    sudo systemctl restart apache2

Uso ora la pagina di importazione di phpmyadmin per caricare il file di backup. Ci vuole un po'!

### Installazione

La procedura guidata è raggiungibile da [192.168.1.200/openemr](192.168.1.200/openemr).

**Dati per l'installazione:**

| nome                  | commento                                          | valore          |
|-----------------------|---------------------------------------------------|-----------------|
| server host           | IP del server MySQL                               | `192.168.1.200` |
| database name         | il nome del db in PHPmyAdmin                      | `openemr`       |
| login name            | username per MySQL                                | `openemr`       |
| password              | pass dell'utente sopra                            | `administrator` |
| root account          | utente root di MySQL, crea l'utente sopra e il db | `root`          |
| root password         | vedi tabella utenti in PHPmyAdmin                 | `lan`           |
| user hostname         | IP del server PHP                                 | `192.168.1.200` |
| initial login name    | nome amministratore                               | `administrator` |
| initial user password | password amministratore                           | `administrator` |

1. Al passo 4 dell'installazione viene richiesto di modificare un file di configurazione di Apache:

    ```bash
    sudo cp /opt/lampp/apache2/conf/httpd.conf /opt/lampp/apache2/conf/httpd.conf.backup
    sudo nano /opt/lampp/apache2/conf/httpd.conf
    ```

    Aggiungere queste righe alla fine del file:
    
    ```php
    <Directory "/opt/lampp/htdocs/openemr">
        AllowOverride FileInfo
        Require all granted
    </Directory>
    <Directory "/opt/lampp/htdocs/openemr/sites">
        AllowOverride None
    </Directory>
    <Directory "/opt/lampp/htdocs/openemr/sites/*/documents">
        Require all denied
    </Directory>
    ```
1. Procedere con l'installazione. Al termine riavviare il server Apache.

1. Il gestionale è disponibile all'indirizzo:

        https://localhost/openemr

    oppure:

        [IP-address]/openemr

1. Per importare solo i dati degli utenti da altri database, importo da phpMyAdmin nella tabella `patient_data` il file `patient-dump.sql`.
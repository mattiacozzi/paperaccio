# Comandi utili per Paperaccio
## Dettagli connessione
    ip a

## Modifica dettagli connessione
    nano /etc/network/interfaces

## Modifica dati container
    cd /etc/pve/lxc
    nano 101.conf

## Spegnimento automatico
Timer

    shutdown -P +30

Orario

    shutdown -P 3:00

Annulla lo spegnimento programmato:

    shutdown -c

## Log di sistema
Per mostrare il log in ordine inverso (dai log più recenti ai più vecchi):

    journalctl -r

## Stato UPS
Sintetico:

    upsc myUPS@localhost ups.status

Completo

    upsc myUPS@localhost
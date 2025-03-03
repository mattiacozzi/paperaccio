# Creare una macchina virtuale in PVE

## Guida
- https://www.youtube.com/watch?v=xBUnV2rQ7do

## Scaricare l'immagine
- Andare sul disco (local) del server e scaricare una ISO incollando il link

## Creare la VM
- Creare una macchina virtuale scegliendo la ISO scaricata
- Dare un id sensato alla macchina
- Scegliere la ISO
- In "System", abilitare Qemu agent
- In "Hard disk", abilitare "Discard" se il disco su cui stiamo creando la VM è una SSD. Se è un disco meccanico, non selezionarlo. 
- vmbr0 significa VM Bridge

## Installare il SO
- Avviare la VM
- Installare normalmente
- Attenzione ad installare OpenSSH
- Per Ubuntu server, non serve installare pacchetti aggiuntivi
- Dopo installato, posso rimuovere la ISO dalla VM (scheda "Hardware")
- Impostare, se serve, un IP fisso per la VM

## SSH
- Dopo l'installazione, aprire un terminale in una macchina in rete ed accedere alla VM con SSH
- Comando:

        ssh nomeUtenteVM@VMipAddress
    Esempio:

        ssh mattia@192.168.1.242

- Per uscire da ssh:

        exit
     Oppure CTRL+D

## Aggiornare i pacchetti della VM
- Debian based:
        
        sudo apt update && sudo apt dist-upgrade

## QEMU Guest Agent
- Serve a comunicare lo stato della VM a PVE
- Comando Debian based:

        sudo apt install qemu-guest-agent

- Abilito in guest agent in PVE:
    - Apro la VM in PVE e abilito nelle Opzioni il QEMU Guest Agent
- Avvio il guest agent

        sudo systemctl start qemu-guest-agent.service

- Controllo se sta funzionando

        systemctl status qemu-guest-agent.service


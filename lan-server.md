# Debian LAN-server

## TO DO



## IP lan-server

    192.168.1.200/24


## Comando per copiare da locale a remoto

    scp -r /home/mattia/Desktop/welcome-page mattia@192.168.1.200:/home/mattia

e poi, sul server:

    sudo mv /home/mattia/welcome-page /var/www/html

rinomina la cartella:

    cd /var/www/html
    
    sudo mv welcome-page/ home/

## Impostazione di un server FTP

Usare `vsftpd`:
    - https://wiki.debian.org/vsftpd#User_access_control
    - https://security.appspot.com/vsftpd.html
    - http://vsftpd.beasts.org/vsftpd_conf.html
    - https://askubuntu.com/questions/354178/what-is-ftp-username-and-password-for-vsftpd


## Programmi utili
Gestione file:
    - `yazi`
    - 

Network manager
    - https://networkmanager.dev/docs/api/latest/nmcli.html https://man.archlinux.org/man/nmcli-examples.7.en
    - https://docs.oracle.com/it/learn/nmcli_ip_linux8/index.html
    - 




# Appunti per EndeavourOS+Sway
OS: EndeavourOS
WM: sway    https://www.lorenzobettini.it/2024/10/sway-in-arch-linux-getting-started/

Pacchetti installati

sway swaybg nano sudo
swayidle swaylock
foot wmenu dmenu
rofi-wayland
nemo
alacritty
zram https://www.lorenzobettini.it/2025/06/speed-up-your-linux-system-with-zram/
polkit-kde-agent (authentication agent di kde)
Per eseguire applicazioni X:
xorg-xwayland
xdg-desktop-portal-gtk
xdg-desktop-portal-wlr
featherpad (notepad)
sxiv (image viewer)
brightnessctl wireplumber (per tasti luminosità e volume)
lemurs (login screen)
neovim
swayimg

da AUR con yay:
ne





Creare il file di configurazione di sway:

    mkdir -p ~/.config/sway
    cp /etc/sway/config ~/.config/sway/

Tastiera italiana, modificare in config:

    input type:keyboard {
        xkb_layout it,us
        xkb_options grp:rctrl_toggle
    }

Impostare il lanciatore:

    ​set $menu "rofi -show drun -show-icons"

Impostare il file manager:

    set $filemanager nemo
    ...
    # Start a filemanager 
    bindsym $mod+Shift+Return exec $filemanager

Impostare il terminale:

    set $term alacritty

Impostare il gestore di autenticazione:

    ### Autostart applications 
    exec "/usr/lib/polkit-kde-authentication-agent-1"

Riavvia (non ricarica Sway dopo averlo impostato).

Per impostare la modalità di sospensione, decommentare in config "exec sway idle ecc"

## Impostazione delle notifiche (mako)

    sudo pacman -S inotify-tools mako

https://www.lorenzobettini.it/2024/11/sway-wm-notifications-with-mako/

## Luminosità e volume

Le scorciatoie per luminosità funzionano già. Quelle per il volume sono da impostare così:

    bindsym XF86AudioRaiseVolume exec wpctl set-volume -l 1.0 @DEFAULT_SINK@ 5%+
    bindsym XF86AudioLowerVolume exec wpctl set-volume -l 1.0 @DEFAULT_SINK@ 5%-
    bindsym XF86AudioMute exec wpctl set-mute @DEFAULT_SINK@ toggle

## Login screen

https://www.lorenzobettini.it/2024/12/sway-in-arch-linux-other-configurations/

https://github.com/coastalwhite/lemurs

Abilitare SDDM:

    sudo systemctl enable lemurs.service

Per i temi:

    https://github.com/Guidobelix/archlinux-themes-sddm

## Waybar

https://www.lorenzobettini.it/2024/12/sway-and-waybar/

Font:

    sudo pacman -S otf-font-awesome ttf-arimo-nerd noto-fonts ttf-fira-sans


IMPOSTARE COME AZIONE PER IL CLIC SULLA CONNESSIONE UN NM MANAGER DA LINEA DI COMANDO


### Custom theming

https://www.youtube.com/watch?v=XUdu3xx6iWs

https://www.youtube.com/watch?v=egh43A8Tlh8


## Browser da terminale

Lynx




## Scorciatoie

https://www.reddit.com/r/swaywm/comments/he9imx/what_are_the_keyboard_shortcuts_for_sway/

## CLI File manager (yazi)

https://www.youtube.com/watch?v=RgV47l59NYs

## General config

https://www.youtube.com/watch?v=egh43A8Tlh8


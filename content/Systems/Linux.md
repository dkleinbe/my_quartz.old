---
{"publish":true,"created":"2025.08.03 12:05","modified":"2025.08.13 15:07","tags":["linux","kb"],"cssclasses":""}
---


# Linux

## Man page

- [Command man page](https://www.commandlinux.com/man-page/)

## Curl

```shell
crul -T file_to_uload.jpg-u "login:passwd" ftp://ftpserver.com/
```

## Bash config files

- [[Systems/bash]]

## Commands

### Colored output with `less`

- `less -R`

### Remote copy

```bash
scp /file/to/send username@remote:/where/to/put
# OR
scp username@remote:/file/to/send /where/to/put
```

### Infos system

Install inxi

```bash
sudo apt install inxi
```

Check info

```bash
inxi -SMA
```

Output: 

```
System:  
  Host: debian-papa Kernel: 6.1.0-13-amd64 arch: x86_64 bits: 64  
    Desktop: GNOME v: 43.6 Distro: Debian GNU/Linux 12 (bookworm)  
Machine:  
  Type: Laptop System: ASUSTeK product: X405UA v: 1.0  
    serial: <superuser required>  
  Mobo: ASUSTeK model: X405UA v: 1.0 serial: <superuser required>  
    UEFI: American Megatrends v: X405UA.305 date: 04/16/2019  
Audio:  
  Device-1: Intel Sunrise Point-LP HD Audio driver: snd_hda_intel  
  API: ALSA v: k6.1.0-13-amd64 status: kernel-api  
  Server-1: PipeWire v: 0.3.65 status: active
```

### System logs

- [Systemd : Utiliser journalctl, les logs de systemd - Wiki - Wiki](https://www.linuxtricks.fr/wiki/systemd-utiliser-journalctl-les-logs-de-systemd)
- pour afficher correctement le contenu de /var/log/boot :

```bash
sed $'s/\^\[/\E/g;s/\[1G\[/\[27G\[/' /var/log/boot.log
```

### apt

- list installed packages

```bash
apt list --installed
```

### Services

- [How to List All Running Services Under Systemd in Linux](https://www.tecmint.com/list-all-running-services-under-systemd-in-linux/)
- [Viewing the Status of a Service](https://www.cyberciti.biz/faq/systemd-systemctl-view-status-of-a-service-on-linux/)

### Package install dry run

`sudo apt-get install --dry-run something`

### Package infos

[How to List Installed Packages on Ubuntu in 2023](https://www.hostinger.com/tutorials/how-to-list-installed-packages-on-ubuntu#:~:text=To%20do%20so%20in%20an,to%20count%20the%20installed%20packages.)

## Troubles

### Mise en veille

Pour supprimer la mise en veille lors de la fermeture du portable  
Dans :  

`/etc/systemd/logind.conf.d`  

Créer le fichier :  

`50-HandleLid.conf`  

Ajouter :

```systemd
[Login]
# Ignore Lid close
HandleLidSwitch=ignore

```

refs :

- [logind.conf](https://www.freedesktop.org/software/systemd/man/latest/logind.conf.html)
- [SystemdSuspendSedation - Debian Wiki](https://wiki.debian.org/SystemdSuspendSedation)

### Bluetooth issue

Connection to #bluetooth sound device OK, but device busy, journalctl says:

```
bluetoothd[4378]: src/service.c:btd_service_connect() a2dp-source profile connect failed for 54:B7:E5:9F:5B:33: Device or resource busy
```

Restart bluetooth:

```bash
sudo systemctl restart bluetooth
systemctl --user restart pipew* wirepl*
```

### No sound

#### Origine

found here: [pulseaudio - Pipewire-pulse: No sound, "playback open failed: Device or resource busy" - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/753676/pipewire-pulse-no-sound-playback-open-failed-device-or-resource-busy)  

> [!tip] Disabling home assistant supervised fix the sound issue  
See: [[Home Assistant/home_assistant#Start & Stop]]

#### Fix

Here's a nice workaround for anyone who's interested. If in the host system you are using only alsa, you can instruct HA's pulse to ignore a certain card via udev. Just create a file `/etc/udev/rules.d/89-pulseaudio.rules` with the following content:

```
DRIVERS=="snd_hda_intel", ENV{PULSE_IGNORE}="1"
```

If needed, replace "snd_hda_intel" with the driver of your card, you can find it with

```
udevadm info -a -p `udevadm info -q path -n /dev/snd/controlC0`
```

This workaround doesn't involve any changes in HA, so it should persist updates. HA's pulse will run happily with any remaining cards in the system (or no card at all), and you can still use alsa in the host.

#### PipeWire

PipeWire (multi media framework) [PipeWire - ArchWiki](https://wiki.archlinux.org/title/PipeWire#WirePlumber)  
  -> WirePlumber (session manager) [WirePlumber — WirePlumber 0.4.16 documentation](https://pipewire.pages.freedesktop.org/wireplumber/)  
     -> pipewire-alsa (audio client)  
     -> pipewire-pulse (audio client)

#### Tested

Then, we need to enable new service:

`systemctl --user enable --now wireplumber`

## Increase swap space with regular file

Source: [How to Increase Swap Space in Linux | Baeldung on Linux](https://www.baeldung.com/linux/increase-swap-space)

### Check swap size

```bash
free -h
```

```plaintext
               total        used        free      shared  buff/cache   available
Mem:           4.7Gi       796Mi       3.0Gi        25Mi       884Mi       3.7Gi
Swap:          2.9Gi          0B       2.9Gi
```

### Check Available Disk Space

```bash
df -h
```

```
Filesystem      Size  Used Avail Use% Mounted on
udev            1.9G     0  1.9G   0% /dev
tmpfs           381M  2.5M  378M   1% /run
/dev/sda5        28G   20G  6.3G  77% /
tmpfs           1.9G   72M  1.8G   4% /dev/shm
tmpfs           5.0M  8.0K  5.0M   1% /run/lock
tmpfs           4.0M     0  4.0M   0% /sys/fs/cgroup
/dev/sda7        68G  4.7G   60G   8% /home
/dev/sda1       256M   33M  224M  13% /boot/efi
tmpfs           381M  124K  380M   1% /run/user/1000
```

### Create a File to Use for Swap Space

Next, we create the swap file **using the _fallocate_ command**. Let’s check out the syntax:

```bash
sudo fallocate -l {SIZE_IN_GB}G /{MOUNT_PATH} 
```

The _SIZE_IN_GB_ gets replaced with our desired file size in gigabytes. For instance, let’s create a 1 GB swap file:

```bash
sudo fallocate -l 1G /swapfile_extend_1GB
```

Then, we **secure our swap file by restricting permissions** on it so that only _root_ can access it:

```plaintext
$ ls -l /swapfile_extend_1GB
-rw-r--r-- 1 root root 1073741824 Aug 28 17:21 /swapfile_extend_1GB
```

```bash
sudo chmod 600 /swapfile_extend_1GB
```

```
$ ls -l /swapfile_extend_1GB
-rw------- 1 root root 1073741824 Aug 28 17:21 /swapfile_extend_1GB
```

### Make It a Swap File Using _mkswap_

Now that we’ve created the file we’re going to use for swap and set appropriate permissions on it, we **format the newly created file as swap**:

```bash
sudo mkswap /swapfile_extend_1GB
```

```
Setting up swapspace version 1, size = 1024 MiB (1073737728 bytes)
no label, UUID=9d28d839-2f4f-4173-9bba-576539fce7b6
```

### Preserve the Swap File

The next important step is to preserve the newly created swap file. In other words, we need **to ensure the swap file is available after a reboot**. At this instant, we add an entry to the _/etc/fstab_ file. Then, we open the file in a text editor:

```bash
sudo vi /etc/fstab
```

Once we’re in the text editor, we add the following line at the end of the file:

```plaintext
/swapfile_extend_1GB       none       swap    sw        0       0
```

### Reboot

Done

## Install C/C++ dev env

source: [How to Install GCC C/C++ Compiler on Debian 12](https://linuxhint.com/install-gcc-c-cpp-compiler-debian-12/)

```bash
sudo apt install build-essential
```

## Install qjournalctl 

```bash

# run qjournalctl
qjournalctl
# error message
qjournalctl: error while loading shared libraries: libssh.so.4: cannot open shared object file: No such file or directory
# install libssh-4
sudo apt install libssh-4
```

## Construire un LiveUSB avec persistance des données

source : [Construire un LiveUSB avec persistance des données / Wiki / Debian-facile](https://debian-facile.org/doc:install:deblive-usb-persistant)

Installé Debian Live + package gparted sur la clef Kingston 64 Go

- Debian Live officiel : [https://www.debian.org/CD/live/index.fr.html](https://www.debian.org/CD/live/index.fr.html "https://www.debian.org/CD/live/index.fr.html")
- [GParted -- A free application for graphically managing disk device partitions](https://gparted.org/index.php)

### Copie de l'iso Debian Live

```bash
dd if=ma_debian.iso of=/dev/sdb bs=4M status=progress; sync

```

### Création de la partition persistante

```bash

fdisk --wipe=never /dev/sdb
 
```

```
Commande (m pour l'aide) : n
Sélectionnez (p par défaut) : p
Numéro de partition (3,4, 3 par défaut) : 3
Premier secteur (1302528-7892039, 1302528 par défaut) :
Dernier secteur, +/-secteurs ou +/-taille{K,M,G,T,P} (1302528-7892039, 7892039 par défaut) :

Une nouvelle partition 3 de type « Linux » et de taille 3,1 GiB a été créée.
 
Commande (m pour l'aide) : w
La table de partitions a été altérée.
Appel d'ioctl() pour relire la table de partitions.
Synchronisation des disques.
```

Explications des commandes effectuées :

- commande : n pour “nouvelle partition”
- type de partition : p pour “primaire”
- numéro de partition : ici 3 (les deux premières partitions étant utilisées)
- premier et dernier secteur : par défaut pour occuper toute la place disponible
- commande : w pour “write”, écrire la table de partition

### Formatage de la partition de persistance

```bash
mkfs.ext4 -L persistence /dev/sdb3
```

### Définition du point de montage

```bash
mount /dev/sdb3 /mnt/
echo "/ union" > /mnt/persistence.conf
# demontage
umount /mnt
```

### Utilisation du Live en mode persistant

Votre clé est prête pour la persistance des données. Pour l'utiliser, au moment du menu de démarrage, presser la touche E afin d'éditer la ligne de commande du lancement du live. il faut ajouter “persistence” (en anglais) aux options présentes ainsi :

boot=live components quiet splash persistence

valider et continuer le boot par Ctrl+x ou F10

```ad-warning
loption persistence est volatile, il faudra répéter cette étape à chaque démarrage

```

![[Systems/attachements/Linux.png]]


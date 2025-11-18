# Linux

## Installation
### Ubuntu
- Rufus for USB boot drive
- try/install ubuntu
- set reduce animations in the installation
- enable both drivers and additional utilities (MP4, etc.)
- create EFI (512MB?) + `/` root + swap partitions

### Initial Setup
**Utilities**
```sh
sudo timedatectl set-local-rtc 1
sudo apt update
sudo apt dist-upgrade
sudo apt install build-essential cmake cmake-doc git git-doc net-tools nmap terminator
sudo apt install nano vim vim-doc neovim
```
**NVIDIA CUDA** _(assuming NVIDIA drivers already installed)_
```sh
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt update
sudo init 3
# insert your username + password
sudo apt --purge remove *nvidia*
sudo apt autoremove
sudo apt update
sudo apt dist-upgrade
sudo apt install libvdpau-dev libvdpau-doc vdpau-driver-all vdpauinfo
sudo apt install nvidia-driver-580
sudo apt install cuda-toolkit-13-0
sudo update-initramfs -u
reboot
```

## Setup

### System Clock in Dual Boot

```sh
sudo timedatectl set-local-rtc 1
```

## Tools

### Bluetooth

```sh
# GTK+ Bluetooth Manager
sudo apt install blueman
```

### Calculator

```sh
# GNU (an arbitrary precision calculator language)
bc --interactive
# KCalc (KDE)
kcalc
# Qalculate!
flatpak install flathub io.github.Qalculate               # GTK UI
flatpak install flathub io.github.Qalculate.qalculate-qt  # Qt UI
```

### Clipboard

```sh
# Clipboard cmd line tool for X11
sudo apt install xclip
xclip -sel c < <filepath>
```

### Disk Creation

```sh
# Ubuntu startup disk creation tool for Gtk+
sudo apt install usb-creator-gtk
usb-creator-gtk
```

### Monitor Positioning

```sh
# ARandR: Another XRandR GUI
sudo apt install arandr
arandr  # then, you can export / save your monitor configuration for xrandr
```

### Office

- [LibreOffice](https://pt.libreoffice.org/)
- [WPS](https://www.wps.com/)

### Process Monitoring

```sh
ps aux  # processes snapshot (a: all; u: additional info; x: background procs)
top     # real-time viewof system activity (q to quit)
htop    # more human readable (q to quit)
```

### Screenshot

- [Flameshot](https://flameshot.org/)
- [Kazam](https://launchpad.net/kazam)
- [Shutter](https://shutter-project.org/)

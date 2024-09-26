# Linux

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

### Process Monitoring

```sh
ps aux  # processes snapshot (a: all; u: additional info; x: background procs)
top     # real-time viewof system activity (q to quit)
htop    # more human readable (q to quit)
```

# Linux

Please do not paste and execute the commands, verify all commands and see if it makes sense for your case!

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

# Add the following lines to your ~/.bashrc
# ## NVIDIA DEVELOPMENT ENVIRONMENT
# export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
# export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

sudo apt install libglfw3-dev libglfw3 libglfw3-doc
sudo apt install freeglut3-dev
sudo apt install libopenmpi-dev openmpi-bin openmpi-doc
sudo apt install libfreeimage-dev
mkdir ~/dev/nvidia/ -p
cd ~/dev/nvidia/
git clone https://github.com/NVIDIA/cuda-samples.git
cd cuda-samples
mkdir build && cd build
cmake ..
make -j$(nproc)
ll Samples/

# Then try any samples to verify if all ok with your CUDA installation
```
**Docker**
```sh
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl enable containerd
sudo systemctl start containerd
sudo systemctl status containerd

sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker

sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker

docker run hello-world
```
**NVIDIA Container Toolkit**
```sh
sudo apt-get update && sudo apt-get install -y --no-install-recommends \
    curl \
    gnupg2
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
sudo apt-get update
sudo apt install nvidia-container-toolkit nvidia-container-toolkit-base libnvidia-container-tools libnvidia-container1

nvidia-ctk runtime configure --runtime=docker --config=$HOME/.config/docker/daemon.json
systemctl --user restart docker
sudo nvidia-ctk config --set nvidia-container-cli.no-cgroups --in-place

sudo docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi
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

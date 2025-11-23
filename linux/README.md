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
**Additional Disk**
```sh
$ cat /etc/fstab 
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
/dev/disk/by-uuid/6051c411-63b3-4d75-bfa0-0d41f038d0ba none swap sw 0 0
# / was on /dev/nvme0n1p6 during curtin installation
/dev/disk/by-uuid/5b8c95c8-afaf-4d13-9689-22e72b2352e9 / ext4 defaults 0 1
# /boot/efi was on /dev/nvme0n1p1 during curtin installation
/dev/disk/by-uuid/445E-7656 /boot/efi vfat defaults 0 1

/dev/disk/by-uuid/7cec3379-0266-4b85-bcc8-790c93b90144 /mnt/data ext4 auto,nouser,exec,dev,rw,noatime,nofail 0 2

$ sudo chown ${USER}:${USER} /mnt/data/
```
**Utilities**
```sh
sudo timedatectl set-local-rtc 1
sudo apt update
sudo apt dist-upgrade
sudo apt install build-essential clang-format cmake cmake-doc gimp git git-doc gnuplot htop inkscape net-tools nmap terminator vlc xclip xpad
sudo apt install nano vim vim-doc neovim
sudo apt install doxygen doxygen-doc doxygen-gui
sudo apt install texlive-full
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

# logout and login again possibly...

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

_**>>>>> WARNING: no-cgroups should be false (https://stackoverflow.com/a/78137688)! <<<<<**_
```sh
$ cat /etc/nvidia-container-runtime/config.toml 
#accept-nvidia-visible-devices-as-volume-mounts = false
#accept-nvidia-visible-devices-envvar-when-unprivileged = true
disable-require = false
supported-driver-capabilities = "compat32,compute,display,graphics,ngx,utility,video"
#swarm-resource = "DOCKER_RESOURCE_GPU"

[nvidia-container-cli]
#debug = "/var/log/nvidia-container-toolkit.log"
environment = []
#ldcache = "/etc/ld.so.cache"
ldconfig = "@/sbin/ldconfig.real"
load-kmods = true
no-cgroups = false
#path = "/usr/bin/nvidia-container-cli"
#root = "/run/nvidia/driver"
#user = "root:video"

[nvidia-container-runtime]
#debug = "/var/log/nvidia-container-runtime.log"
log-level = "info"
mode = "auto"
runtimes = ["runc", "crun"]

[nvidia-container-runtime.modes]

[nvidia-container-runtime.modes.cdi]
annotation-prefixes = ["cdi.k8s.io/"]
default-kind = "nvidia.com/gpu"
spec-dirs = ["/etc/cdi", "/var/run/cdi"]

[nvidia-container-runtime.modes.csv]
mount-spec-path = "/etc/nvidia-container-runtime/host-files-for-container.d"

[nvidia-container-runtime.modes.legacy]
cuda-compat-mode = "ldconfig"

[nvidia-container-runtime-hook]
path = "nvidia-container-runtime-hook"
skip-mode-detection = false

[nvidia-ctk]
path = "nvidia-ctk"
```

_**And how to use GUI apps with Docker?**_
```yaml
services:
  ros1noetic:
    image: osrf/ros:noetic-desktop-full
    container_name: ros1noetic
    volumes:
      - /tmp/.X11-unix:/tmp/.X11-unix
      - /dev/dri:/dev/dri
      - ${XDG_RUNTIME_DIR}/${WAYLAND_DISPLAY}:${XDG_RUNTIME_DIR}/${WAYLAND_DISPLAY}
      # - /home/sousarbarb97/datasets:/home/datasets
      - /mnt/data/datasets/:/home/datasets/
      - /home/sousarbarb97/dev/phd/srrg-software/srrg_system:/home/srrg_ws/src/srrg_system/
      - ./:/home/ros_ws/src/inesctec_mrdt_slam_distmap_2d/

      # - ~/.Xauthority:/root/.Xauthority:rw
      # - ${XDG_RUNTIME_DIR}/${WAYLAND_DISPLAY}:/tmp/runtime-user/${WAYLAND_DISPLAY}

    environment:
      - DISPLAY=${DISPLAY}
      - QT_QPA_PLATFORM=xcb
      - QT_X11_NO_MITSHM=1
      - NVIDIA_VISIBLE_DEVICES=all
      - NVIDIA_DRIVER_CAPABILITIES=all

      # - QT_QPA_PLATFORM=wayland
      # - QT_X11_NO_MITSHM=1
      # - XDG_RUNTIME_DIR=/tmp/runtime-user
      # - XDG_SESSION_TYPE=wayland
      # - WAYLAND_DISPLAY=${WAYLAND_DISPLAY}

      # - DISPLAY=${DISPLAY}
      # - WAYLAND_DISPLAY=${WAYLAND_DISPLAY}
      # - QT_QPA_PLATFORM=wayland
      # - QT_X11_NO_MITSHM=1
      # - NVIDIA_VISIBLE_DEVICES=all
      # - NVIDIA_DRIVER_CAPABILITIES=all
      # - XDG_RUNTIME_DIR=${XDG_RUNTIME_DIR}
      # - __GLX_VENDOR_LIBRARY_NAME=nvidia
      # - LIBGL_ALWAYS_INDIRECT=0
      # - LIBGL_ALWAYS_SOFTWARE=0

    tty: true
    devices:
      - /dev/dri:/dev/dri
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]
```

Tried several ways of using native Wayland (uncomment some options above and install inside Docker `sudo apt install qtwayland5 libnvidia-egl-wayland1`), but not successful at least with Rviz (ROS 1 Noetic, Ubuntu 20).

**Visual Studio Code**
```sh
# PlatformIO Requirement
sudo apt install python3-venv
```
```json
{
  "[cpp]": {
    "editor.defaultFormatter": "ms-vscode.cpptools"
  },
  "C_Cpp.clang_format_style": "file",
  "C_Cpp.formatting": "clangFormat",
  "chat.agent.enabled": false,
  "chat.disableAIFeatures": true,
  "cmake.configureOnOpen": false,
  "cSpell.language": "en,pt_PT",
  "editor.formatOnSave": true,
  "editor.renderWhitespace": "none",
  "editor.rulers": [
    80,
    120
  ],
  "editor.tabSize": 2,
  "explorer.confirmDragAndDrop": false,
  "explorer.confirmDelete": false,
  "files.trimTrailingWhitespace": true,
  "files.trimFinalNewlines": true,
  "git.openRepositoryInParentFolders": "always",
  "terminal.integrated.stickyScroll.enabled": false,
  "window.title": "${dirty}${rootName}",
  "workbench.colorTheme": "Night Owl Light (No Italics)"
}
```
- cschlosser.doxdocgen
- ms-azuretools.vscode-docker
- ms-azuretools.vscode-containers
- ms-python.python
- ms-vscode.cpptools-extension-pack
- ms-vscode-remote.remote-containers
- ms-vscode-remote.remote-ssh
- platformio.platformio-ide
- redhat.vscode-xml
- redhat.vscode-yaml
- sdras.night-owl
- streetsidesoftware.code-spell-checker
- streetsidesoftware.code-spell-checker-portuguese
- tomoki1207.pdf

**SSH Keys**
```sh
ssh-keygen -t ed25519 -C "sousa.ricardob@outlook.com" -f ~/.ssh/git
eval $(ssh-agent -s) 
ssh-add ~/.ssh/git
cat ~/.ssh/git.pub | xclip -selection clipboard
# paste the SSH key into your git remote server
ssh -T git@github.com
```
**Sway**
```sh
sudo apt install libnvidia-egl-wayland-dev
sudo apt install brightnessctl blueman grimshot
sudo apt install sway swayidle swaylock
# Waybar dependencies (maybe should n ot be needed, just for compiling from the source...)
sudo apt install         \
  clang-tidy             \
  gobject-introspection  \
  libdbusmenu-gtk3-dev   \
  libevdev-dev           \
  libfmt-dev             \
  libgirepository1.0-dev \
  libgtk-3-dev           \
  libgtkmm-3.0-dev       \
  libinput-dev           \
  libjsoncpp-dev         \
  libmpdclient-dev       \
  libnl-3-dev            \
  libnl-genl-3-dev       \
  libpulse-dev           \
  libsigc++-2.0-dev      \
  libspdlog-dev          \
  libwayland-dev         \
  scdoc                  \
  upower                 \
  libxkbregistry-dev
sudo apt install waybar
sudo apt install wofi
sudo apt install fonts-font-awesome fonts-fork-awesome
sudo apt install python3-i3ipc

sudo groupadd input
sudo groupadd video
sudo usermod -aG input $USER
sudo usermod -aG video $USER

# login and logout?...

sway --unsupported-gpu
```
```sh
$ sudo nano /usr/share/wayland-sessions/sway.desktop
$ cat /usr/share/wayland-sessions/sway.desktop
[Desktop Entry]
Name=Sway
Comment=An i3-compatible Wayland compositor
Exec=sway --unsupported-gpu
Type=Application
DesktopNames=sway

$ sudo nano /etc/environment
$ cat /etc/environment
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"

QT_QPA_PLATFORM="wayland;xcb"
SDL_VIDEODRIVER="wayland,x11"
CLUTTER_BACKEND=wayland

QT_AUTO_SCREEN_SCALE_FACTOR=1
QT_WAYLAND_DISABLE_WINDOWDECORATION=1

GBM_BACKEND=nvidia-drm
__GLX_VENDOR_LIBRARY_NAME=nvidia
LIBVA_DRIVER_NAME=nvidia

WLR_DRM_NO_ATOMIC=1
WLR_NO_HARDWARE_CURSORS=1
WLR_RENDER_NO_EXPLICIT_SYNC=1
WLR_RENDERER=vulkan
```
**OBS Studio**
```sh
sudo add-apt-repository ppa:obsproject/obs-studio
sudo apt update
sudo apt install obs-studio
```
- Check if screen sharing is working (probably not due to wayland...)
```sh
# If screen sharing not working...
sudo apt install xdg-desktop-portal-wlr

# possibly reboot for 'exec dbus-update-activation-environment --systemd WAYLAND_DISPLAY XDG_CURRENT_DESKTOP=sway'
# in config file of sway
```

**Git Repositories**

Possibly, when copying git repositories, it will appear git differences referred to the file modes.
```sh
# Set directories to 755 (executable needed to enter directories)
find latex/ -type d -exec chmod 755 {} \;

# Set files to 644 (non-executable)
find latex/ -type f -exec chmod 644 {} \;
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

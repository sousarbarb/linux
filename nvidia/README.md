# NVIDIA

## Setup

### Display Drivers _(550.107.02)_

**Download**

1. Go to https://www.nvidia.com/en-us/drivers/
2. Manual search for the intended driver:
   - Product Category: _GeForce_
   - Product Series: _GeForce RTX 40 Series (Notebooks)_
   - Product: _GeForce RTX 4060 Laptop GPU_
   - Operating System: _Linux 64-bit_
3. Download Recommended/Certified > View > Download
   (_Linux x64 (AMD64/EM64T) Display Driver 550.107.02 | Linux 64-bit_)
4. (Optional) Search for the _Installation and Configuration Instructions_ of
   the driver in Google (e.g., _NVIDIA-Linux-x86\_64-550.107.02.run README_)
   - See https://download.nvidia.com/XFree86/Linux-x86_64/550.107.02/README/
5. (Optional) Save the `.run` file in a known safe location for you to be able
   to uninstall the drivers and install other versions (TBC)

**Prerequisites**

- Disable dynamic GPU in BIOS (usually, Linux distros only work with the
  discrete GPU setting...)
- Install VDPAU (required for hardware-accelerated video playback)
  ```sh
  sudo apt install libvdpau-dev vdpau-driver-all vdpauinfo
  ```
- Exit the X server and terminate all OpenGL applications
  ```sh
  # Exit the X server + terminate all OpenGL applications (see `$ man runlevel`)
  # (UNSAFE, temporary change)
  runlevel        # N 5 (currently, in runlevel 5, graphical multiuser mode)
  sudo init 3     # change to 3 (no GUI to close X session + OpenGL-based apps)
  # $ sudo init 5 (or previous level) to revert change

  # OR (SAFE, permanent change, to avoid an unboatable OS in the event of the
  # installation being unsuccessful)
  # (https://ubuntu-mate.community/t/how-to-permanently-switch-over-to-runlevel-3-on-20-04-x-lts/23752/2)
  sudo systemctl set-default multi-user.target
  # $ sudo systemctl set-default graphical.target to revert change
  ```
- Disable the Nouveau driver
  ```sh
  sudo su
  cat > /etc/modprobe.d/blacklist-nouveau.conf << EOF
  blacklist nouveau
  options nouveau modeset=0
  EOF
  # paste the previous lines as multi-line command (instead of a single line!)
  ```

**Uninstall previous NVIDIA drivers**

```sh
# (SAFE, I think... >>> use the ORIGINAL run shell script of the NVIDIA drivers)
# (not tested!)
sh ./NVIDIA-Linux-x86_64-550.107.02.run --info              # embedded info
sh ./NVIDIA-Linux-x86_64-550.107.02.run --check             # check sums + md5
sh ./NVIDIA-Linux-x86_64-550.107.02.run --advanced-options  # see advanced opts
sh ./NVIDIA-Linux-x86_64-550.107.02.run --uninstall         # uninstall driver

# (SAFE, but also not tested!)
sudo su
nvidia-installer --uninstall  # probably, exit X server + close all OpenGL apps

# (UNSAFE?... supposedly official from Ubuntu documentation...)
sudo apt --purge remove *nvidia*
sudo apt autoremove

# (UNSAFE, maybe...)
sudo apt autoremove nvidia* --purge
```

**Installation (from NVIDIA official documentation)**

See
https://download.nvidia.com/XFree86/Linux-x86_64/550.107.02/README/installdriver.html.

```sh
# NOTE: do not forget to exit X session and all OpenGL apps (see prerequisites)
cd ~/Downloads

sudo su
sh NVIDIA-Linux-x86_64-550.107.02.run
update-initramfs -u
reboot

# if you set-default multi-user.target, do not forget to switch to graphical
```

**Installation (from Ubuntu official documentation)**

See
https://documentation.ubuntu.com/server/how-to/graphics/install-nvidia-drivers/.

```sh
cat /proc/driver/nvidia/version

sudo add-apt-repository ppa:graphics-drivers/ppa  # Proprietary GPU Drivers PPA
sudo apt update

sudo ubuntu-drivers list
# OR sudo ubuntu-drivers list --gpgpu

sudo ubuntu-drivers install nvidia:560
# OR sudo ubuntu-drivers install (automatic detection, install hw best match)
```

_Note:_ the problem is the version is usually much older than the most stable
version made available by NVIDIA, specially on older versions of Linux
distributions... Additionally, even if the available drivers are updated, this
way may update automatically the drivers even if you do not wanted to...

# docker

## Setup

### Installation

```sh
sudo apt install cpu-checker
sudo apt install gnome-terminal
sudo apt install ca-certificates curl

# General system requirements
modprobe kvm      # load manually kvm for virtualization support (if enabled)
modprobe kvm_amd
kvm-ok            # diagnostics if previous commands fail

lsmod | grep kvm  # check if kvm enabled...
ls -al /dev/kvm   # add user to kvm group to access kvm device
sudo usermod -aG kvm $USER

# Setup Docker apt repository
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker engine
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-compose docker-doc

# Check if Docker was correctly installed
sudo docker run hello-world

# Allow non-privileged users to run Docker commands
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
docker run hello-world

# Configure Docker to start on boot with systemd
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

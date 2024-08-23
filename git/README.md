# git

## Setup

### Installation

```sh
sudo apt install git
```

### SSH keys

```sh
ssh-keygen -t ed25519 -C "<insert comment>" # create SSH key file
eval "$(ssh-agent -s)"                      # check if SSH agent is running
ssh-add ~/.ssh/id_ed25519                   # add SSH private key to SSH agent
xclip -sel c < .ssh/id_ed25519.pub          # copy the public key contents
# now paste it in GitHub or GitLab
ssh -T git@github.com                       # attempt to SSH to GitHub / other
```

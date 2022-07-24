# Shell scripts

## Install virtual box and vagrant

Virtual box now works with WSL though it is still experimental.
Download on oracle website

- Install vagrant

```shell
# add key to list of trusted keys
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/hashicorp.gpg
# add to list of sources
echo "deb https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
sudo apt install vagrant
```

- configure .bashrc (or equivalent)

```shell
export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1"
export PATH="$PATH:/mnt/c/Program Files/Oracle/VirtualBox"
```

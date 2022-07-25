# Shell scripts

## Install virtual box and vagrant

Virtual box now works with WSL though it is still experimental.
Download on oracle website

- Install vagrant on Linux

```shell
# add key to list of trusted keys
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/hashicorp.gpg
# add to list of sources
echo "deb https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
sudo apt install vagrant
```

- Additional steps on WSL:

```shell
# install special wsl2 plugin
vagrant plugin install virtualbox_WSL2
# more infos on https://blog.thenets.org/how-to-run-vagrant-on-wsl-2/
```

- WINDOWS 10: Then go to windows firewall and you should see two boxes VirtualBox Headless Frontend.
  Make sure both are ticked so that it is available on public network

- configure .bashrc (or equivalent)

```shell
export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1"
export PATH="$PATH:/mnt/c/Program Files/Oracle/VirtualBox"
```

- Then, to make sure vagrant work in a given folder, we must set the env var:  
  `` export VAGRANT_WSL_WINDOWS_ACCESS_USER_HOME_PATH=`pwd` ``

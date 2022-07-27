# Useful shell commands and tricks

## Commands

- `type <cmd>` : can check if a command is a shell builtin or gives the path to it
- `man <cmd>`: open manual for command
- `id`: prints user and group info for user

## Tricks

- single vs double quotes: https://stackoverflow.com/questions/6697753/difference-between-single-and-double-quotes-in-bash

```bash
WORD='script'
echo '$WORD' # $WORD
echo "$WORD" # script
echo "This is a $WORD" # This is a script
echo "Alternate syntax ${WORD}" # Alternate syntax script
echo "$WORDing" #nothing: WORDing does not exist
echo "${WORD}ing" #scripting
```

- evaluate expr: `$(expr)` or `` `expr`  ``

# Vagrant and VirtualBox

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

## Vagrant basics

### Vagrant basic commands

Each project comes with a Vagrantfile defining several vm

- `vagrant up <vm_name>`: brings up the vm. If no name is specified, brings up all vms
- `vagrant ssh <vm_name>`: ssh into the vm. If only one vm in the vagrant file, you can omit the name
- `vagrant halt <vm_name>`: stops the vm. If no name is specified, stops all vms. To restart them: `vagrant up`

- `vagrant suspend <vm_name>`: hibernates the vm
- `vagrant suspend <vm_name>`: resumts the vm

- `vagrant destroy <vm_name>`: destroys the vm

### Basic Vagrantfile

```bash
Vagrant.configure(2) do |config| #2 corresponds to configuration version
  config.vm.box = "jsonc/centos7" # name of the vbox
  config.vm.hostname = "linuxsvr1"
  config.vm.network "private_network", ip:"10.2.3.4"
  config.vm.provision "shell", path: "setup.sh" # allows to create a shell script to configure system the first time vagrant up is executed
end
```

# Shell scripts

- This section contains the collection of shell scripts, separated by exercises

## Display UID and username of user executing the script + check if root

Note for if statement

- space after [[and before]]
- variables between quotes
- old syntax uses only 1 [:

```bash
echo "Your UID is ${UID}"
USERNAME = $(id -un)
echo "Your username is ${USERNAME}"

if [[ "${UID}" -eq 0 ]]
then
  echo 'You are root'
else
  echo 'You are not root'
```

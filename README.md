# Linux systems

## Useful commands

- `type <cmd>` : can check if a command is a shell builtin or gives the path to it
- `man <cmd>`: open manual for command
- `whatis`: one line description
- `apropos <keyword>`: finds commands that contain specific keyword

- `file <filepath>`: see file type

- `du`: see size

- `echo $PS1`: see bash prompt config

- `chsh`: change shell (see current with $SHELL var)

- `read`: prompt and retrieve user input. By default uses REPLY var but can specify var
- `pushd` and `popd`: add / retrieves dir from stack

- `uname`: info about the kernel (-r for kernel release)
- `dmesg`: kernel msgs
- `udevadm`: multiple commands for device manager
- `lspci`, `lsblk`, `lscpu`, `lsmem` and `free`, `lshw` : list relevant hardware infos

- `ls -l /sbin/init`: see where it points to check the init system used
- `systemctl get-default`: see default target

- `find /path/to/dir -name filename`: finds a file in a dir (only one - before name)
- `locate` can also be used but you might need to update db: `updatedb`

## Security

- `id`: prints user and group info for user

- see users at `/etc/passwd`, Structure is `USERNAME:PASSWORD:UID:GID:GECOS:HOMEDIR:SHELL`
  Note: password can be shown as `x`, which means it is stored in `/etc/shadow`.
  Note: GECOS contains a csv list of user info (optional)

- passwords are stored in `/etc/shadow` (they are hashed).
  Structure is `USERNAME:HASHED_PASSWORD:LASTCHANGE:MINAGE:MAXAGE:WARN:INACTIVE:EXPDATE`

- see groups at `/etc/group`. Structure is `NAME:PASSWORD:GID:MEMBERS`
  Note: a group password can allow a user to temporarily be granted the permissions of the group

- see sudoers at `/etc/sudoers`
  Note: for sudoers, on the left, user or group, then host (localhost by default) then user group then cmd
  For ex: `%sudo ALL=(ALL:ALL) ALL` (members of sudo can use sudo on any host with any command)

- add user: `useradd <user>` and set psswd `passwd <user>`
- delete user: `userdel`
- add group: `groupadd -g <gid> <name>`
- delete group: `groupdel <name>`

- file permissions (see with `ls -l`): first letter is file type, then you have
  owner permissions, group permissions, permissions for others (r=4, w=2, x=1)
- change permissions : `chmod ugo+r-x <file>`, `chmod u+rgx,g+r-x,o-rwx <file>`, `chmod 755 <file>`
- change ownership:
  change user and group: `chown <user>:<group> <file>`, change only user `chown <user> <file>`
  change group only: `chgrp <group> <file>`

- connect to another machine: `ssh <hostname_or_ip>` or `ssh <user>@<hostname>`
  - generate basic key: `ssh-keygen -t rsa`
  - copy key on server with `ssh-copy-id <user>@<remote-server-name_or-IP>`
  - see authorized public keys on the server in `/home/<user>/.ssh/authorized_keys`
- transfer files with scp: `scp /path/to/file <hostname>:/path/to/file`

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

- check last exit status with `${?}`:

```bash
test "$a" = foo ; echo "$?"
# same as
["$a" = foo ] ; echo "$?"
# double [[ is bash specific so i can use =~
["$a" = foo ]] ; echo "$?"
```

- get nb of args: `${#}`
- get all pos args as one string `${*}` and several `${@}`

## IO redirection

File descriptors: 0: STDIN, 1: STDOUT, 2: STDERR.

- use file as STDIN: `read LINE < ${FILE}` ( < same as 0<)
- redirect STDOUT to a file: `head -n1 /etc/passwd > ${FILE}` (> same as 1>)
- redirect STDERR to a file: `... 2> ${FILE}`
- redirect both to same :`... > ${FILE} 2>&1` or `... &> ${FILE}`
- redirect STDOUT to STDERR: `echo 'error' 1>&2`
- mask STDOUT and STDERR: `... &> /dev/null`

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

## add user (must be executed with root)

```bash
#!/bin/bash

# Creates a new user with a default password.

# User will have to change it on first login

# only root can change password
if [[ ${UID} != 0 ]]; then
	echo "Permission Denied"
	exit 1
fi

read -p "Enter username: " USERNAME
read -p "Enter full name: " FULLNAME
read -p -s "Enter initial password: " PASSWORD

# add the user
useradd -c "${COMMENT}" -m "${USERNAME}"

# returns an error if last command failed
if [[ "${?}" -ne 0 ]]; then
	echo "Problem when creating the user"
	exit 1
fi

# updates the passwd
echo "${USERNAME}:${PASSWORD}" | chpasswd
if [[ "${?}" -ne 0 ]]; then
	echo "Problem when changing the password"
	exit 1
fi

# force passwd change on first login
passwd -e ${USERNAME}

# echo everything
echo "Username: ${USERNAME}"
echo "Password: ${PASSWORD}"
echo "Host: ${HOSTNAME}"

exit 0
```

## add user with random password and command line args

```bash
#!/bin/bash

# Creates a new user.
# first arg is username
# all other args are full name
# password automatically generated

# only root can change password
if [[ ${UID} != 0 ]]; then
	echo "Permission Denied"
	exit 1
fi

if [[ ${#} -lt 2 ]]; then
	echo "You must specify at least two args (username and fullname)" 1>&2
	exit 1
fi

USERNAME=${1}

shift # removes first pos arg

## first solution:
## for loop through all args: problem: extra space at the end
FULLNAME=""
for WORD in "${@}"; do # note: ${@} is optional (default value)
	FULLNAME+="$WORD "
done
echo $FULLNAME

## second solution: while loop through all args
## only add extra space when necessary
FULLNAME=""
while [[ ${#} -gt 0 ]]; do
	FULLNAME+=${1}
	shift
	if [[ ${#} -gt 0 ]]; then
		FULLNAME+=" "
	fi
done
echo $FULLNAME

# third solution: add all remaining args as comments
FULLNAME="${*}"

SPECIAL='!@#$%^&*()_-+='

# gets one ramdom char from SPECIAL
SPECIAL_CHAR=$(echo ${SPECIAL} | fold -w 1 | shuf -n 1)

PASSWORD=$(date +%s%N${RANDOM}${RANDOM} | sha256sum | head -c32)
PASSWORD="${PASSWORD}${SPECIAL_CHAR}"

# # add the user
useradd -c "${FULLNAME}" -m "${USERNAME}" &> /dev/null

# returns an error if last command failed
if [[ "${?}" -ne 0 ]]; then
	echo "Problem when creating the user" >&2
	exit 1
fi

# updates the passwd
echo "${USERNAME}:${PASSWORD}" | chpasswd

# force passwd change on first login
passwd -e ${USERNAME}

# echo everything
echo "Username: ${USERNAME}"
echo "Password: ${PASSWORD}"
echo "Host: ${HOSTNAME}"

exit 0
```

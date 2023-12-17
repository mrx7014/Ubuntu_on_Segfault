<div align="center">
  
# Ubuntu On Segfault

in this repo you will know how to run **Ubuntu** on **Segfault** Server.

</div>

> if you don't know this server check this https://github.com/mrx7014/SSH-KaliLinux
> 
______

# Let's GOOOOOO !

- First open your server and update it

```sh
sudo apt-get update -y ; sudo apt-get upgrade -y
```

- After this you should use `tmate`, because when you use it you will be able to use **Ubuntu** and **Kali** at the same time.

- To use it just type `tmate` in terminal and copy `ssh session` to connect to server by it again.

_______

- This is some tricks to use tmate and create two tabs

- To create a new tab of tmate use this --> **ctrl + b + c**
- To move between tabs use this --> **ctrl + b + "number of tab"**
> you will see a number of tab at bottom in the green line
>
______

- Then enter this command on terminal to install requirements
```sh
IMAGE="ubuntu-22.04-server-cloudimg-amd64.img" ; sudo apt-get install cloud-image-utils libguestfs-tools
```

- Now create .vm/ubuntu directory.
```sh
mkdir -p ~/.vm/ubuntu &>/dev/null
cd ~/.vm/ubuntu
```

- Now create metadata.yaml file.
```sh
cat >metadata.yaml <<-__EOF__
instance-id: iid-${SF_HOSTNAME}01
local-hostname: ${SF_HOSTNAME}-guest
__EOF__
```

- Now Create an SSH key and configure the VM. The host's /sec is shared with the VM:
```sh
[[ ! -f ~/.ssh/id_ed25519 ]] && ssh-keygen -t ed25519 -N "" -f ~/.ssh/id_ed25519 
cat >user-data.yaml <<-__EOF__
#cloud-config
users:
  - name: root
    lock_passwd: false
    hashed_passwd: '$(echo segfault | mkpasswd -m sha-512 -s)'
    ssh_authorized_keys:
      - $(cat ~/.ssh/id_ed25519.pub)
bootcmd:
  - mkdir -p /sec
mounts:
  - [ host0, /sec, 9p ]
__EOF__
cloud-localds seed.img user-data.yaml metadata.yaml
```
- Install & Download cloud images:
```sh
[[ ! -f "${IMAGE}" ]] && wget "https://cloud-images.ubuntu.com/releases/jammy/release/${IMAGE}"
qemu-img resize "${IMAGE:?}" 32G
```
- Now you can start using ubuntu image,but....**Let's do some tricks**

- Enter this command on terminal to make a shortcut with bash script to start a image in an easy way.

```sh
cd ; touch start_ubuntu.sh ; echo "cd .vm/ubuntu ; qemu-system-x86_64 \
    -m 2G \
    -nographic \
    -device virtio-net-pci,netdev=net0 \
    -netdev user,id=net0,hostfwd=tcp::2222-:22 \
    -drive "if=virtio,format=qcow2,file=ubuntu-22.04-server-cloudimg-amd64.img" \
    -drive "if=virtio,format=raw,file=seed.img" \
    -virtfs local,path=/sec,mount_tag=host0,security_model=passthrough" >> start_ubuntu.sh ; chmod +x start_ubuntu.sh
```
```diff
- Note: Tag -m pointing to size of server memorey,if you have 2 GB RAM "at a usual of server keep it,And if you have more than 2GB of RAM change the number "2" to what you want
```

- Now you can start a server by:
```sh
./start_ubuntu.sh
```

- To stop server use `halt`

*it will take sometime at first booting,Don't worry*
_____

- Now after it booting and ask you about user name and password type this:

- Username : `root`
- Password : `segfault`

- Now server is started.

__________

# Now let's update and setup server.

- Now we will update and setup server for everything.

- Just enter this command into the server
```sh
apt update ; apt install sudo ; sudo apt-get update -y ; sudo apt-get full-upgrade -y ; sudo apt install curl
```
________

# Now if you facing any errors with this ubuntu version you can use DistroBox and install Ubuntu with docker via it and use Ubuntu or any other distro.

_________

# First:

- We should install Docker.So,Enter this command in terminal
```sh
sudo apt install docker.io
```

- After this install DistroBox
```sh
curl -s https://raw.githubusercontent.com/89luca89/distrobox/main/install | sudo sh
```
- After installation is completed now learn how to install Ubuntu 23.04
```sh
distrobox create -i ubuntu:23.04
```

- After installation is completed,Enter this command to open Ubuntu
```sh
distrobox enter ubuntu-23-04
```
It takes some time at the first installation.

- After it completed you can use Ubuntu easily.
________

- If you want to use another distro just type `distrobox -i create <distro name:version>`

Check DistroBox repo if you want to know more about it <a href="https://github.com/89luca89/distrobox">Here</a>

_______

- To use /sec/root directory in ubuntu just enter:
```sh
cd /run/host/sec/root
```

- if you wanna make a simple customize to server bash shell use this:
```sh
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
```

- if you want another theme check oh-my-bash repo https://github.com/ohmybash/oh-my-bash

___________
# Contact US
- Linktree: https://linktr.ee/mrx7014
- Donate: https://bmc.link/mrx7014

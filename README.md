# docker-install-on-WSL
Install dockerand docker compose on WSL(Ubuntu) without docker desktop  

Start by removing any old Docker related installations
On Windows: uninstall Docker Desktop
On WSL2: sudo apt remove docker docker-engine docker.io containerd runc

Continue on WSL2 with the following
1. Install pre-required packages
```
sudo apt update
sudo apt install --no-install-recommends apt-transport-https ca-certificates curl gnupg2
```
2. Configure package repository
```
source /etc/os-release
curl -fsSL https://download.docker.com/linux/${ID}/gpg | sudo apt-key add -
echo "deb [arch=amd64] https://download.docker.com/linux/${ID} ${VERSION_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt update
```
3. Install Docker
```
sudo apt install docker-ce docker-ce-cli containerd.io
```
4.Add user to group
```
sudo usermod -aG docker $USER
```
Configure dockerd
5. DOCKER_DIR=/mnt/wsl/shared-docker
```
mkdir -pm o=,ug=rwx "$DOCKER_DIR"
sudo chgrp docker "$DOCKER_DIR"
sudo mkdir /etc/docker
sudo <your_text_editor> /etc/docker/daemon.json

{
   "hosts": ["unix:///mnt/wsl/shared-docker/docker.sock"]
}
```
Note! Debian will also need the additional configuration to the same file
"iptables": false
Now you’re ready to launch dockerd and see if it works
Run command 
```
sudo dockerd
```
If the command ends with “API listen on /mnt/wsl/shared-docker/docker.sock”, things are working
If docker fails to start with an error something like "iptables failed"

Run the following commands for Ubuntu 22.0
```
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```
You can perform an additional test by opening a new terminal and running
```
docker -H unix:///mnt/wsl/shared-docker/docker.sock run --rm hello-world
```
To always run dockerd automatically
Add the following to .bashrc or .profile (make sure “DOCKER_DISTRO” matches your distro, you can check it by running “wsl -l -q” in Powershell)
```
DOCKER_DISTRO="Ubuntu-20.04"
DOCKER_DIR=/mnt/wsl/shared-docker
DOCKER_SOCK="$DOCKER_DIR/docker.sock"
export DOCKER_HOST="unix://$DOCKER_SOCK"
if [ ! -S "$DOCKER_SOCK" ]; then
   mkdir -pm o=,ug=rwx "$DOCKER_DIR"
   sudo chgrp docker "$DOCKER_DIR"
   /mnt/c/Windows/System32/wsl.exe -d $DOCKER_DISTRO sh -c "nohup sudo -b dockerd < /dev/null > $DOCKER_DIR/dockerd.log 2>&1"
fi
```

###Installing Docker compose

1. Run below command to install latest version of dockere compose
```
$  sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

2. Give executable permissions
```
$ sudo chmod +x /usr/local/bin/docker-compose
```

3. Test Installation
```
$ docker-compose --version
```


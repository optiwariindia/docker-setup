# docker-setup
A collection of script required to be executed on new ubuntu machine to run docker
## Update the system
It is highly recomended to update the system to ensure security patches and zero day bug elimination. Following commamnds are useful to perform this action.
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install unattended-upgrades
sudo dpkg-reconfigure unattended-upgrades  # Enable automatic security updates
```
## Create a non root user with sudo permissions
Always use a non root user to login to the server. It is highly recomended to use sudo root user for all administrative activities, using root user is highly unsecure hence is not recomended for general usage. To add ubuntu in linux environment you can go with following commands
```bash
adduser yourusername
usermod -aG sudo yourusername
```
You can also use following:
```bash
useradd yourusername
passwd yourusername
usermod -aG sudo yourusername
```
## Setup passwordless SSH connection
It is recomended to create a passwordless setup with ssh key authentication from one of your own machine. This will help you to manage the server easily and secuely. To setup ssh keys and set passwordless authentication use following commands on your terminal (or powershell / cmd for windows)
```bash
ssh-keygen -t ed25519
ssh-copy-id [yourusername]@[serverip]
```
## SSH Server Security Hardening
SSH server runs on port 22. If you want to harden security change the port to something else by following steps below:
- Open /etc/ssh/sshd_config in an editor of your choice
- Update following lines:
  ```text
  PermitRootLogin no
  PasswordAuthentication no
  Port 2222  # (Optional: Change default SSH port)
  ```
- Now restart ssh server
## Firewall Setup
Add ports that you want to publish into firewall using following commands:
```bash
ufw allow http
ufw allow https
ufw allow 2222/tcp # or any other port used for ssh
ufw enable
```
## Install and enable fail2ban
This package saves from bruitforce attacks. Install and enable it using following:
```bash
apt install fail2ban
systemctl enable fail2ban
```
## Install Docker and Docker compose
Docker is the best way to deploy applications easily and securely hence it is recommended to install all your applications inside docker container. To install docker on your vps use following commands:
```bash
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker yourusername #adding non root user to docker group
```
Now reboot the system and install your applications.

# DelphinusLabs Prover
A step by step guide on running delphinus zkWASM prover and earn credits.

Official Prover Repository

#Requirement

GPU: min. RTX 4090
RAM: >100 GB
Nvidia CUDA, Docker
Local GPU PC Or Cloud GPU with VM ubuntu template
Ubuntu ( Windows users can Install Ubuntu too)

#Rewards

TGE in Q4. Provers based on their performance share 3% of token allocation at TGE.
If the liveness-time is 90%+ and your proving speed keeps stable, then there will be around estimated $100+/month + base * number of proofs you have generated.
From the current estimate of the overall provers, the minimal reward of a certified node is around $200/month. the maximal is $380.


#Notes

The most profittable way is to use Local PC GPUs.

#Installation

Step 1: Delete Preserved Variables
Open /etc/environment:
```
sudo nano /etc/environment
```
Delete everything.

Add this code to it:
```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
```
Step 2: Install & Update Packages
```
sudo apt-get update && sudo apt-get upgrade -y
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev  -y
```
Step 3: Install Docker
```
sudo apt update -y && sudo apt upgrade -y
```
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y && sudo apt upgrade -y

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Test Docker
sudo docker run hello-world
```
Step 4: Install nVIDIA Cuda
```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
            sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
            sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```
```
sudo apt-get update
```
```
sudo apt install nvidia-cuda-toolkit
```
```
sudo apt-get install -y nvidia-container-toolkit
```
```
sudo nvidia-ctk runtime configure --runtime=docker --set-as-default
```
```
sudo apt install nvidia-cuda-toolkit
```
```
sudo rmmod nvidia_drm nvidia_modeset nvidia_uvm nvidia
```
```
sudo systemctl enable docker
sudo systemctl restart docker

OR

sudo service docker restart

```
Verify installation commands:

Nvidia driver: nvidia-smi
Cuda toolkits: nvcc --version
Step 5: Set Up HugePages (memory)
The prover node requires 80000 in RAM in total, and 15000 of it for HugePages (approximately 30 GB of 80 GB memory)

Check memory: ( you need at least 80 GB)
```
free -h
```
Check current HugePages:
```
grep Huge /proc/meminfo
```
Ensure HugePages_Total is at least 15000.

Set HugePages temporarily:
```
sudo sysctl -w vm.nr_hugepages=15000
```
Make HugePages permanently:
Edit /etc/sysctl.conf to add:
```
sudo nano /etc/sysctl.conf
```
Add the line:
```
vm.nr_hugepages=15000
```
To save: Ctrl + X + Y + Enter

Apply configuration:
```
sudo sysctl -p
```
Verify:
```
grep Huge /proc/meminfo
```
Step 6: Build prover
Clone repo:
```
git clone https://github.com/DelphinusLab/prover-node-docker
```
```
cd prover-node-docker
```
Configure prover_config.json:
```
nano prover_config.json
```
Set:

server_url:"https://rpc.zkwasmhub.com:8090"
priv_key: Your EVM private key (without 0x prefix).
Configure dry_run_config.json

nano dry_run_config.json
Set:

server_url:"https://rpc.zkwasmhub.com:8090"
priv_key: Your EVM private key same as above (without 0x prefix).
Build:
```
bash scripts/build_image.sh
```
Step 7: Run Prover
Edit scripts/start.sh file:
```
nano scripts/start.sh
```
Add -d in front of docker compose up command in the bottom of the file:

Run:
```
bash scripts/start.sh
```
View Logs:
```
cd prover-node-docker

docker compose logs -fn 100
```
Optional:
to Stop Node: docker compose down -v or bash scripts/stop.sh
to Restart Node: docker compose up -d
Check all docker containers: docker ps -a
Step 8: Check Prover Node Stats
Search your Node address in https://explorer.zkwasmhub.com/

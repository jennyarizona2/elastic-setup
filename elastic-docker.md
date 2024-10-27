# installing elasticsearch cluster on docker using ubuntu 20.04 image to mimic the requirements of the test document:
- ### Install Docker on the EC2 instance:
* Set up Docker's apt repository.
```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
* Install the Docker packages (latest version).
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
* Verify that the Docker Engine installation is successful by running the hello-world image.
```bash
sudo docker run hello-world
```
* Create a network bridge for the Elasticsearch cluster.
```bash
sudo docker network create elasticsearch
sudo docker network ls
```
* Spin up three Ubuntu 20.04 containers and connect them to the elasticsearch network.
```bash
docker run -d --name elastic1 --network elasticsearch ubuntu:20.04 tail -f /dev/null
docker run -d --name elastic2 --network elasticsearch ubuntu:20.04 tail -f /dev/null
docker run -d --name elastic3 --network elasticsearch ubuntu:20.04 tail -f /dev/null
```
In this setup:

Each container is created with the ubuntu:20.04 image.
The -d flag runs them in detached mode.
The tail -f /dev/null command keeps the containers running.
All containers are connected to the elasticsearch Docker network, allowing them to communicate with each other.
* Install the necessary packages on each container.
```bash
docker exec -it elastic1 bash
```
- upon entering the container, install the necessary packages:
1. Install wget and apt-transport-https.
```bash
apt-get update
apt-get install wget apt-transport-https
```
Download and install the public signing key:
```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
```
Save the repository definition to /etc/apt/sources.list.d/elastic-8.x.list:
```bash
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
```
Install the Elasticsearch Debian package:
```bash
sudo apt-get update && sudo apt-get install elasticsearch
```
- Repeat the same steps for the other two containers.

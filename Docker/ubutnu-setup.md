---
title: Ubuntu Setup
date: 2020-11-18 18:57
---
## Installing Docker
```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

`sudo apt-key fingerprint 0EBFCD88`

`sudo apt install -y docker-compose`

## Adding current user to Docker group

`sudo usermod -aG docker ${USER}`

`su - ${USER}`

`id -nG`


## Sources
* https://www.digitalocean.com/community/tutorials/so-installieren-und-verwenden-sie-docker-auf-ubuntu-18-04-de
* https://docs.docker.com/engine/install/ubuntu/
	
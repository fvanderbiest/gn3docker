# docker + geonetwork 3

This repository contains the [docker](https://www.docker.com/) and [compose](https://docs.docker.com/compose/) configuration files required to build a basic [geonetwork 3](http://geonetwork-opensource.org/) instance.

## pre-requisites

Install a recent Docker version:
```
sudo apt-get remove docker.io
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
sudo sh -c "echo deb https://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
sudo apt-get update
sudo apt-get install lxc-docker
```

Install Compose:
```
sudo -i
curl -L https://github.com/docker/compose/releases/download/1.3.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
exit
```

## build & run

Run the stack with:
```
cd ~/gn3docker && docker-compose up
```

Register the IP of the front server in your host machine with this one-liner:
```
sudo sed -i '/geonetwork.mydomain.org/d' /etc/hosts && \
sudo sh -c "echo `docker inspect --format {{.NetworkSettings.IPAddress}} gn3docker_geonetwork_1` geonetwork.mydomain.org >> /etc/hosts"
```

Open [http://geonetwork.mydomain.org:8080/geonetwork/](http://geonetwork.mydomain.org:8080/geonetwork/) in your browser and voila !  
Login with ```admin``` / ```admin```.

Once you're done with it, you can stop the containers with CTRL + C

## customizing the images

In case you make changes to the Dockerfiles, and before running again ```docker-compose up```, remember you have to:
 - stop the containers with CTRL + C
 - remove the containers with ```docker-compose rm```
 - remove the previous images with eg ```docker rmi -f gn3docker_geonetwork``` (in case you modified geonetwork/Dockerfile)

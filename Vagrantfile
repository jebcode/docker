$setup = <<SCRIPT
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)

cd /var/www/
docker build -t mongo docker/mongo
docker build -t postgresql docker/postgresql
docker build -t web docker/web
docker build -t enketo docker/enketo

DOCKER_DIR=/var/www/docker/

docker run -d -p 27017:27017 -v "$DOCKER_DIR"mongo:/docker --name mongo mongo:latest
docker run -d -p 5432:5432 -v "$DOCKER_DIR"postgresql:/docker --name postgresql postgresql:latest
docker run -d -p 80:80 -v "$DOCKER_DIR"web:/docker -v "$DOCKER_DIR"web/src/siv-v3:/var/www/siv-v3 --link mongo:mongo --link postgresql:postgresql --name web web:latest
docker run -d -p 8006:8005 -p 35729:35729 --name enketo enketo:latest

SCRIPT

$start = <<SCRIPT
docker start mongo
docker start postgresql
docker start web
docker start enketo

SCRIPT

Vagrant.configure("2") do |config|

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    vb.customize ["modifyvm", :id, "--memory", "4096"]
    vb.customize ["modifyvm", :id, "--cpus", "2"]
  end
  
  config.vm.boot_timeout = 900
  
  config.vm.network "private_network", ip: "192.168.50.5"

  config.vm.box = "ubuntu/trusty64"

  config.vm.provision "docker"

  config.vm.synced_folder ".", "/var/www"

  config.vm.provision "shell", inline: $setup

  config.vm.provision "shell", run: "always", inline: $start
end


# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/bionic64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  config.vm.network "forwarded_port", guest: 8080, host: 8080
  config.vm.boot_timeout = 1000

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
     # Display the VirtualBox GUI when booting the machine
     # vb.gui = true
  
     # Customize the amount of memory on the VM:
     # vb.memory = "1024"
   # end
  
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
   config.vm.provision "shell", inline: <<-SHELL
	sudo apt-get -y update
  #Desintalo el servidor web instalado previamente en la unidad 1,
  # a partir de ahora va a estar en un contenedor de Docker.
  if [ -x "$(command -v apache2)" ];then
   sudo apt-get remove --purge apache2 -y
   sudo apt autoremove -y
  fi

  # Directorio para los archivos de la base de datos MySQL. El servidor de la base de datos
  # es instalado mediante una imagen de Docker. Esto está definido en el archivo
  # docker-compose.yml
  if [ ! -d "/var/db/mysql" ]; then
          sudo mkdir -p /var/db/mysql
  fi

  ######## Instalacion de DOCKER ########
  #
  # Esta instalación de docker es para demostrar el aprovisionamiento
  # complejo mediante Vagrant. La herramienta Vagrant por si misma permite
  # un aprovisionamiento de container mediante el archivo Vagrantfile. A fines
  # del ejemplo que se desea mostrar en esta unidad que es la instalación mediante paquetes del
  # software Docker este ejemplo es suficiente, para un uso más avanzado de Vagrant
  # se puede consultar la documentación oficial en https://www.vagrantup.com
  #
  if [ ! -x "$(command -v docker)" ]; then
          sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
        ##Configuramos el repositorio
        curl -fsSL "https://download.docker.com/linux/ubuntu/gpg" > /tmp/docker_gpg
        sudo apt-key add < /tmp/docker_gpg && sudo rm -f /tmp/docker_gpg
        sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
        #Actualizo los paquetes con los nuevos repositorios
        sudo apt-get update -y
        #Instalo docker desde el repositorio oficial
        sudo apt-get install -y docker-ce docker-compose
        #Lo configuro para que inicie en el arranque
        sudo systemctl enable docker
  fi

#    sudo apt-get -y install nodejs
#	sudo curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
#	sudo echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
#    sudo apt update && sudo apt -y install yarn
#	sudo git clone https://github.com/Nicolas-Vega/grupo1-devops-app.git;
#	cd grupo1-devops-app
#	sudo yarn
#	sudo yarn start
   SHELL
end

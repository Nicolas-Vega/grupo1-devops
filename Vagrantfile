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
  config.vm.network "forwarded_port", guest: 8081, host: 8081
  config.vm.network "forwarded_port", guest: 8082, host: 8082
  # Puerto en que escuchar el servidor maestro de Puppet^M
  config.vm.network "forwarded_port", guest: 8140, host: 8140

  ### config.vm.boot_timeout = 1000

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

  # configuración del nombre de maquina
  config.vm.hostname = "grupo1-devops.localhost"
  config.vm.provider "virtualbox" do |v|
        v.name = "grupo1-devops-vagrant-ubuntu"
  end

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  config.vm.synced_folder ".", "/vagrant"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
     # Display the VirtualBox GUI when booting the machine
     # vb.gui = true
  
     # Customize the amount of memory on the VM:
     vb.memory = "1024"
   end
  
  # View the documentation for the provider you are using for more
  # information on available options.

  # Con esta sentencia lo que hara Vagrant es copiar el archivo a la máquina Ubuntu.
  # Además de usarlo como ejemplo para distinguir dos maneras de aprovisionamiento el archivo contiene
  # una definición del firewall de Ubuntu para permitir el tráfico de red que se redirecciona internamente, configuración
  # necesaria para Docker. Luego será copiado al lugar correcto por el script Vagrant.bootstrap.sh
  config.vm.provision "file", source: "hostConfigs/ufw", destination: "/tmp/utw"
  config.vm.provision "file", source: "hostConfigs/etc_hosts.txt", destination: "/tmp/etc_hosts.txt"
  # Archivos de Puppet
  config.vm.provision "file", source: "hostConfigs/puppet/site.pp", destination: "/tmp/site.pp"
  config.vm.provision "file", source: "hostConfigs/puppet/init.pp", destination: "/tmp/init.pp"
  config.vm.provision "file", source: "hostConfigs/puppet/init_jenkins.pp", destination: "/tmp/init_jenkins.pp"
  config.vm.provision "file", source: "hostConfigs/puppet/puppet-master.conf", destination: "/tmp/puppet-master.conf"
  config.vm.provision "file", source: "hostConfigs/puppet/.env", destination: "/tmp/env"
  # Archivo para Jenkins
  config.vm.provision "file", source: "hostConfigs/jenkins/default_jenkins", destination: "/tmp/jenkins_default"
  config.vm.provision "file", source: "hostConfigs/jenkins/init_d", destination: "/tmp/jenkins_init_d"

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
   config.vm.provision "shell", inline: <<-SHELL

#Actualizo los paquetes de la maquina virtual
sudo apt-get update -y

#Aprovisionamiento de software
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common linux-image-extra-virtual-hwe-$(lsb_release -r |awk  '{ print $2 }') linux-image-extra-virtual

# Muevo el archivo de configuración de firewall al lugar correspondiente
if [ -f "/tmp/ufw" ]; then
	sudo mv -f /tmp/ufw /etc/default/ufw
fi

# Muevo el archivo hosts. En este archivo esta asociado el nombre de dominio con una dirección
# ip para que funcione las configuraciones de Puppet
if [ -f "/tmp/etc_hosts.txt" ]; then
	sudo mv -f /tmp/etc_hosts.txt /etc/hosts
fi

###### Instalación de Puppet ######
#configuración de repositorio
if [ ! -x "$(command -v puppet)" ]; then

	#### Instalacion puppet master
  #Directorios
  PUPPET_DIR="/etc/puppet"
  ENVIRONMENT_DIR="${PUPPET_DIR}/code/environments/production"
  PUPPET_MODULES="${ENVIRONMENT_DIR}/modules"

	sudo add-apt-repository "deb http://archive.ubuntu.com/ubuntu $(lsb_release -sc) universe"
 	sudo apt-get update
	sudo apt install -y puppetmaster

	#### Instalacion puppet agent
	sudo apt install -y puppet

  # Esto es necesario en entornos reales para posibilitar la sincronizacion
  # entre master y agents
	sudo timedatectl set-timezone America/Argentina/Buenos_Aires
	sudo apt-get -y install ntp
	sudo systemctl restart ntp

  # Muevo el archivo de configuración de Puppet al lugar correspondiente
  sudo mv -f /tmp/puppet-master.conf $PUPPET_DIR/puppet.conf

  # elimino certificados de que se generan en la instalación.
  # no nos sirven ya que el certificado depende del nombre que se asigne al maestro
  # y en este ejemplo se modifico.
  sudo rm -rf /var/lib/puppet/ssl

  # Agrego el usuario puppet al grupo de sudo, para no necesitar password al reiniciar un servicio
  sudo usermod -a -G sudo,puppet puppet

  # Estructura de directorios para crear el entorno de Puppet
  sudo mkdir -p $ENVIRONMENT_DIR/{manifests,modules,hieradata}
  sudo mkdir -p $PUPPET_MODULES/docker_install/{manifests,files}

  # Estructura de directorios para crear el modulo de Jenkins
  sudo mkdir -p $PUPPET_MODULES/jenkins/{manifests,files}

  # muevo los archivos que utiliza Puppet
  sudo mv -f /tmp/site.pp $ENVIRONMENT_DIR/manifests #/etc/puppet/manifests/
  sudo mv -f /tmp/init.pp $PUPPET_MODULES/docker_install/manifests/init.pp
  sudo mv -f /tmp/env $PUPPET_MODULES/docker_install/files
  sudo mv -f /tmp/init_jenkins.pp $PUPPET_MODULES/jenkins/manifests/init.pp
  sudo mv -f /tmp/jenkins_default $PUPPET_MODULES/jenkins/files/jenkins_default
  sudo mv -f /tmp/jenkins_init_d $PUPPET_MODULES/jenkins/files/jenkins_init_d

  sudo cp /usr/share/doc/puppet/examples/etckeeper-integration/*commit* $PUPPET_DIR
  sudo chmod 755 $PUPPET_DIR/etckeeper-commit-p*
fi

sudo ufw allow 8140/tcp

# al detener e iniciar el servicio se regeneran los certificados
echo "Reiniciando servicios puppetmaster y puppet agent"
sudo systemctl stop puppetmaster && sudo systemctl start puppetmaster
sudo systemctl stop puppet && sudo systemctl start puppet


# limpieza de configuración del dominio utn-devops.localhost es nuestro nodo agente.
# en nuestro caso es la misma máquina
sudo puppet node clean utn-devops.localhost

# Habilito el agente
sudo puppet agent --certname utn-devops.localhost --enable

#Configuracion de puppet
############################################################################
# TODO Esto no lo pude ejecutar acá porque la maquina virtual Ubuntu
# requiere un reboot para poder seguir...no sé bien como hacerlo
# la alternativa es hacer un vagrant ssh y ejecutar los siguientes comandos
# a mano
############################################################################
sudo puppet agent -t --debug
sudo puppet cert sign utn-devops.localhost
#sudo puppet agent -t --debug
SHELL

# trigger reload
config.vm.provision :reload

# execute code after reload
config.vm.provision "shell", inline: <<-SHELL
 sudo puppet agent -t --debug
SHELL

end
   


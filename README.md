# Grupo1 DevOps

## Requisitos:
- Tener VirtualBox 6 y Vagrant instalados

## Iniciar el deploy:

``` vagrant up --provision ```

## Iniciar las app docker:

* vagrant ssh
* cd /vagrant/docker
* sudo docker-compose up -d
* exit
* acceder en el host a http://localhost:8080
* loguearse con admin/admin y acceder a Entities->Clientes para modificar la base

## Borrar la maquina:

``` vagrant destroy ```


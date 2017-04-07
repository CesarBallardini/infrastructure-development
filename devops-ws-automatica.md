# Instalación automática de la estación de desarrollo de infraestructura


Los dos primeros pasos: Git y Ansible, son inevitables de hacer manualmente.


## Instalación manual de Git

Instalamos Git con el gestor de paquetes del sistema operativo:

```bash
  sudo apt-get install git git-gui gitk git-svn
```

Puede en este momento clonar este repositorio para su trabajo posterior:

```bash
  git clone https://github.com/CesarBallardini/infrastructure-development.git
```

Para hacer el clone se necesita acceso a internet.  Configure correctamente el acceso
a internet de su pc local; puede ser necesario salir a internet mediante un proxy.

## Instalación manual de Ansible

Ahora instalamos Ansible, mediante el gestor de paquetes Python `pip`:

```bash
  sudo apt-get install python-pip python-cffi python-dev
  sudo pip install ansible
```

Hoy (6 de abril de 2017) se instaló la versión 2.2.2.0 de Ansible mediante pip. Para comparar, 
en Ubuntu 14.04 las versiones que se pueden instalar mediante el gestor de paquetes APT son:

* 1.7.2+dfsg-1~ubuntu14.04.1 desde trusty-backports/universe
* 1.5.4+dfsg-1 desde trusty/universe

Las versiones empaquetadas por la distribución son muy antiguas.  La serie de 2.2 y 2.3 tienen
muchas mejoras que no podemos permitirnos ignorar.

Pip usará el acceso a internet: recuerde configurar el proxy si eso es necesario en su caso.


## Instalación de Virtualbox


## Instalación de  Vagrant + plugin útiles

## Instalación de  Packer

## Instalación de  KVM, QEMU

## Instalación de  LXD

## Instalación de  libvirt para gestionar KVM y LXD

## Instalación de  Plugin libvirt para Vagrant

## Instalación de  VMware Workstation

## Instalación de  Bibliotecas pysphere y pyvmomi para módulos vSphere de Ansible


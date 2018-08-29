# Instalación manual de la estación de desarrollo de infraestructura


En una primera instancia vamos a instalar en forma manual todas las herramientas, para 
familiarizarnos con su procedimiento de instalación.  El siguiente paso será crear roles
para hacer automática la instalación de la siguiente estación de trabajo para
desarrollo de infraestructura.

## Instalación manual de Git

Instalamos Git con el gestor de paquetes del sistema operativo:

```bash
  sudo apt-get install -y git git-gui gitk git-svn
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
  sudo apt-get install -y python-pip python-cffi python-dev
  sudo pip install --upgrade pip
  sudo pip install --upgrade setuptools
  sudo pip install --upgrade ansible 
```

Hoy (6 de abril de 2017) se instaló la versión 2.2.2.0 de Ansible mediante pip. Para comparar, 
en Ubuntu 14.04 las versiones que se pueden instalar mediante el gestor de paquetes APT son:

* 1.7.2+dfsg-1~ubuntu14.04.1 desde trusty-backports/universe
* 1.5.4+dfsg-1 desde trusty/universe

Las versiones empaquetadas por la distribución son muy antiguas.  La serie de 2.2 y 2.3 tienen
muchas mejoras que no podemos permitirnos ignorar.

Pip usará el acceso a internet: recuerde configurar el proxy si eso es necesario en su caso.


## Instalación del hipervisor

Tenemos cuatro alternativas de hipervisor:

* Virtualbox
* KVM
* VMware
* LXC

El caso de LXC está admitido directamente en el núcleo Linux.  Los otros deben obtenerse por separado.

Cuando utilizamos un hipervisor, éste usa los recursos del sistema operativo mediante unos módulos especiales propios.
No es factible que tengamos habilitados a la vez dos o más hipervisores sin que entren en conflicto.

Debe tenerse esto en cuenta: antes de activar o instalar un hipervisor, asegúrese que los otros hipervisores no están activos.


## Instalación de Virtualbox

Las instrucciones que se siguen son las de https://www.virtualbox.org/wiki/Linux_Downloads

La distro se puede obtener mediante:

```bash
  sudo apt-get install -y  lsb-release
  lsb_release -cs
```

Para buscar las claves del repo de Virtualbox se usará `wget`: instale y configure el proxy para que `wget` pueda salir a internet.

```bash
sudo apt-get install -y wget
```


### Ubuntu 14.04

Si instalamos usando el gestor de paquetes APT, las versiones disponibles son: 
* 4.3.36-dfsg-1+deb8u1ubuntu1.14.04.1 0 trusty-updates/multiverse
* 4.3.10-dfsg-1 0 trusty/multiverse

Cuando la página de descargas muestra que está disponible la versión 5.1. Seguimos las instrucciones allí
descritas para instalar la última versión desde el paquete Oracle oficial:

```bash
sudo apt-get install -y dkms
wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
echo "deb http://download.virtualbox.org/virtualbox/debian "$(lsb_release -cs)" contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
sudo apt-get update
sudo apt-get install -y virtualbox-5.1
```

### Ubuntu 16.04

```bash
sudo apt-get install -y dkms
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
echo "deb http://download.virtualbox.org/virtualbox/debian "$(lsb_release -cs)" contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
sudo apt-get update
sudo apt-get install -y virtualbox-5.1
```

### Debian 8

```bash
sudo apt-get install -y dkms
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
echo "deb http://download.virtualbox.org/virtualbox/debian "$(lsb_release -cs)" contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
sudo apt-get update
sudo apt-get install -y virtualbox-5.1
```


### Oracle VM VirtualBox Extension Pack

En https://www.virtualbox.org/wiki/Downloads encontramos el Extension Pack para todas las plataformas admitidas.  Permite USB 2.0 y USB 3.0 devices, VirtualBox RDP, disk encryption, NVMe and PXE boot for Intel cards. See this chapter from the User Manual for an introduction to this Extension Pack.

Se descarga desde:

```bash
wget http://download.virtualbox.org/virtualbox/5.1.18/Oracle_VM_VirtualBox_Extension_Pack-5.1.18-114002.vbox-extpack
```



## Instalación de  Vagrant + plugin útiles

Las versiones disponibles en Ubuntu 14.04 son muy antiguas:
* 1.4.3-1 trusty/universe

En la página de descargas de Vagrant la última versión actual es 1.9.3 https://www.vagrantup.com/downloads.html

Descargamos:

```bash
  wget https://releases.hashicorp.com/vagrant/1.9.3/vagrant_1.9.3_x86_64.deb
  sudo dpkg -i vagrant_1.9.3_x86_64.deb 

```


La página de plugins https://www.vagrantup.com/docs/plugins/ nos permite elegir los que instalaremos:

* https://www.vagrantup.com/vmware/index.html el plugin de vmware es de pago, no es software libre.

Una lista de los plugins disponibles, sean oficiales o provistos por la comunidad, está en:

* https://github.com/hashicorp/vagrant/wiki/Available-Vagrant-Plugins

Los interesantes son:

* https://github.com/tmatilai/vagrant-proxyconf -- Configures the VM to use proxies
* https://github.com/dotless-de/vagrant-vbguest -- automatically update VirtualBox guest additions if necessary
* https://github.com/smdahlen/vagrant-hostmanager -- manages hosts files within a multi-machine environment
* https://github.com/fgrehm/vagrant-cachier -- caches packages for different managers like apt, yum



## Instalación de  Packer

Packer es un programa que consta de un único binario que se descarga de la página oficial, y se 
descomprime en algún directorio que quede en el PATH de ejecución.

La página de descargas está en: https://www.packer.io/downloads.html

```bash
  wget https://releases.hashicorp.com/packer/1.0.0/packer_1.0.0_linux_amd64.zip
  sudo unzip packer_1.0.0_linux_amd64.zip -d /usr/local/bin/

```


## Instalación de  KVM, QEMU

Nos aseguramos de disponer de una vertsión de CPU / BIOS / Sistema Operativo que sea
de 64 bits y activada la virtualización por hardware:

El siguiente mandato debe indicar `x86_64` para un kernel de 64 bits.
```bash
uname -m
```

Si la salida siguiente es `0`, significa que la virtualización no está activada o no es posible en tu BIOS:

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```

La información relacionada con la arquitectura de la CPU y la instalación del sistema operativo se puede obtener también mediante el 
programa `kvm-ok` que se instala mediante:

```bash
sudo apt-get install -y cpu-checker
```

y vemos el resultado de nuestro equipo con:

```bash
sudo kvm-ok
```

Instalamos los paquetes requeridos:

```bash
sudo apt-get install -y qemu-kvm libvirt-bin bridge-utils virtinst
``` 

Soporte para iPXE en kvm:

```bash
sudo apt-get install -y kvm-ipxe-precise
```

Virtual Distributed Ethernet:

```bash
sudo apt-get install -y vde2 vde2-cryptcab
```

Agregamos la cuenta de usuario al grupo `libvirtd` para gestionar LVM desde una cuenta no privilegiada:

```bash
sudo adduser `id -un` libvirtd
```

Crear una nueva sesión de login para que el cambio en el grupo de nuestra cuenta de usuario tenga efecto y verificar que funciona nuestro acceso mediante:

```bash
virsh -c qemu:///system list
```


Si se desea una interfaz gráfica para la creación y gestión de las máquinas virtuales, se puede instalar:

```bash
sudo apt-get install -y virt-manager virt-viewer qemu-system
```

En el caso de Ubuntu, se puede agregar:

```bash
sudo apt-get install ubuntu-vm-builder
```


Por problemas de bibliotecas en mi Ubuntu 14.04, tuve que cambiarlas mediante:

```bash
sudo rm /usr/lib/libvirt.so.0
sudo ln -s libvirt.so.0.1002.2  /usr/lib/libvirt.so.0

sudo rm /usr/lib/libvirt-lxc.so.0
sudo ln -s /usr/lib/libvirt-lxc.so.0.1002.2 /usr/lib/libvirt-lxc.so.0

sudo rm /usr/lib/libvirt-qemu.so.0
sudo ln -s /usr/lib/libvirt-qemu.so.0.1002.2 /usr/lib/libvirt-qemu.so.0

sudo modprobe kvm_intel
sudo service  libvirt-bin restart
```

Ahora puedo ver la lista de VMs (nula inicialmente) en el hipervisor KVM:

```bash
virsh -c qemu:///system list

```



Como en mi sistema Ubuntu tengo Python 2 y 3 simultáneamente instalados, para correr `virt-manager`
debo hacelo así:

```bash
python2 /usr/share/virt-manager/virt-manager.py

```

Los directorios distinguidos de libvirt son:
* `/var/lib/libvirt/boot/` imágenes ISO para instalaciones
* `/var/lib/libvirt/images/` imágenes de las VMs
* `/etc/libvirt/` configuración


Si deseamos apagar los servicios de kvm, debemos hacer:

```bash
sudo service qemu-kvm stop
sudo rmmod kvm_intel ; sudo rmmod kvm

```


## Instalación de  LXC


```bash
sudo apt-get install -y lxc2 lxctl
sudo apt-get install -y lxc2 lxctl -t trusty-backports # en Ubuntu 14.04
```

Verificamos la configuración:

```bash
sudo lxc-checkconfig
```

Hay plantillas para crear distros en `/usr/share/lxc/templates/`


Configurar el LXD subyacente:

```bash
sudo adduser `id -un` lxd
newgrp lxd
sudo service lxd restart
sudo -i lxd init --auto

# Configuramos el proxy si hace falta para nuestra red:
lxc config set core.proxy_http ${http_proxy}
lxc config set core.proxy_https ${https_proxy}
lxc config set core.proxy_ignore_hosts 10.0.0.0/8,127.0.0.1,*.santafe.gov,ar,*.santafe.gob.ar
```

Fuentes de imágenes: 

```bash
lxc remote list
lxc image list
```

Altero el perfil default para que incorpore la info de proxies:

```bash
lxc profile set default environment.http_proxy  ${http_proxy}
lxc profile set default environment.https_proxy ${https_proxy}
lxc profile set default environment.HTTP_PROXY  ${http_proxy}
lxc profile set default environment.HTTPS_PROXY ${https_proxy}
lxc profile set default environment.no_proxy 127.0.0.1,10.0.0.0/8
lxc profile set default environment.NO_PROXY 127.0.0.1,10.0.0.0/8
```

¿Cómo esta el servicio LXD?

```bash
sudo service lxd status
lxc info
```


## Instalo algunas imágenes para crear contenedores:

```bash

lxc image list images: ubuntu/bionic/amd64
lxc image copy ubuntu:18.04    local: --copy-aliases --auto-update
lxc image copy ubuntu:16.04    local: --copy-aliases --auto-update
lxc image copy ubuntu:14.04    local: --copy-aliases --auto-update


lxc image list images: debian/buster/amd64  # Debian10 Testing
lxc image list images: debian/stretch/amd64 # Debian9  Stable
lxc image copy images:debian/9 local: --copy-aliases --auto-update
lxc image copy images:debian/8 local: --copy-aliases --auto-update
lxc image copy images:debian/7 local: --copy-aliases --auto-update

```

## Configuro el servidor DHCP de dnsmasq

En `/etc/default/lxc-net`

```bash
# This file is auto-generated by lxc.postinst if it does not
# exist.  Customizations will not be overridden.
# Leave USE_LXC_BRIDGE as "true" if you want to use lxcbr0 for your
# containers.  Set to "false" if you'll use virbr0 or another existing
# bridge, or mavlan to your host's NIC.
USE_LXC_BRIDGE="true"

# If you change the LXC_BRIDGE to something other than lxcbr0, then
# you will also need to update your /etc/lxc/default.conf as well as the
# configuration (/var/lib/lxc/<container>/config) for any containers
# already created using the default config to reflect the new bridge
# name.
# If you have the dnsmasq daemon installed, you'll also have to update
# /etc/dnsmasq.d/lxc and restart the system wide dnsmasq daemon.
LXC_BRIDGE="lxcbr0"
LXC_ADDR="192.168.55.1"
LXC_NETMASK="255.255.255.0"
LXC_NETWORK="192.168.55.0/24"
LXC_DHCP_RANGE="192.168.55.51,192.168.55.150"
LXC_DHCP_MAX="100"
# Uncomment the next line if you'd like to use a conf-file for the lxcbr0
# dnsmasq.  For instance, you can use 'dhcp-host=mail1,10.0.3.100' to have
# container 'mail1' always get ip address 10.0.3.100.
#LXC_DHCP_CONFILE=/etc/lxc/dnsmasq.conf

# Uncomment the next line if you want lxcbr0's dnsmasq to resolve the .lxc
# domain.  You can then add "server=/lxc/10.0.3.1' (or your actual $LXC_ADDR)
# to your system dnsmasq configuration file (normally /etc/dnsmasq.conf,
# or /etc/NetworkManager/dnsmasq.d/lxc.conf on systems that use NetworkManager).
# Once these changes are made, restart the lxc-net and network-manager services.
# 'container1.lxc' will then resolve on your host.
#LXC_DOMAIN="lxc"

```


```bash
sudo service lxc-net restart
```



## Verifico que puedo crear contenedores:

```bash
time lxc launch debian/7  prueba # aprox. 11s
lxc list
lxc info  prueba

time lxc stop   prueba --force   # aprox. 1s
lxc list

time lxc start  prueba           # aprox. 1s
lxc list

time lxc delete prueba --force   # aprox. 1s
lxc list
```

Creamos un contenedor a partir de un template:

```bash
sudo lxc-create --template download -n ubuntu-pruebas -- --dist ubuntu --release bionic --arch amd64
sudo lxc-attach -n ubuntu-pruebas -- bash -c "ip addr show eth0 | awk  '/inet /{print $2}'
# FIXME: elegir el rango de direcciones IP que se entregan a los contenedores y configurarlos permitidos en el proxy server
sudo lxc-attach -n ubuntu-pruebas -- bash -c "apt update && apt install -y openssh-server"
sudo lxc-attach -n ubuntu-pruebas -- bash -c "echo 'PermitRootLogin Yes' >> /etc/ssh/sshd_config && service sshd restart "
sudo lxc-attach -n ubuntu-pruebas -- bash -c "echo root:root | chpasswd "
# Ahora se puede hacer SSH al contenedor

sudo lxc-create -n ubuntu-pruebas --template ubuntu
sudo lxc-ls --fancy

sudo lxc-start -n ubuntu-pruebas -d
sudo lxc-ls --fancy

sudo lxc-console -n ubuntu-pruebas
# Ctrl-A Q para salir de esa consola

sudo lxc-info -n ubuntu-pruebasa

sudo lxc-stop -n ubuntu-pruebas
sudo lxc-destroy -n ubuntu-pruebas
```


Referencias
* https://hostpresto.com/community/tutorials/install-and-setup-lxc-on-ubuntu-14-04/
* https://help.ubuntu.com/lts/serverguide/lxc.html


## Instalación de plugin Vagrant para LXC


```bash
vagrant plugin install vagrant-lxc
```

Prueba:

```bash
vagrant init fgrehm/precise64-lxc
vagrant up --provider=lxc

sudo lxc-ls --fancy

vagrant ssh
vagrant halt
vagrant destroy -f

```

Referencias

* https://github.com/fgrehm/vagrant-lxc
* https://github.com/hsoft/vagrant-lxc-base-boxes Cómo construir tus propios boxes





## Instalación de plugin para gestionar libvirt y KVM:

```bash
sudo apt-get build-dep ruby-libvirt
sudo apt-get install -y libxslt-dev libxml2-dev libvirt-dev zlib1g-dev ruby-dev
vagrant plugin install vagrant-libvirt
```

Lo verificamos con:

```bash
# la imagen debian/stretch64 requiere un servidor NFS en el host:
sudo apt-get install nfs-kernel-server

cd /tmp
vagrant init --minimal debian/stretch64
vagrant up --provider=libvirt
```

## Instalación de  VMware Workstation

desde http://www.vmware.com/products/workstation/workstation-evaluation.html
elegimos:

VMware Workstation Pro Trial Version


Descargo el archivo: https://download3.vmware.com/software/wkst/file/VMware-Workstation-Full-12.5.5-5234757.x86_64.bundle

http://pubs.vmware.com/workstation-12/index.jsp#com.vmware.ws.using.doc/GUID-1F5B1F14-A586-4A56-83FA-2E7D8333D5CA.html instrucciones de instalación

http://pubs.vmware.com/workstation-12/index.jsp#com.vmware.ws.using.doc/GUID-42F4754B-7547-4A4D-AC08-353D321A051B.html opciones de línea de mandatos para la instalación

instalamos mediante el mandato:

```bash
sudo bash VMware-Workstation-Full-12.5.5-5234757.x86_64.bundle \
              --console \
              --required \
              --set-setting vmware-workstation-app softwareUpdateEnabled no \
              --set-setting vmware-player-app softwareUpdateEnabled no 
```

Para ver la lista de, hacemos:

```bash
sudo bash VMware-Workstation-Full-12.5.5-5234757.x86_64.bundle -t
```

Lo cual muestra:

```
Extracting VMware Installer...done.
Component Name                 Component Long Name                               Component Version   
============================== ================================================= ====================
vmware-installer               VMware Installer                                  2.1.0.5087745       
vmware-player-setup            VMware Player Setup                               12.5.5.5234757      
vmware-vmx                     VMware VMX                                        12.5.5.5234757      
vmware-vix-core                VMware VIX Core Library                           1.15.7.5234757      
vmware-network-editor          VMware Network Editor                             12.5.5.5234757      
vmware-network-editor-ui       VMware Network Editor User Interface              12.5.5.5234757      
vmware-tools-netware           VMware Tools for NetWare                          10.1.6.5234757      
vmware-tools-linuxPreGlibc25   VMware Tools for legacy Linux                     10.1.6.5234757      
vmware-tools-winPreVista       VMware Tools for Windows 2000, XP and Server 2003 10.1.6.5234757      
vmware-tools-winPre2k          VMware Tools for Windows 95, 95, Me and NT        10.1.6.5234757      
vmware-tools-freebsd           VMware Tools for FreeBSD                          10.1.6.5234757      
vmware-tools-windows           VMware Tools for Windows Vista or later           10.1.6.5234757      
vmware-tools-solaris           VMware Tools for Solaris                          10.1.6.5234757      
vmware-tools-linux             VMware Tools for Linux                            10.1.6.5234757      
vmware-usbarbitrator           VMware USB Arbitrator                             15.2.0.5234757      
vmware-player-app              VMware Player Application                         12.5.5.5234757      
vmware-workstation-server      VMware Workstation Server                         12.5.5.5234757      
vmware-ovftool                 VMware OVF Tool component for Linux               4.1.0.3634792       
vmware-vprobe                  VMware VProbes component for Linux                12.5.5.5234757      
vmware-vix-lib-Workstation1200 VMware VIX Workstation-12.0.0 Library             1.15.7.5234757      
vmware-workstation             VMware Workstation                                12.5.5.5234757      
```


Podemos eliminar componentes que no nos interesan con:

```bash
sudo bash VMware-Workstation-Full-12.5.5-5234757.x86_64.bundle --uninstall-component=NAME
```

Para ver la lista de productos instalados, hacemos:

```bash
sudo bash VMware-Workstation-Full-12.5.5-5234757.x86_64.bundle -l
```

```
Extracting VMware Installer...done.
Product Name         Product Version     
==================== ====================
vmware-workstation   12.5.5.5234757      
```


Para eliminar completamente el producto, hacer:

```bash
sudo bash VMware-Workstation-Full-12.5.5-5234757.x86_64.bundle --uninstall-product=vmware-workstation
```

Para detener la ejecución de la virtualización VMware, hacer:

```bash
# detenemos los servicios VMware:
sudo service vmware stop ; sudo service vmware-USBArbitrator stop ;   sudo service  vmware-workstation-server stop

```


## Instalación de  Bibliotecas pysphere y pyvmomi para módulos vSphere de Ansible

VMware vSphere API Python Bindings 
pyVmomi is the Python SDK for the VMware vSphere API that allows you to manage ESX, ESXi, and vCenter.
https://github.com/vmware/pyvmomi
The official release is available using pip, just run 

```bash
pip install --upgrade pyvmomi.
```

Virtualization Management Object Management Infrastructure (VMOMI)



https://github.com/argos83/pysphere

```bash
pip install --upgrade pysphere
```


FIXME


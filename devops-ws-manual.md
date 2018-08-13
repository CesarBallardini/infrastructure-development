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
  sudo pip install --upgrade ansible 
```

Hoy (6 de abril de 2017) se instaló la versión 2.2.2.0 de Ansible mediante pip. Para comparar, 
en Ubuntu 14.04 las versiones que se pueden instalar mediante el gestor de paquetes APT son:

* 1.7.2+dfsg-1~ubuntu14.04.1 desde trusty-backports/universe
* 1.5.4+dfsg-1 desde trusty/universe

Las versiones empaquetadas por la distribución son muy antiguas.  La serie de 2.2 y 2.3 tienen
muchas mejoras que no podemos permitirnos ignorar.

Pip usará el acceso a internet: recuerde configurar el proxy si eso es necesario en su caso.


## Instalación de Virtualbox

Las instrucciones que se siguen son las de https://www.virtualbox.org/wiki/Linux_Downloads

La distro se puede obtener mediante:

```bash
  sudo apt-get install -y  lsb-release
  lsb_release -cs
```

Para buscar las claves del repo de Virtualbox se usará `wget`: configure el proxy para que `wget` pueda salir a internet.

### Ubuntu 14.04

Si instalamos usando el gestor de paquetes APT, las versiones disponibles son: 
* 4.3.36-dfsg-1+deb8u1ubuntu1.14.04.1 0 trusty-updates/multiverse
* 4.3.10-dfsg-1 0 trusty/multiverse

Cuando la página de descargas muestra que está disponible la versión 5.1. Seguimos las instrucciones allí
descritas para instalar la última versión desde el paquete Oracle oficial:

sudo apt-get install -y dkms
wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
echo "deb http://download.virtualbox.org/virtualbox/debian "$(lsb_release -cs)" contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
sudo apt-get update
sudo apt-get install -y virtualbox-5.1

### Ubuntu 16.04

sudo apt-get install -y dkms
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
echo "deb http://download.virtualbox.org/virtualbox/debian "$(lsb_release -cs)" contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
sudo apt-get update
sudo apt-get install -y virtualbox-5.1


### Debian 8

sudo apt-get install -y dkms
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
echo "deb http://download.virtualbox.org/virtualbox/debian "$(lsb_release -cs)" contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
sudo apt-get update
sudo apt-get install -y virtualbox-5.1

### Oracle VM VirtualBox Extension Pack

En https://www.virtualbox.org/wiki/Downloads encontramos el Extension Pack para todas las plataformas admitidas.  Permite USB 2.0 y USB 3.0 devices, VirtualBox RDP, disk encryption, NVMe and PXE boot for Intel cards. See this chapter from the User Manual for an introduction to this Extension Pack.

Se descarga desde:
wget http://download.virtualbox.org/virtualbox/5.1.18/Oracle_VM_VirtualBox_Extension_Pack-5.1.18-114002.vbox-extpack

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

Instalamos los paquetes requeridos:

```bash
sudo apt-get install -y qemu-kvm libvirt-bin bridge-utils
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

## Instalación de  LXD

La instalación desde Ubuntu 14.04 en adelante se hace mediante `snap`, así que lo instalamos primero:

```bash
sudo apt install -y snapd  
```

Y luego instalamos LXD

```bash
sudo snap install lxd  
```


Configuración inicial:

```bash
sudo adduser `id -un` lxd
sudo lxd init
```

Instalo algunas imágenes para crear contenedores:

```bash
lxc image copy ubuntu:18.04    local: --copy-aliases --auto-update
lxc image copy ubuntu:16.04    local: --copy-aliases --auto-update
lxc image copy ubuntu:14.04    local: --copy-aliases --auto-update
lxc image copy images:debian/9 local: --copy-aliases --auto-update


```

Referencias
  * https://stgraber.org/2016/03/30/lxd-2-0-image-management-512/
  * https://lxd.readthedocs.io/en/latest/image-handling/

## Instalación de plugin libvirt y LXD para Vagrant

```bash
vagrant plugin install vagrant-lxd
```

Verificamos que funciona con:

```bash
cd /tmp
vagrant init --minimal debian/stretch64
vagrant up --provider=lxd
```

FIXME: esto da un error de conexion al LXD

Ahora instalamos el plugin para gestionar libvirt y KVM:

```bash
sudo apt-get build-dep ruby-libvirt
sudo apt-get install -y libxslt-dev libxml2-dev libvirt-dev zlib1g-dev ruby-dev
vagrant plugin install vagrant-libvirt
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


FIXME

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


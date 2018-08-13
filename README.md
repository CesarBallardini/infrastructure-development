# Infrastructure Development


## Infraestructura

La infraestructura es el conjunto de equipos físicos y virtuales que dan servicio
a aplicaciones corporativas, en general aplicaciones Web.

Los servicios de DNS corporativo, email, chat, son servicios visibles de la organización que forman 
parte de la infraestructura.  Otros servicios no tan visibles, pero imprescindibles para asistencia
a las aplicaciones corporativas son: monitoreo de servicios y equipos; gestión integrada de bitácora
(logs); integración continua de cambios en código fuente; repositorios de código fuente y de artefactos binarios;
proxy y cache de acceso a Web; instalación desatendida de equipos físicos; clonación de una instalación
en múltiples equipos; respaldos de datos; etc.

Los servicios corporativos pueden clasificarse también de acuerdo al nivel dentro de la pila de servicios,
de acuerdo a sus dependencias: un servicio A que depende para funcionar de otro B, indica que A está
debajo de B en la pila.  Sin ser exhaustivos, y si listamos servicios con los elementos de la pila
en orden de dependencias serán:

* aplicaciones corporativas
  * Web de negocios
  * mensajería instantánea
  * gestión de proyectos
  * intranet, etc.
* aplicaciones para gestión de infraestructura 
  * gestión de incidencias, documentación de infraestructura
  * gestión de cuentas de correo, etc.
* integración continua
* repositorio de artefactos binarios
* monitoreo de servicios y equipos, alertas y paneles de visualización
* gestión de configuraciones centralizado (Ansible)
* mensajería instantánea
* repositorio de código fuente
* correo electrónico
* gestión de identificaciones (LDAP)
* acceso a Internet (proxy y caché Web)
* servicio de hora (NTP)
* servicio de resolución de nombres y direcciones IP (DNS)
* hipervisores, nodos físicos

Hay dos situaciones de inicio de infraestructura: una se da cuando no existe la infraestructura
previamente y otra cuando se desea recrear la infraestructura.  Ejemplo del primer caso es cuando
se comienza un emprendimiento, o cuando se reemplaza la vieja infra por una basada en una tecnología
diferente.  El caso de recrear la infra se da cuando se agrega una nueva sede que requiere su propio
centro de datos, o cuando la anterior infra queda fuera de servicio por razones de fuerza mayor.

Vamos a concentrarnos en el problema de crear por primera vez la infraestructura.

## Cómo iniciar el desarrollo de infraestructura

El desarrollo del código fuente se hace en una pc local.  La mayoría de las herramientas que usaremos
se han desarrollado primeramente sobre sistemas GNU/Linux.  Instala un GNU/Linux en tu pc.
¿Cuál distribución instalar? La que te guste.  Yo hoy tengo Ubuntu 14.04.  La que quieras; adapta
las instrucciones correspondientes a la pc de desarrollo con la distro que elijas.

Vamos a usar Ansible para la gestión de configuraciones, y el código fuente que escribamos lo
guardaremos en un repositorio Git.  Estas dos herramientas se instalarán manualmente, como primer paso.

Luego vamos a elegir o construir roles y playbooks Ansible para instalar el resto de programas que vamos a utilizar:
* Virtualbox
* Vagrant + plugin útiles
* Packer
* KVM, QEMU
* LXD
* libvirt para gestionar KVM y LXD
* Plugin libvirt para Vagrant
* VMware Workstation
* Bibliotecas pysphere y pyvmomi para módulos vSphere de Ansible

En una primera instancia vamos a instalar en forma manual todas las herramientas, para 
familiarizarnos con su procedimiento de instalación.  El siguiente paso será crear roles
para hacer automática la instalación de la siguiente estación de trabajo para
desarrollo de infraestructura.


* [Instalación manual de la estación de desarrollo de infraestructura](devops-ws-manual.md)
* [Instalación automática de la estación de desarrollo de infraestructura](devops-ws-automatica.md)

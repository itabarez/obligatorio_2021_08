# obligatorio_2021_08
 
Tabla de contenido
1.	DECLARACIÓN AUTORÍA	2
2.	RESUMEN EJECUTIVO	2
2.1.	Introducción	2
2.2.	Objetivos Específicos	2
3.	SOLUCIÓN PROYECTO	2
3.1.	Instalación CentOS y Ubuntu	2
3.2.	Instalar ansible	4
3.3.	Configurar Claves	4
3.4.	Instalar Git	5
3.5.	Correcciones de errores archivos	7
3.6.	Ejecución ansible	9
3.7.	Roles Ansible	11
4.	REFERENCIAS	12


 
SOLUCIÓN PROYECTO TALLER DE SERVIDORES LINUX
1.	DECLARACIÓN AUTORÍA
Victoria Gallardo, Ignacio Tabárez y Luciano Villa, con número de estudiante 258562 - 173307 - 240976, y estudiantes de la carrera Analista en Infraestructura Informática de la Escuela de Tecnologías de la Universidad ORT, en relación con el trabajo final presentado para su defensa y evaluación de la materia “Taller de Servidores Linux”, declaráramos que asumimos la originalidad de dicho trabajo, entendida en el sentido de que no se ha utilizado fuentes sin citarlas debidamente.
Montevideo, 10 de agosto de 2021
Firma: Victoria Gallardo, Ignacio Tabárez y Luciano Villa
2.	RESUMEN EJECUTIVO
2.1.	Introducción
El presente documento tiene como objetivo presentar una solución para el proyecto de Taller de Servidores Linux en el marco de un trabajo obligatorio final. Este proyecto tiene como fin principal brindar una solución que automatice procesos de TI y/o infraestructura.
2.2.	Objetivos Específicos
El objetivo del taller es poder crear procesos que automaticen las tareas que pueden ser repetitivas. Alcanzar la mayoría de las tareas automatizadas nos permite administrar mejor una infraestructura y utilizar u optimizar de buena manera el tiempo de trabajo.
3.	SOLUCIÓN PROYECTO
A continuación, se brindan los detalles de las modificaciones y configuración de las diferentes tareas realizadas.
3.1.	Instalación CentOS y Ubuntu
En dicha solución se cuentan con 3 máquinas virtuales, 2 Ubuntu 20.04 y 1 CentOS 8.
Características Ubuntu:
•	1 GB RAM
•	15 GB Disco Duro
Particiones:
•	1 GB /boot
•	5 GB /
•	4 GB /var
•	3 GB /home
•	2 GB /swap

En la siguiente imagen se puede apreciar como quedaron las particiones realizadas en el equipo Ubuntu.
 
Por otra parte, como se solicita en le letra del proyecto, se configura la misma con dos interfaces de red (enp0s3 y enp0s8). La interface enp0s3 conectada a NAT y enp0s8 a una red interna que permite conectarse a las otras máquinas.
        





En la imagen a continuación se puede apreciar las IP asignada a cada una de las interfaces.
 
3.2.	Instalar ansible
Para comenzar a utilizar ansible como medio de administración, debemos instalarlo en el equipo de bastión. Para ellos ejecutar los comandos:
•	apt update
•	apt install ansible

3.3.	Configurar Claves
Para configurar las claves públicas en el bastión, antes debemos generar el par de claves en cada una de las máquinas host. Se debe utilizar las siguientes instrucciones:
•	ssh-keygen

El par de claves, la generará en la ruta:
•	/home/user/.ssh/id_rsa

User: carpeta del usuario

Desde el equipo bastión, procedemos a configurar las claves públicas del resto de los equipos hosts (CentOS y Ubuntu). Para ellos utilizamos los siguientes comandos:
•	ssh-copy-id remote_username@server_ip_address
Username = usuario host
server_ip_address= IP máquina host
Desde el equipo bastión se deberá ingresar la password del host, y si es correcta, como resultado se agregará la clave al equipo. Una forma de verificar esto es de la siguiente manera: ssh user@IP_HOST
 

3.4.	Instalar Git
Como solicita el proyecto, debemos utilizar los archivos que se encuentra en un repositorio de Github (https://github.com/emverdes/obligatorio_2021_08). Primero, debemos crear o ingresar con nuestro usuario al repositorio de referencia. Una vez en el mismo, hacer un fork.
 
En los repositorios de nuestro perfil debemos visualizarlo.
 
Lo siguiente que se debe realizar es generar la clave SSH en el equipo bastión de la misma forma que se hicieron en los hosts. Una vez que tengamos esta clave, debemos copiarla e importarla a nuestro perfil de Github. 
 
Posteriormente, en nuestro equipo bastión debemos instalar Git con el siguiente comando:
•	apt install git
Una vez instalado, procedemos a configurar y clonar el repositorio de nuestro perfil para poder trabajar de forma local y luego subir los cambios aplicados.
En nuestro flujo de trabajo, realizaremos lo siguiente:
•	Clonar el repositorio
•	Realizar “pull” del repositorio para siempre tener el mismo actualizado.
•	Realizar “git add .” en caso que tengamos archivos nuevos para subir.
•	Realizar “commit” con un mensaje descriptivo de lo que modificamos.
•	Si todo está bien, hacer “push” para subir los cambios al repositorio remoto.
A continuación, configuramos nuestro ambiente Git.
Nombre de usuario
•	$ git config --global user.name "itabarez"
Dirección de correo electrónico
•	$ git config --global user.email nacho.tabarez13@gmail.com
Clonar repositorio
•	$ git clone “https://github.com/itabarez/obligatorio_2021_08.git”
La URL mencionada anteriormente, se obtiene desde el repositorio
 
Ejecutado el comando, nos dejará en /home/[usuario] los archivos del repositorio.
 
A partir de ese momento, se puede comenzar a trabajar sobre los archivos.
3.5.	Correcciones de errores archivos
A continuación, se detallan todas las correcciones realizadas a los archivos del repositorio para que se ejecute correctamente en el equipo bastión, y ahí a los hosts que tiene configurado en un inventario.
En el inventario se agregaron las siguientes variables:
[webservers]
centos ansible_host=192.168.56.104

[dbservers]
ubuntu ansible_host=192.168.56.105

[centos]
centos ansible_host=192.168.56.104

[ubuntu]
ubuntu ansible_host=192.168.56.105

[linux:children]
centos
ubuntu

Archivo site.yml de la carpeta /lamp/
•	se modificó remote_user con el valor "ansible"
•	se agregó la propiedad become en cada uno de los roles
Archivo hosts de la carpeta /lamp/
•	se agregaron los valores de las variables [webserver], [dbservers]
•	 se agregaron las variables [centos], [ubuntu] y [linux:children]
•	se configurar las ip para cada uno de los hosts.

Archvio main.yml de la carpeta roles/common/handlers/
•	se dieron salto de linea para name y state
•	se sustituyeron los "=" por ":"
Archivo chrony.conf.j2 de la carpeta /roles/common/templates/
•	se modificó el server definido anteriormente por: server {{ ntpserver }}
Archivo main.yml de la carpeta roles/common/task/
•	se dieron salto de línea para name y state
•	se sustituyeron los "=" por ":"
•	se pusieron las variables (item) entre comillas "".
•	salto de línea en "Configure ntp file" para el atributo "src"
•	salto de línea en "dest" y se sustituyó "=" por ":"
•	salto de línea en "tags".
•	salto de línea en enables y se sustituyó "=" por ":".
•	Se agregaron las condiciones en cada sentencia para ejecutar cada "proceso "según el sistema operativo como solicita la letra del proyecto
•	Para Debian, el módulo a instalar se llama "ntp".
•	Para Redhat el módulo a instalar se llama "chrony".
•	Para Redhat, se copia el archivo chrony.conf para la carpeta /etc/ .
•	Para Redhat, se copia el archivo chrony.conf.j2 para la carpeta /roles/common/templates/ .
Archivo main.yml de la carpeta roles/db/handlers/
•	se dieron salto de línea para name y state
•	se sustituyeron los "=" por ":"
Archivo main.yml de la carpeta roles/db/task/
•	se dieron salto de línea para name y state
•	se sustituyeron los "=" por ":"
•	se pusieron las variables (item) entre comillas "".
•	salto de línea en "Configure SELinux to start mysql on any port" para el atributo "name" y "state"
•	salto de línea en "dest" y se sustituyó "=" por ":"
•	salto de línea en "tags".
•	salto de línea en enables y se sustituyó "=" por ":".
•	salto de línea en "persistent" y se sustituyó "=" por ":".
•	salto de línea en "Create Mysql configuration file" para el atributo "src"
•	salto de línea en "dest" y se sustituyó "=" por ":"
•	salto de línea en "path" y se sustituyó "=" por ":"
•	salto de línea en "state" y se sustituyó "=" por ":"
•	salto de línea en "owner" y se sustituyó "=" por ":"
•	salto de línea en "group" y se sustituyó "=" por ":"
•	salto de línea en "mode" y se sustituyó "=" por ":"
•	En "Create MariaDB PID Directory" salto de línea en "path" y se sustituyó "=" por ":"
•	En "Create MariaDB PID Directory" salto de línea en "state" y se sustituyó "=" por ":"
•	En "Create MariaDB PID Directory" salto de línea en "owner" y se sustituyó "=" por ":"
•	En "Create MariaDB PID Directory" salto de línea en "group" y se sustituyó "=" por ":"
•	En "Create MariaDB PID Directory" salto de línea en "mode" y se sustituyó "=" por ":"
•	Se agregaron las condiciones en cada sentencia para ejecutar cada "proceso "según el sistema operativo como solicita la letra del proyecto.
•	Para "Debian" el paquete se llama "python-mysqldb".
•	Se quitaron las sentencias de configuración de "SELinux" para "Debian".
Archivo copy_code.yml de la carpeta roles/web/tasks/
•	se dieron salto de línea para "repo" y "dest"
•	se sustituyeron los "=" por ":"
•	se agrega comillas ("") a la varibale "repository"
•	se dieron salto de línea para "src" y "dest"
•	se sustituyeron los "=" por ":"
Archivo install_httpd.yml de la carpeta roles/web/tasks/
•	se dieron salto de línea para "name", "state", "persistent", "permanent", "immediate" y "enable"
•	se sustituyeron los "=" por ":"
•	se agrega comillas ("") a la varibale "item"
•	se agrega comillas ("") a la varibale "httpd_port"
•	Se agregaron las condiciones en cada sentencia para ejecutar cada "proceso "según el sistema operativo como solicita la letra del proyecto
•	Se modificó el módulo de instalación de apache para "Debian", debe ser "apache2"
•	Se modificó el módulo de instalación de "firewalld" por "ufw" en el sistema operativo "Debian"
•	Se modificó las propiedades en las instrucciones de "ufw". Se agregaron "to_port", "state".
•	Se quitaron las sentencias de configuración de "SELinux" para "Debian"
•	Se modificaron las instrucciones de "service state", dado que para "Debian" debe ser "apache2".
3.6.	Ejecución ansible
Para verificar las correcciones de los archivos mencionados anteriormente, procedemos a realizar la ejecución de ansible para el playbook, esperando resultados existosos. Para ello utilizamos los siguientes comandos en el equipo bastión (dentro del directorio /home/ansible/obligatorio_2021_08/lamp):
•	Eval $(ssh-agent)
•	Ssh-add
 
Posteriormente, ejecutamos el ansible de la siguiente manera:
•	Ansible-playbook -i hosts site.yml –ask-become-pass
 
Evidencias de la ejecución satisfactoria.
 
 
 
 
 
3.7.	Roles Ansible
Los roles permiten cargar automáticamente archivos, controladores, ejecutar tareas, y demás acciones.
A medida que vamos añadiendo funcionalidad y complejidad a nuestros playbooks, cada vez se hace más difícil manejarlo. Los roles, nos permiten crear un playbook con una mínima configuración y definir la complejidad y lógica de las acciones a más bajo nivel.
Según la propia documentación de Ansible: “Los roles en Ansible se basan en la idea de incluir archivos y combinarlos para formar abstracciones limpias y reutilizables: le permiten concentrarse más en el panorama general y solo sumergirse en los detalles cuando sea necesario.”
Para la correcta utilización de los roles, es necesario crear toda una estructura de carpetas y subcarpetas donde iremos depositando nuestra configuración. 
4.	REFERENCIAS

•	Material y video de Aulas – https://aulas.ort.edu.uy
•	Documentación Ansible - https://docs.ansible.com/ansible/latest/user_guide/index.html 

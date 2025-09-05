# Reporte del Laboratorio - Intro a Linux CLI
## Ximena Marín Sánchez C14448

## Introducción

El presente reporte tiene como finalidad la creación de una imagen base o "golden image" siendo el nombre asignado a una máquina virtual cuyo objetivo es posteriomente crear más máquinas virtuales y su enfoque es mostrar los comandos específicos empleados para la Golden Image. 

Para el desarrollo de la máquina virtual se utiliza Virtual Box 7.2.0, con el sistema operativo Rocky Linux 9, la versión Rocky Linux 9.6 minimal iso, con la finalidad que no tenga interfaz gráfica e impulsar el aprendizaje de usar la línea de interfaz de comandos.  

Las herramientas y guías mostradas a continuación son las que se utilizaron para el desarrollo del reporte:

- CIS Rocky Linux 9 Benchmark

- [Automated security hardening with OpenSCAP](https://tech-couch.com/post/automated-security-hardening-on-rockylinux-with-openscap)

- [Firewalld](https://docs.rockylinux.org/guides/security/firewalld-beginners/)

- [Fail2ban](https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-rocky-linux-9)

- [DNF Automatic](https://docs.rockylinux.org/guides/security/dnf_automatic/)

## Instalación  de VirtualBox y Rocky Linux 9 
1. Ingresar a [VirtualBox Downloads](https://www.virtualbox.org/wiki/Downloads)

2. Descargar la opción según sea su máquina host, por ejemplo Windows. 

3. Seguir los pasos de la instalación de VirtualBox.

4. Ingresar a la página de [Rocky Linux Downloads](https://rockylinux.org/pt-PT/download)

5. Descargar la versión 9.6 llamada Minimal ISO

### Tamaños utilizados para la máquina Virtual
La elección de 20 GB de disco duro, 4096 MB de memoria y 4 CPUs, fue con la finalidad de no tener problemas de almacenamiento, y simular una computadora. Cabe destacar que también las medidas utilizadas dependen de los recursos de la computadora host. 

## Particionamiento personalizado

En los puntos de montaje puede seleccionar Partición Estándar o LVM, en mi caso escogí LVM porque permite mayor escalabilidad. 

La distribución del particionamiento que utilice es:
 
| Partición      | Tamaño       | Tipo  | LVM
| ----------  | :-------------: | ----: | ----:
| /home   | 1 GiB   | ext4 | Sí
| /var/log   | 2 GiB   | ext4 | Sí
| /var/log/audit  | 2 GiB   | ext4 | Sí
| /var/tmp |  1,41 GiB | ext4 | Sí
| / |  7 GiB | ext4 | Sí
| /tmp |  1 GiB | ext4 | Sí
| /var |  2 GiB | ext4 | Sí
| /boot |  1 GiB | ext4 | No
| /boot/efi |  600 MiB | EFI | No
| swap |  7 GiB | SWAP | No

### ¿Por qué personalizar el particionamiento?
Dado que se tiene un mayor control sobre la máquina virtual de designar los tamaños asignados a cada una y previene  desbordamientos de disco. 

Asimismo, los tamaños de las particiones se deben basar en el tamaño del disco definido para la máquina virtual.

*Consejo:* Para evitar un error que detecta OpenSCAP **no olviden /var/log/audit** y en general ninguna de las particiones para evitar futuros problemas con OpenSCAP. 

## Usar la Minimal ISO Rocky Linux 9.6
Al ser sin interfaz gráfica, solo se observará la línea de comandos, en el menú principal pueden darle ENTER.

Se solicita el login basado en las contraseñas definidas en la Instalación previa, para root y admin.

## Conectar SSH
El objetivo principal por el cual lo implementé es que al ser la máquina virtual no se permitia copiar y pegar desde la máquina hosts los comandos, dificultando la velocidad y aumentando el margen de error al digitar carácter por carácter, es por ello que hay una manera más sencilla, que explico a continuación:

- Estar en el menú principal de Oracle VirtualBox 

- Hacer click derecho a la máquina virtual y presionar Configuración

- Seleccionar la opción de red

- Conectar a: Adaptador puente, y en nombre selecciona su controlador de red, en mi caso empezaba por Intel

- Presionar la opción de Aceptar

### ¿Cómo saber mi IP en Rocky Linux?
Una vez ingresado en el sistema operativo use el comando
``` 
ip a
```
El comando lo que hace es mostrar las direcciones IP, y se ejecuta para poder crear una conexión FULL DUPLEX entre la máquina host y la máquina virtual. 

Seleccione la dirección IP, por ejemplo, puede empezar con 192.

### Abrir una terminal en la computadora Host
Ahora que conoce la dirección IP de la máquina virtual, abra una terminal en su computadora por ejemplo de WSL o de su preferencia 

Y digite el siguiente comando:

```   
ssh admin@[direccion IP obtenida del ip a]
```
Lo que hace es establecer mediante el ssh, en este caso con el admin crear una conexión FULL DUPLEX, y se ejecuta para ser la terminal en la que se copien los comandos y así evitar tener que digitar caracter por caracter los comandos en la terminal de Rocky Linux dentro de la máquina virtual. 

## Instalar OpenSCAP
```
sudo dnf install scap-security-guide openscap-scanner
```

El comando lo que hace es descagar openscap-scanner y el de scap-security-guide proporciona múltiples perfiles predeterminados para estándares comunes de seguridad. 

Se ejecuta para mejorar el hardening del sistema operativo dado que es un software para auditar el cumplimiento de políticas de seguridad estándar.

## Observación: Intenta apagar la máquina virtual en este punto 

OpenSCAP tras la instalación implementa una política de seguridad y se caducan las contraseñas previas.

La recomendación es porque entre más pronto apagues posterior a la descarga de OpenSCAP, mejor, dado que en caso de tener problemas técnicos como el mensaje: Password expired, contact your system administrator, es más rápido iniciar de cero y en la nueva máquina virtual llegar a este punto.

```
sudo poweroff
```

### Configuración de expiración de contraseñas

Si intentaste apagar la máquina virtual te debio salir el mensaje:

"You are required to change your password inmediately (password expired)

Lo que debes hacer es
- Escribir tu contraseña actual
- **Escoger una contraseña con 14 caracteres, teniendo Mayúsculas, minúsculas, números, y caracteres especiales**
- Una manera de escoger las contraseñas es la unión de palabras, para saber que cumple con el minimo de caracteres
- Recomendación: Dado que en la terminal no puedes ver lo que digitas, puedes ir más despacio para tener mayor certeza que presionaste las letras que deseabas.  

### Definir una fecha de expiración
Tras el restauro de tus contraseñas, para evitar de que se vuelva a expirar más pronto de lo que esperas, puedes utilizar el siguiente comando:

```
sudo chage -M [cantidad maxima de dias antes de que se caduzca la contraseña] [usuario]
```

```
sudo chage -M 6000 admin

sudo chage -M 6000 root
```

El comando lo que hace es definir una cantidad de días máximo a que se caduque la contraseña, en el caso de 6000 sería en el 2042, y se ejecuta para definir una fecha en la que se debe caducar una contraseña.

### Verificar la nueva fecha de caducidad

```
chage -l admin
```

```
sudo chage -l root
```

Lo que hace es visualizar la información de las contraseñas asociadas a cada usuario, y se ejecuta para revisar de que se haya actualizada la fecha de caducidad con respecto a los días seleccionados en el caso anterior. 

En este caso tanto para root como para admin se cambia y verifica la nueva caducidad de la contraseña. 

## Para hacer pruebas con OpenSCAP

```
sudo oscap xccdf eval \
  --remediate \
  --profile xccdf_org.ssgproject.content_profile_cis \
  --report compliance-report.html \
  /usr/share/xml/scap/ssg/content/ssg-rl9-ds.xml
```

El comando lo que hace es mostrar una salida con lo que evalua y si pasó o no la prueba del cumplimiento de políticas de seguridad, además de que tiene la capacidad de solucionar automáticamente los problemas detectados durante la evaluación.

Se ejecuta para saber en que se puede mejorar y los aspectos pendientes de implementar para cumplir con las políticas de seguridad estándar.

*Consejo* Lo puede ejecutar en la terminal de su máquina host para mayor facilidad, y correrlo más de una vez para que haya un aumento en los que tienen el "Result fixed" 

## Visualización y solución de los problemas detectados por OpenSCAP

### Resultado fallido 1: 
```
--- Starting Remediation ---
Title   Ensure all users last password change date is in the past 
Rule    xccdf_org.ssgproject.content_rule_accounts_password_last_change_is_in_past
Result  fail
```
### Solución 1:
Lo que nos comunica el mensaje es que los cambios de contraseñas tenían fecha en el futuro.

Es por ello que se ejecuta el siguiente comando para visualizar la fecha asociada de cambio de contraseña

```
chage -l admin
```
```
sudo chage -l root
```

El siguiente comando es para ajustar la fecha con una fecha pasada con la finalidad que la siguiente vez que se ejecute OpenSCAP el resultado sea aprobado. 

```
sudo chage --lastday 2025-08-29 root
sudo chage --lastday 2025-08-29 admin
```

### Resultado fallido 2: 
```
Title   Set Boot Loader Password in grub2 
Rule xccdf_org.ssgproject.content_rule_grub2_password
Result fail  
```

### Solución 2:
```
sudo grub2-setpassword
```

Este comando es para crear una contraseña encriptada y asegurarse de que la contraseña del cargador de arranque esté configurada. 

Y se ejecuta para que en la siguiente ejecución de OpenSPAC el resultado sea aprobado.

### Resultado fallido 3: 
```
Title   Limit Users' SSH Access 
Rule    xccdf_org.ssgproject.content_rule_sshd_limit_user_access
Result fail
```

### Solución 3:
```   
sudo nano /etc/ssh/sshd_config
```

Escriba la siguiente línea en el editor de texto para que limite el acceso a SSH exclusivamente al usuario llamado admin.  

```
AllowUsers admin
```

##  Instalar Firewalld

Es el comando para habilitar el firewalld.

Se ejecuta porque proporciona un firewall gestionado dinámicamente y es el firewall por defecto de Rocky Linux.

```
sudo systemctl enable --now firewalld
```

El comando es para visualizar cuál es la zona que tiene el firewall por defecto

Se ejecuta para verificar cuál es la zona predeterminada y el resultado que puede aparecer es Public.

```
sudo firewall-cmd --get-default-zone
```

Se ejecuta para verificar que zonas se encuentran activas. 
```
sudo firewall-cmd --get-active-zones
```

Se ejecuta el comando para verificar los servicios que tiene asociados la zona predeterminado, lo que nos permite evaluar si se ocupan habilitar otros servicios y puertos para la misma.

```
sudo firewall-cmd --list-all
```

El comando es para establecer una zona de trabajo.

Se ejecuta el comando porque para efectos del reporte solo se va a confiar en las máquinas de trabajo dentro de la misma zona de trabajo. 
```
sudo firewall-cmd --set-default-zone work
```

El comando es para habilitar en este caso ssh en la zona de trabajo.

Se ejecuta para garantizar que se pueda conectar por ssh

```
sudo firewall-cmd --zone=work --add-service=ssh
```

El comando es para habilitar un puerto.

Se ejecuta para habilitar un puerto de trabajo teniendo las computadoras una comunicación por el puerto 5000 TCP.

```
sudo firewall-cmd --zone=work --add-port=5000/tcp
```

Es para guardar los cambios de forma permanente. 

```
sudo firewall-cmd --runtime-to-permanent
```

## Requisito previo a usar Fail2Ban
- Estar utilizando un sistema Rocky Linux 9
- Tener el firewalld corriendo, que se puede revisar con el siguiente comando:
```
systemctl status firewalld
```

## Instalación Fail2Ban

El comando lo que hace es utilizar el repositorio EPEL, que se utiliza para paquetes de terceros de Red Hat y Rocky Linux, dado que Fail2ban no está disponible en el repositorio de software predeterminado de Rocky.

```
sudo dnf install epel-release -y
```

Se ejecuta el comando para instalar el sistema de prevención de intrusiones Fail2Ban cuyo objetivo en el reporte es habilitar el SSH. 

```
sudo dnf install fail2ban -y
```

### Instalación de nano en Rocky Linux

Lo que hace es instalar un editor de texto denominado nano, que se suele usar para sistemas operativos Unix y Linux.

Se ejecuta para que en el siguiente paso no les de un error.

```
sudo dnf install nano
```

### Creacion de archivo para habilitar el SSH en Fail2Ban

Crea un archivo llamado jail.local en la carpeta /etc/fail2ban

Se ejecuta para mejorar las políticas de seguridad, y evitar posibles ataques de fuerza de bruta al tener una cantidad considerable de intentos en un tiempo determinado.

```
sudo nano /etc/fail2ban/jail.local
```

A continuación escriba en el editor de texto lo que desea implementar.

* El parámetro enabled = true es para habilitar el ssh

* El parámetro logpath le indica donde estan localizados los logs. 

```
[DEFAULT]
# Ban hosts for fifteen minutes:
bantime = 900

# Maxium of tries in ten minutes
findtime = 10m
maxretry = 3

[sshd]
enabled = true
port = ssh
logpath = %(sshd_log)s
backend = %(sshd_backend)s
```

## Verificación de que está corriendo Fail2ban

Este comando se ejecuta para revisar de que se haya configurado de la manera esperada, por ejemplo, en la impresión que se muestra, Tasks: tiene el número definido por maxretry.

Se ejecuta con la finalidad de revisar de que la salida sea según lo esperado y esté actualizada.

```
sudo systemctl status fail2ban
```

### Ejecución automática

El comando es para habilitar para que Fail2Ban se ejecute automáticamente

Se ejecuta el comando con la finalidad de que se inicie automáticamente tras el arranque del sistema. 

```
sudo systemctl enable fail2ban
```

## Instalación del paquete dnf-automatic

Descarga la herramienta que se utiliza para proporcionar actualizaciones de software. 

Se ejecuta para tener automatizadas las descargas y actualizaciones en el servidor de Rocky.

```
sudo dnf install dnf-automatic
```

### Configuración para hacer actualizaciones de seguridad

El comando es para editar el temporizador de dnf-automatic

Se ejecuta para personalizar el dnf-automatic.

```
sudo systemctl edit dnf-automatic.timer
```

#### Script del dnf-automatic

Se debe descomentar las siguientes líneas.

Se ejecuta para tener una configuración definida para actualizar en este caso con un retraso de inicio entre las 6 y 6:20 am 

```
[Unit]
Description=dnf-automatic timer
# See comment in dnf-makecache.service
ConditionPathExists=!/run/ostree-booted
Wants=network-online.target

[Timer]
OnCalendar=*-*-* 6:00
RandomizedDelaySec=20m
Persistent=true

[Install]
WantedBy=timers.target
``` 

### Activación de temporizador

Se ejecuta para activar el temporizador asociado al servicio según lo estipulado en el paso anterior.

``` 
sudo systemctl enable --now dnf-automatic.timer
``` 

# Configuración SSH

## Pasos para que permita llaves y contraseñas 

El comando es para acceder al archivo sshd_config.

Y se ejecuta dado que se van a realizar modificaciones, por ejemplo para permitir llaves y contraseñas

``` 
sudo nano /etc/ssh/sshd_config
``` 

* En la línea de que dice PasswordAuthentication, descomentarla y revisar que la opción asociada es yes.

* Sirve para que el servidor SSH autentique a los usuarios mediante llaves (pública y privada) y contraseñas.

``` 
PasswordAuthentication yes
``` 

De igual manera descomente la linea de PubKeyAuthentication, lo que hace es permitir usar un par de claves SSH pública y privada, por ejemplo, si desea intentar ahorrar tiempo al acceder a los servidores Rocky Linux ya que facilitan el inicio de sesión en máquinas remotas y la ejecución de comandos.

``` 
PubKeyAuthentication yes
``` 

El comando es para que se cargue la configuración con los nuevos cambios.
``` 
sudo systemctl restart sshd
``` 

### Generación de llaves en SSH

En la terminal Windows para crear una llave ejecute el siguiente comando:

``` 
ssh-keygen
``` 

Al no pertenecer a la preparación del Golden Image ni del Hardening el reporte no tendrá el paso a paso.

Para tener mayor información puede revisar el sitio [Configuracion claves SSH en Rocky Linux](https://www-digitalocean-com.translate.goog/community/tutorials/how-to-set-up-ssh-keys-on-rocky-linux-9?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es&_x_tr_pto=tc&_x_tr_hist=true)


## Inhabilitar que el root se conecte por ssh

El comando es para acceder al archivo sshd_config.

Se ejecuta para definir que el usuario root no se pueda logear usando SSH.

``` 
sudo nano /etc/ssh/sshd_config
``` 

En la línea PermitRootLogin descomentela y escriba no, debido a que de esta manera los administradores deben autenticarse con su propia cuenta individual y luego escalar a root.

``` 
PermitRootLogin no
``` 

## Pasos para no permitir contraseñas en blanco

El comando es para acceder al archivo sshd_config.

Se ejecuta para acceder a la configuración y modificar el archivo al impedir contraseñas en blanco.

``` 
sudo nano /etc/ssh/sshd_config
``` 

En la línea PermitEmptyPasswords descomentela, y se realiza para reducir las probabilidades de acceso no autorizado al sistema, debido a que brinda mayor seguridad. 

``` 
PermitEmptyPasswords no
``` 

# Conclusiones

La elaboración de la máquina virtual brinda nuevos desafíos y un tiempo asociado a resolver problemas, no obstante para el final de la misma cumplirá con el Nivel 1 de CIS Server.

Habrá aprendido sobre el particionamiento, cumplir con políticas de seguridad, tendrá hardening implementado, y el conocimiento de como instalar y utilizar las diversas herramientas mostradas en esta guía como Firewalld, Fail2Ban, OpenSCAP y DNF-Automatic. 

Asimismo, sabrá sobre como modificar el archivo de configuración de SSH basado en sus necesidades y la seguridad, por ejemplo, al no permitir contraseñas vacías y habilitar los usuarios que tienen acceso al SSH.

Y por último, cabe destacar que su servidor al implementar estas herramientas tendrá un firewall, actualizaciones configuradas autómaticamente entre un rango de las 6 am a 6:20 am, y al estar actualizada implementa los parches de seguridad evitando ataques en una vulnerabilidad implementada en una actualización.

# Ejercicio de DNS y Routing

## CREACIÓN

### RAIDs

Durante la instalación de Ubuntu, en la creación de particiones, se elige la opción de **Configurar RAID por software**.

**RAID 0** será utilizado para el PC del servidor DNS, y utilizará dos discos duros.

![RAID 0](/RAID/raid0.png)

**RAID 5** será utilizado para los tres PCs del servidor ftp y http, y utilizarán cuatro discos duros.

![RAID 5](/RAID/raid5.png)

En ambos casos se crea una partición de intercambio fuera del RAID.

### DNS

- Lo primero que se hace es instalar el servidor DNS, la aplicación de nombre **BIND9**.

![BIND9](/DNS/DNS_bind.png)

`sudo apt-get install bind9`

- Tras instalar **BIND9** modificaremos el archivo `/etc/network/interfaces` para tener la IP estática.

![DNS interfaces](/DNS/DNS_interfaces.png)

- Después modificaremos algunos archivos localizados en `/etc/bind/` para configurar el servidor DNS.

![named.conf.local](/DNS/DNS_named.conf.local.png)

`/etc/bind/named.conf.local`

![named.conf.options](/DNS/DNS_named.conf.options.png)

`/etc/bind/named.conf.options`

- Entonces crearemos los nuevos archivos con la configuración de la resolución de nuetras URLs.

Se crean archivos con el nombre del dominio precedido de _rd._ con el siguiente contenido.

![rd.sitioa.com](/DNS/DNS_rd.sitioa.com.png)

![rd.sitiob.net](/DNS/DNS_rd.sitiob.net.png)

![rd.sitioc.net](/DNS/DNS_rd.sitioc.net.png)

Una vez creados se crea el archivo de la resolución contraria, para obtener la URL desde la IP.

![ri.192.168.15](/DNS/DNS_ri.192.168.15.png)

Con esto tendremos nuestro servidor DNS creado y listo para usar.

### SERVIDORES

- De igual manera que con el servidor DNS, lo primero que hay que hacer es instalar **APACHE2** y **VSFTPD**, las aplicaciones usadas para crear el servidor HTTP y FTP respectivamente.

![Apache](/SERVERS/SERVER_apache.png)

`sudo apt-get install apache2`

![Vsftpd](/SERVERS/SERVER_ftp.png)

`sudo apt-get install vsftpd`

- Una vez tenemos ambos instalados configuraremos el archivo `/etc/network/interfaces` para modificar la IP.

![interfaces A](/SERVERS/SERVER_A_interfaces.png)

![interfaces B](/SERVERS/SERVER_B_interfaces.png)

![interfaces C](/SERVERS/SERVER_C_interfaces.png)

Una vez estén las IPs configuradas de la manera que indicamos en el servidor DNS, habremos terminado. Ya se podrá acceder al servidor HTTP creado por apache accediendo a la IP de la máquina.

### ROUTER

- Se instalan dos adaptadores de red en la máquina que vaya a usarse como router/puerta de enlace; una para conectar con internet, y otra para conectar con la red local.

- Tras esto se configura el archivo `/etc/network/interfaces` para configurar la conexión de los dos adaptadores.

![ROUTER interfaces](/ROUTER/ROUTER_interfaces.png)

- Una vez terminado, hay que activar el packet forwarding para IPv4. Esto se realiza accediendo al archivo `/etc/sysctl.conf` y eliminando el comentario `#` de la linea `#net.ipv4.ip_forward=1`.

![sysctl.conf](/ROUTER/ROUTER_sysctl.conf.png)

- Luego debemos de crear las **iptables** para que el firewall de linux permita acceso a las otras máquinas de la red que entran desde la tarjeta de red de la local. Para no tener que insertarlas manualmente crearemos un archivo de bash `.sh` que permitirá alterar las reglas de las iptables al iniciar el sistema sin tener que hacer nada.

![iptables](/ROUTER/ROUTER_iptables.png)

- Luego añadimos la ejecución del archivo a `/etc/rc.local`, que es el archivo donde se registran los comandos a ejecutarse en el inicio de sesión. Añadimos la linea `sh /(dirección del .sh antes creado)` antes de `exit 0`

![rc.local](/ROUTER/ROUTER_rc.local.png)

### LINUX GRÁFICO

- Se modifica la configuración de la conexión para añadirle una IP del octeto donde están el resto de máquinas, y configuramos los parámetros de la IP estática.

![editando conexión](/GRAFICO/GRAFICO_conexion.png)

## RESULTADO

![accediendo google](/GRAFICO/GRAFICO_routed.png)

#### Accediendo a `www.google.es` desde el Ubuntu con entorno gráfico.

![accediendo el sitioa](/GRAFICO/GRAFICO_sitioa.png)

#### Accediendo a `www.sitioa.com` desde el Ubuntu con entorno gráfico mediante nuestro servidor DNS y servidor Apache.

![accediendo el sitiob](/GRAFICO/GRAFICO_sitiob.png)

#### Accediendo a `www.sitiob.net` desde el Ubuntu con entorno gráfico mediante nuestro servidor DNS y el servidor Apache.

![accediendo el sitioc](/GRAFICO/GRAFICO_sitioc.png)

#### Accediendo a `www.sitioc.net` desde el Ubuntu con entorno gráfico mediante nuestro servidor DNS y el servidor Apache.

![accediendo wikipedia](/GRAFICO/GRAFICO_wikipedia.png)

#### Accediendo a `www.wikipedia.org` desde el Ubuntu con entorno gráfico mediante nuestro Ubuntu configurado como Router.

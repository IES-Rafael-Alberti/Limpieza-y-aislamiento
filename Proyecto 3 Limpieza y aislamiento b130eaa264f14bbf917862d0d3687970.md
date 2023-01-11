# Proyecto 3 Limpieza y aislamiento

## Linux

Empezaremos realizando una actualización del Sistema Linux.

![Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image1.png](Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image1.png)

Aleatorización del diseño del espacio de direcciones del kernel (KASLR)

Esto obligara a los ejecutables como glibcse se encuentren cada vez en direcciones diferentes y que así el atacante no sea capaz de lanzar un exploit con direcciones predecibles. ASLR además de cambiar las direcciones, hará que el kernel se bloquee cuando se ejecute un exploit.

Editando el archivo /proc/sys/kernel/randomize_va_spacecon y añadiéndole el valor 2 activaremos la aleatorización completa más todo lo siguiente:

- Stack ASLR: cada ejecución de un programa da como resultado un diseño de memoria de pila diferente
- LIBS/MMAP ASLR: cada ejecución de un programa da como resultado un mmapdiseño de espacio de memoria diferente
- EXEC ASLR: cada programa que se cumplió -fPIE -pie, que significa Position Independent Executables, se cargará en diferentes ubicaciones de memoria

![Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image2.png](Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image2.png)

### Hardening de servicios

```bash

- Restringir kallsyms: chmod 400 /proc/kallsyms
- Acceso solo root a PERF: sysctl -w kernel.perf_event_paranoid = 3
- Eliminar acceso a ptrace: sysctl -w kernel.yama.ptrace_scope=3
- Deshabilitar FIFOs:

> sysctl fs.protected_regular = 1
> sysctl fs.protected_fifos = 1
```

![Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image3.png](Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image3.png)

### Deshabilitar IPv6 utilizando Sysctl

![Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image4.png](Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image4.png)

## Controlar el acceso de root

modificamos el fichero /etc/securetty para controlar las tty desde donde root puede conectarse:

![Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image5.png](Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image5.png)

## Configuraciones SSH

SSH terminar conexión por inactividad o no responde:

Entramos al archivo /etc/ssh/sshd_config y añadimos lo siguiente

> ClientAliveInterval 500
> 

> ClientAliveCountMax 3
> 

## Instalación app armor

![Untitled](Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/Untitled.png)

Podemos comprobar si esta instalado con el siguiente comando:

```bash
aa-status
```

![Untitled](Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/Untitled%201.png)

## Windows

Para empezar a limpiar la imagen de windows vamos a utilizar la herramienta Chris Titus Tech's la cual nos permitira realizar una pequeña optimización para empezar.

Utilizando el siguiente comando en powershell como administrador podemos lanzar la aplicación:

iwr -useb [https://christitus.com/win](https://christitus.com/win) | iex

Una vez ejecutada nos aparecera la siguiente ventana en la que podremos empezar a trabajar:

![Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image6.png](Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/image6.png)

Con esta primera implementación estaremos deshabilitando muchos servicios y funciones que se ejecutan en segundo plano y ralentizan nuestro sistema.

Tras esto accedemos a la sección de actualizaciones y las configuramos para que se realicen solo las de seguridad para reducir las posibilidades de tener una actualización que traiga incompatibilidades.

![Untitled](Proyecto%203%20Limpieza%20y%20aislamiento%20b130eaa264f14bbf917862d0d3687970/Untitled%202.png)

Programas, protocolos y servicios que sobran en la imagen de Windows 10 proporcionada en PNetLab:

En cuanto a los programas instalados, este Windows trae por defecto los siguientes:

- 7-Zip 19.00
- Google Chrome
- Microsoft Edge
- PuTTY release 0.74
- WinSCP 5.17.10

Como lo que queremos es darle un uso de navegación web y ofimática básica a la máquina, tendremos que desinstalar 3 de los 5 programas que listamos anteriormente:

- 7-Zip 19.00: Este lo quitaremos porque Windows ya trae por defecto un gestor de ficheros comprimidos.
- PuTTY release 0.74: Este programa está destinado a la conexión por Telnet y SSH, por lo tanto, no nos hace falta para darle las funcionalidades que deseamos a la máquina.
- WinSCP 5.17.10: Este programa está destinado a la transferencia de archivos por FTP, por lo tanto, tampoco nos hace falta para darle las funcionalidades que deseamos a la máquina.

En cuanto a los servicios, podríamos prescindir de los siguientes:

|Nombre|Nombre en Adm Tareas|Descripción|Configuración|Imagen Servicio|
|:----|:----|:----|:----|:----|
|Administrador de mapas descargados|MapsBroker| Al no utilizar la aplicación Mapas de Windows en la máquina Windows, lo podemos desactivar.|Deshabilitado| |
|Fax|Fax|No suele ser necesario para usuarios domésticos.|Deshabilitado| |
|Servicio biométrico de Windows|WbioSrvc|Al no tener hardware de huellas dactilares o reconocimiento facial en la máquina Windows, lo podemos desactivar.|Deshabilitado| |
|Servicio de seguimiento de diagnósticos|Diagsvc|Como no nos interesa mandar información de telemetría a Microsoft, lo podemos desactivar.|Deshabilitado| |
|Servicio de panel de escritura a mano y teclado táctil|TabletInputService|Como el usuario no va a utilizar hardware compatible (por ejemplo, una tarjeta gráfica) en la máquina Windows, lo podemos desactivar.|Deshabilitado| |
|Servicio asistente para la compatibilidad de programas|PcaSvc|Como no vamos a usar versiones antiguas de software en la máquina Windows, lo podemos desactivar.|Deshabilitado| |
|Servicio de compatibilidad con bluetooth|BluetoothUserService|Como la máquina Windows no va a utilizar ningún periférico, ni nada que requiera de conectividad bluetooth, lo podemos desactivar.|Deshabilitado| |
|Servicio de red de Xbox Live|XboxNetApiSvc|Como el usuario que maneja la máquina Windows, no va a utilizar la aplicación de Xbox, lo podemos desactivar.|Deshabilitado| |
|Partida guardada en Xbox Live|XblGameSave|Como el usuario que maneja la máquina Windows, no va a utilizar la aplicación de Xbox, lo podemos desactivar.|Deshabilitado| |
| | | | | |
|Administración de autentación de Xbox Live|XblAuthManager|Como el usuario que maneja la máquina Windows, no va a utilizar la aplicación de Xbox, lo podemos desactivar.|Deshabilitado| |
| | | | | |
|IPV6|Iphlpsvc|Como la máquina Windows utiliza direccionamiento IPV4, no requiere del uso de este servicio. Por lo tanto, lo podemos desactivar.|Deshabilitado|
|Escritorio Remoto|RemoteAccess|Como la máquina Windows va a ser utilizada por usuarios, en principio no van a necesitar que nadie acceda a su equipo o viceversa. A no ser que un administrador desee hacerlo por motivos técnicos. Por el momento, lo podemos desactivar.|Deshabilitado|
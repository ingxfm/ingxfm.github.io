+++
title = 'Convertidores USB: no se me cambien los nombres'
date = 2024-11-22T10:03:34+01:00
draft = false
lang = 'es'
+++

Tienes que conectar 3 cables USB a una workstation con Ubuntu 22.04. Estos 3 cables USB son 1 convertidor UART 232TTL y 2 convertidores RS485 USB.

Necesitas resetear la computadora unas cuantas veces. O, a veces, necesitas desconectar los cables. Y notas que los nombres (/dev/ttyUSB0, /dev/ttyUSB1, /dev/ttyUSB2) de los puertos seriales cambian en el Ubuntu OS.

Como estas usando un programa que necesita usar los nombres, te gustaría fijar los benditos. Bueno, estas en en el lugar correcto. Vamos a hacer eso mismo.

# Asignando nombres a cables específicos

Necesitamos identificar cada cable. Cada uno debería tener información de fábrica en la cual encontrar un identificador único. Para ello, usaremos las reglas y comandos udev. Para conseguir alguna información de fábrica:
```
udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep manufacturer
udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep product
udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep serial
```
De estos 3 comandos, obtendremos la siguiente información para uno de los cables:
```
➜  ~ udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep manufacturer
    ATTRS{manufacturer}=="FTDI"
    ATTRS{manufacturer}=="Generic"
    ATTRS{manufacturer}=="Linux 6.5.0-45-generic xhci-hcd"
➜  ~
➜  ~ udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep product    
    ATTRS{product}=="TTL232R-3V3"
    ATTRS{product}=="4-Port USB 2.0 Hub"
    ATTRS{product}=="xHCI Host Controller"
➜  ~
➜  ~ udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep serial
    SUBSYSTEM=="usb-serial"
    ATTRS{serial}=="FTD1BYGI"
    ATTRS{serial}=="0000:00:14.0"
```
Repitamos los mismos comandos para los otros 2 cables, también. Notemos que el diferenciador en esta data es el número de serie. Usémoslo para crear una regla que asigne o fije un nombre cada vez que un puerto USB detecte un cable especifico. Creemos o modifiquemos el archivo /lib/udev/rules.d/10-local.rules.
```
sudo nano /lib/udev/rules.d/10-local.rules
```
Entonces, copiemos la siguiente información en ese archivo: 
```
###########################
# Serial 232TTL cable     #
###########################
SUBSYSTEM=="tty", ATTRS{product}=="TTL232R-3V3", ATTRS{serial}=="FTD1BYGI", SYMLINK+="conv_serial232TTL_control"

####################################
# RS485 USB cable 1                #
####################################
SUBSYSTEM=="tty",ATTRS{serial}=="AV0KDAA6", ATTRS{manufacturer}=="FTDI", SYMLINK+="conv_RS485_furnace"

####################################
# RS485 USB cable 2                #
####################################
SUBSYSTEM=="tty",ATTRS{serial}=="FT54F6WE", ATTRS{manufacturer}=="FTDI", SYMLINK+="conv_RS485_meter"
```
Notemos que en el enlace simbólico SYMLINK podemos escribir cualquier string. Para este ejemplo, escribamos la abreviación "conv" por convertidor, y una palabra refiriéndose a que dispositivo se está conectando.

Ahora, tecleamos Ctrl+S para guardar nuestros cambios; y Ctrl+X para cerrar nano.

Entonces, refresquemos las reglas udev con:
```
sudo udevadm control --reload
```

E implementemos las reglas con:
```
sudo udevadm trigger
```
Podemos verificar que nuestros nuevos nombres han sido asignados a los cables:
```
ls /dev/conv_*
```
El output es:
```
/dev/conv_serial232TTL_control /dev/conv_RS485_furnace /dev/conv_RS485_meter
```

# Conclusión
Aquí lo tenemos. Ahora podemos conectar y desconectar los susodichos cables, o resetear la computadora y los nombres de los cables persistirán.

¡Salud!

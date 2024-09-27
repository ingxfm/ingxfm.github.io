+++
title = 'Comando en Linux Tail: útil para monitorear archivos de registro/log en texto'
date = 2024-09-27T14:16:42+02:00
draft = true
lang = 'es'
+++

Cuando estás monitoreando los archivos de registros o logs de cualquier programa en Ubuntu y muchos otros sistemas operativos basados en Linux un comando que puedes utilizar es el tail. El comando tail imprime las últimas líneas en la terminal de Linux.

Para ello, escribe y presiona Enter en tu programa de Shell, de la forma siguiente:

``` Shell
tail /<path>/logfile.log
```

Por default, el comando tail muestra las últimas 10 líneas de cualquier archivo de texto. Si quieres especificar el número de líneas, podemos agregar el parámetro "-n" de la forma siguiente:

``` Shell
tail -n 15 /<path>/logfile.log
```
En este ejemplo, el argumento para el parámetro 'n es 15, por lo tanto, tail presentará en la consola las últimas 15 líneas del archivo logfile.log.

Digamos que queremos monitorear los logs en tiempo real. Podemos observar las líneas a medida que son añadidas al archivo de registro/log con el parámetro "-f" de la manera siguiente:

``` Shell
tail -f /<path>/logfile.log
```

Para saber más sobre el comando tail, entra "tail --help" o "man tail" en la consola, o visita el manual online en: https://www.man7.org/linux/man-pages/man1/tail.1.html.


# Conclusión

El comando "tail" es útil para cuando imprimir texto en Linux y cuando estamos monitoreando archivos de registro/logs. La mala noticia es que tail no puede monitorear todos los logs. Desde la introducción de systemd, muchos archivos de logs son binarios, los cuales tail no puede leer. Para ese tipo de archivos, necesitaremos el comando journalctl. Pronto escribiré sobre este.

Iubilate!

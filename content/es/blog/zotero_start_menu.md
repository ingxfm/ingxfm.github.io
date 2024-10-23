+++
title = 'No te veo en el Inicio'
date = 2024-10-18T22:08:48+02:00
draft = false
lang = 'es'
+++

![Review_comment_funny_issue](/img/paint_lost_audio.png)
*Cuando instalas un programa para dibujar en Linux y de repente... (ಠ_ಠ)*

Imagina que has instalado un programa en Discover en tu Kubuntu 22.04.
![alt text](/img/zotero_installation.png)
Por alguna razón, no puedes encontrar el susodicho en el menú de Inicio. ¡Que panda el cúnico! Digo perdón, ¡que no cunda el pánico! 
![alt text](/img/zotero_not_start_menu.png)
Es que para que un programa aparezca en el menú de Inicio tiene que tener una entrada de Desktop en el directorio /usr/share/applications. Surgen 2 preguntas:

- ¿Cómo agregar agregar la entrada .desktop del programa a ese directorio?
- ¿Por qué Discover no está agregando la entrada como parte del proceso normal de instalación?

## ¿Cómo agregar agregar la entrada .desktop del programa a ese directorio?

Tenemos que agregar la así llamada entrada de desktop de nuestro programa para verlo en el menú de Inicio. Tuvimos un encuentro breve con estas entradas cuando tuvimos el problema con la herramienta de captura de pantallas [Spectacle](spectacle_bug.md).

De acuerdo a esta [referencia](https://wiki.archlinux.org/title/Desktop_entries#Application_entry), las entradas .desktop están localizadas en /usr/share/applications/. En este caso, estaba instalando el programa Zotero. Veamos si está allí:
```CLI
ls -l /usr/share/applications | grep zotero
```
El output me muestra que no está. Mmm, me not like. Vamos a agregarlo. ¿Pero, de dónde? Después de investigar, veo que los archivos .desktop están ya en /var/lib/snapd/desktop/applications/. ¿Está Zotero ahí?
```CLI
ls -l /var/lib/snapd/desktop/applications/ | grep zotero
```
El output dice que sí. Ahora, vamos a ponerlo en /usr/share/applications. Usemos un link simbólico:
```CLI
sudo ln -s /var/lib/snapd/desktop/applications/zotero-snap_zotero-snap.desktop /usr/share/applications
```
Entonces, podemos intentar de nuevo con el menú de Inicio.
![zotero in the start menu](/img/zotero_start_menu.png)
¡Victoria! Ahora, no deberíamos tener que hacer esto.

## ¿Por qué Discover no está agregando la entrada como parte del proceso normal de instalación?

No es un problema del snap de zotero, porque esto me pasa con los demás snaps en Discover.
```CLI
sudo journalctl -u packagekit | grep snapd
sudo journalctl -u snapd
```
Y puedo ver está advertencia, la cual no había ocurrido cuando antes cuando instalaba snaps:
```CLI
systemd[1]: /lib/systemd/system/snapd.service:23: Unknown key name 'RestartMode' in section 'Service', ignoring.
```
Luego de verificar, parece que este RestartMode es una [directiva de systemd (en inglés)](https://www.freedesktop.org/software/systemd/man/latest/systemd.service.html#RestartMode=). Hay un defecto reportado: https://github.com/systemd/systemd/issues/34758.

Para resolver esto vamos a hacer un script que agregue los enlaces simbólicos en el inicio de sesión:
```bash
#!/bin/bash
for file in /var/lib/snapd/desktop/applications/*.desktop; do
ln -sf "$file" /usr/share/applications/
done
```
Añadamos esto en System Settings -> Startup and Shutdown -> Autostart:

No olvidemos poner el archivo como ejecutable:
```CLI
chmod +x <path_to_script>/script.sh
```
Ahora, cuando reinicies, el script creará los enlaces simbólicos si encuentra nuevos archivos .desktop en el directorio /var/lib/snapd/desktop/applications/.

# Conclusión

Otro día, y otro problema en Linux Desktop. Este es un buen sistema operativo para servidores, data centers, HPC. Sin embargo, para las computadoras de escritorio/Desktops los resultados varían y puedes frustrarte al gastar tiempo intentando reparar problemas en funcionalidades simples como instalar un programa y verlo en el menú de Inicio. Esto funciona bien en el sistema operativo del tragaluz. No pensemos en eso tanto. ¡La herramienta correcta con la tarea correcta!

¡Salud!

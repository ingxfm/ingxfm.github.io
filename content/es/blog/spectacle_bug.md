+++
title = 'Spectacle: ¿Cómo reportar un defecto de software (bug)?'
date = 2024-10-11T18:29:10+02:00
draft = false
lang = 'es'
+++

Erase una vez, le moi estaba escribiendo su artículo de los Viernes. Está pobre alma necesitaba unos cuantas imágenes de pantalla, y contemplen usted que su Spectacle no presentó un comportamiento no muy espectacular. Spectacle necesitó de las siguientes acciones para hacer una sola captura de pantalla: abrir Spectacle -> Area: Rectangular Region -> hacer clic en Take a New Screenshot, el puntero del mouse desaparece y no se ve el modo de captura de pantalla, entonces tengo que abrir Spectacle de nuevo desde el menú principal con la tecla Super -> cliquear nuevamente en Take a New Screenshot, entonces pude ver el puntero y el modo de captura de pantalla normalmente. Por lo tanto, por el precio de 2 capturas, conseguí 1. Me hace sentido, ya que estamos cercanos a las ofertas del Black Friday en Noviembre.

En cualquier caso, resolvamos este desbalance de precios, ¿si?

Mi sistema operativo es Kubuntu 22.04 (no quiero el 24.04, por ahora). Ahora, no había tenido este problema, anteriormente. Parece que puede ser un problema causado por una actualización.
```
spectacle --version
```
La versión es:
```
spectacle 21.12.3
```
Podemos usar journalctl para verificar los logs de Spectacle y ver que pasa.

```
journalctl | grep spectacle
```
Los logs indican que hay un error hace unos días con un mimetype. ¿Qué rayos?
```
1. kglobalaccel5[2796]: kf.globalaccel.kglobalacceld: No desktop file found for service  "org.kde.spectacle.desktop"
2. PackageKit[2915]: in /8040_dedcabaa for install-packages package kde-spectacle;21.12.3-0ubuntu1;amd64;+manual:ubuntu-jammy-universe was installing for uid 1000
3. tracker-extract[4598]: Could not get mimetype, Error when getting information for file “/usr/share/applications/org.kde.spectacle.desktop.dpkg-new”: No such file or directory
4. tracker-extract[4598]: Task for 'file:///usr/share/applications/org.kde.spectacle.desktop.dpkg-new' finished with error: Error when getting information for file “/usr/share/applications/org.kde.spectacle.desktop.dpkg-new”: No such file or directory
```
También, podemos ver los logs con el programa Ksystemlogs.

¿Podría ser este la fuente del problema? El sistema no puede saber cual es el mimetype porque el archivo no existe. Quizás, esto está causando el problema cuando abro Spectacle. Re-instalar, desinstalar e instalar de nuevo no ayudó. Ya sé, ya sé, esa es la salida fácil.

Por lo que, decidí crear el archivo manualmente, como no se creaba al re-instalar
```
sudo nano /usr/share/applications/org.kde.spectacle.desktop.dpkg-new
```
Por favor, agrega las siguientes líneas, guarda el archivo (Ctrl + S) y cierra el archivo (Ctrl+X):
```
[Desktop Entry]
Version=21.12.3
Name=Spectacle
GenericName=Screenshot Capture Utility
Comment=Capture screenshots of your desktop or applications
Exec=spectacle %U
Icon=spectacle
Terminal=false
Type=Application
Categories=Graphics;Photography;
MimeType=image/png;image/jpeg;image/jpg;image/bmp;image/x-portable-pixmap;image/x-portable-bitmap;image/x-xbitmap;image/x-xpixmap;image/x-tga;image/x-icns;image/x-ico;image/x-pcx;image/x-png;image/x-xcf;image/x-xwindowdump;
X-KDE-StartupNotify=true
X-KDE-Protocols=file,smb
```
A continuación, actualicemos base de datos del desktop:
```
sudo update-desktop-database
```
Esto tampoco ayudó y decidí cambiar la versión de Spectacle. Para ello, agregue los backports de Kubuntu 22.04:
```
sudo add-apt-repository ppa:kubuntu-ppa/backports
sudo apt update
```
Entonces, instale la versión más actualizada.
```
apt-cache policy kde-spectacle
sudo apt install kde-spectacle=24.08.2-0ubuntu1
```
Esto casi resolvió el problema completamente. Sin embargo, a las 20 capturas de pantalla, presionó el botón Take a New Screenshot, y Spectacle decide no ir al modo de captura y tengo que abrir el programa de nuevo. Tomé una caminata de 5 minutos y recordé que estoy usando una laptop conectada a un monitor, y eso podría estar relacionado al problema.

He aquí, desconecté el monitor y ¡voila!, el problema desapareció.

Esto podría ser un defecto de software (bug). Por lo que, decidí crear un ticket en la comunidad KDE: https://bugs.kde.org.

Crea una cuenta, verifica que tu problema no ha sido reportado. Los detalles solicitados en mi caso son: KDE Plasma version, KDE Frameworks version, Qt version, kernel version, Graphics Platform. 

Para ver la versión de QT:
```
dpkg -l | grep libqt
```

O en Kubuntu, ve a System settings -> About this System.
![alt text](/img/about_this_system_spectacle_bug.png)

# Conclusión
Algunas veces, nos proponemos reparar algo, pero rápidamente nos damos cuenta que no podemos hacerlo por completo porque hay un defecto de software. Reportemos estos casos. Ojalá, la comunidad KDE le echará un ojo a esto. O probablemente ya haya sido resuelto en Kubuntu 22.04. De todas formas, déjame terminar mi artículo del Viernes.

¡Salud!

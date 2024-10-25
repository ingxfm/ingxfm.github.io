+++
title = 'Rotando un video en Linux'
date = 2024-10-25T20:13:09+02:00
draft = false
lang = 'es'
+++

Digamos que estúpidamente grabas un video en vertical, y cuando los copias a tu computadora personal, y el video es guardado de forma horizontal. Entonces, tienes un vídeo vertical, que en si mismo ya es una blasfemia, reproduciéndose horizontalmente. Giremos el vídeo para que podamos continuar nuestra pecaminosa verticalidad. O puedes conseguirte una cita con un doctor para el cuello. ¡Vamos!
![Vertical video rotated wrongly](/img/video_wrong.png)
Usemos el magnifico program ffmpeg.

Hay un llamado parámetro transpose que puedes pasar con los siguientes dígitos:
```
0 = 90° CounterClockwise and Vertical Flip (default) 
1 = 90° Clockwise 
2 = 90° CounterClockwise
3 = 90° Clockwise and Vertical Flip
```
En este caso, queremos girar a contrareloj:

```CLI
ffmpeg -i wrong_direction_video.mp4 -vf "transpose=2" output.mp4
```

El parámetro -i es el nombre del archivo input. El parámetro -vf es un filtro de video y puede ser usado para escalar, cambiar a escala de grises, cortar, voltear entre otras cosas, tus videos. En este caso, solo giraremos el vídeo 90 grados a contrareloj usando "transpose=2".

Si todo sale bien, verás unos logs como estos:
```
frame=  156 fps=8.4 q=-1.0 Lsize=    6247kB time=00:00:05.24 bitrate=9751.1kbits/s speed=0.282x    
video:6194kB audio:46kB subtitle:0kB other streams:0kB global headers:0kB muxing overhead: 0.110241%
[libx264 @ 0x55e393a999c0] frame I:4     Avg QP:23.44  size: 93745
[libx264 @ 0x55e393a999c0] frame P:47    Avg QP:25.88  size: 63735
[libx264 @ 0x55e393a999c0] frame B:105   Avg QP:28.75  size: 28298
[libx264 @ 0x55e393a999c0] consecutive B-frames:  5.1% 12.8%  7.7% 74.4%
[libx264 @ 0x55e393a999c0] mb I  I16..4:  7.1% 78.0% 14.9%
[libx264 @ 0x55e393a999c0] mb P  I16..4:  6.6% 34.2%  3.9%  P16..4: 29.2% 16.4%  5.3%  0.0%  0.0%    skip: 4.5%
[libx264 @ 0x55e393a999c0] mb B  I16..4:  1.8%  6.1%  1.1%  B16..8: 43.1% 13.5%  2.3%  direct: 3.5%  skip:28.4%  L0:50.5% L1:44.7% BI: 4.8%
[libx264 @ 0x55e393a999c0] 8x8 transform intra:74.3% inter:70.0%
[libx264 @ 0x55e393a999c0] coded y,uvDC,uvAC intra: 59.1% 60.9% 14.2% inter: 21.5% 21.1% 0.7%
[libx264 @ 0x55e393a999c0] i16 v,h,dc,p: 40% 21%  3% 36%
[libx264 @ 0x55e393a999c0] i8 v,h,dc,ddl,ddr,vr,hd,vl,hu: 32% 26% 11%  4%  5%  5%  6%  5%  6%
[libx264 @ 0x55e393a999c0] i4 v,h,dc,ddl,ddr,vr,hd,vl,hu: 38% 20% 12%  5%  5%  5%  5%  5%  4%
[libx264 @ 0x55e393a999c0] i8c dc,h,v,p: 49% 19% 24%  8%
[libx264 @ 0x55e393a999c0] Weighted P-Frames: Y:0.0% UV:0.0%
[libx264 @ 0x55e393a999c0] ref P L0: 66.6% 11.4% 17.2%  4.8%
[libx264 @ 0x55e393a999c0] ref B L0: 91.4%  6.6%  2.0%
[libx264 @ 0x55e393a999c0] ref B L1: 97.0%  3.0%
[libx264 @ 0x55e393a999c0] kb/s:9621.14
[aac @ 0x55e393afefc0] Qavg: 1173.531
```
Y este es el resultado con la orientación correcta:
![Corrected orientation](/img/video_corrected.png)

Ahora, puedes continuar con la blasfemia de usar videos verticales.

# Conclusión
Probablemente, un pana de mercadeo rasgará sus vestiduras por mis comentarios en vídeos verticales. Poniendo de lado el fanatismo sobre la orientación del vídeo, usemos la herramienta adecuado para la actividad adecuada. Si necesitas tener estos vídeos verticales y por alguna razón, cuando copiaste los vídeos a tu computadora, estos se giraron, puedes devolverlos a su estado previo. La próxima vez, podemos hablar sobre como evitar el problema en primer lugar, o como crear videos panorámicos a partir de videos verticales, y viceversa. Hasta entonces.

¡Salud!

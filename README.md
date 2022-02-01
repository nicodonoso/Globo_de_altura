## Globo_de_altura
Descripción de un proyecto de lanzamiento de globo de altura

# APRS con el equipo ligth APRS
En un ARDUINO IDE se debe de cargar el programa para globos de altura (HAB) en donde se debe de configurar la señal de llamada y el tiempo entre cada transmisión. 
El código arduino del proyecto completo encuentra en https://github.com/lightaprs/LightAPRS-1.0
y el código para este caso se encuentra en https://github.com/lightaprs/LightAPRS-1.0/tree/master/LightAPRS-hab 

El lightAPRS se puede comprar en http://shop.qrp-labs.com/lightaprs

# Convertir imagen tomada por la raspberry pi en SSTV
Primero clonamos lo necesario para convertir imagenes a SSTV, debemos de abrir una terminal en la raspberry y dar el comando
git clone https://github.com/AgriVision/pisstv

Luego se deben de actualizar y instalar lo siguiente:

sudo apt update

sudo apt install -y libmagic-dev libgd-dev

Para compilar

gcc -o pisstv pisstv.c -lgd -lmagic -lm

ejecutamos:

./pisstv test.png 22050

Se ha creado el archivo test.png.wav

# Trayectoria pronosticada y medida
Habiamos pensado en lanzar hasta 21km de altura, pero por motivos de tiempo el ajuste fino de esto no se pudo realizar antes del lanzamiento y la 
altura alcanzada fue de 23 km. 

Para calcular el pronóstico de la trayectoria usamos https://predict.habhub.org/, la estimación de la altura se realizó con el "Use Burst Calculator" que aparece en esta página.

La trayectoria pronosticada fue:

![Captura de pantalla de 2022-01-18 14-43-42](https://user-images.githubusercontent.com/13879560/151903385-cafa4d72-b497-4340-9b24-05571a5d9e77.png)

La trayectoria media con el APRS fue :

![Captura de pantalla de 2022-01-29 18-04-33](https://user-images.githubusercontent.com/13879560/151903289-3d8b8799-7d6d-451c-8cf8-466def76f385.png)


# Algunas imagenes que obtuvimos con nuestro globo
Al poco tiempo de despegar pudimos ver una imagen del río Valdivia y la llegada de este al mar:

![R36_20220121_230835](https://user-images.githubusercontent.com/13879560/151903649-3023db0e-667f-49d3-aebc-1b59ee8500bc.png)

Fue subiendo y las imagenes fueron espectaculares:

![R36_20220121_231835](https://user-images.githubusercontent.com/13879560/151903777-d1b3997a-befd-48b7-9b7f-9c6039a503ed.png)

El viento hacia mover la carga del globo:

![R36_20220121_235035](https://user-images.githubusercontent.com/13879560/151903860-4c51122a-8152-4dec-a9b7-90c251425cbe.png)

A medida que subía el cielo se va poniendo oscuro a pesar que era de día:

![R36_20220122_001035](https://user-images.githubusercontent.com/13879560/151903925-30f945f2-b35e-4e39-a015-327afc9c1f3d.png)

Poco antes de empezar a caer:

![R36_20220122_004840](https://user-images.githubusercontent.com/13879560/151904035-dae47fd8-e0d8-4f2d-a28b-580075720069.png)

Una vez que el globo explotó igual logramos obtener algunas imagenes. 

## Globo_de_altura
Descripción de un proyecto de lanzamiento de globo de altura

# APRS con el equipo ligth APRS
En un ARDUINO IDE se debe de cargar el programa para globos de altura (HAB) en donde se debe de configurar la señal de llamada y el tiempo entre cada transmisión. 
El código arduino se encuentra en https://github.com/lightaprs/LightAPRS-1.0
y el código para este caso se encuentra en https://github.com/lightaprs/LightAPRS-1.0/tree/master/LightAPRS-hab 

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


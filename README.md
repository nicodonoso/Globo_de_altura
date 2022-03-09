## Globo_de_altura
Descripción de un proyecto de lanzamiento de globo de altura

# Paracaidas 
Hicimos algunos cálculos y encontramos que con un diámetro de 1 metro era suficiente. Utilizamos un paracaída de ejercicio como el de la imagen:
![Captura de pantalla de 2022-01-31 23-34-03](https://user-images.githubusercontent.com/13879560/151905122-1be54c90-f225-4802-ae92-9051a8636357.png)

Fue necesario hacer algunas modificaciones para poder colgar todo desde el globo.

# Globo
Utilizamos un globo de 300 gramos comprado en https://www.kaymont.com/balloons 

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

# Los siguientes pasos son: 

Instalar la cámara de la raspberry y tomar una imagen con el siguiente comando:

raspistill -t 1 --width 320 --height 256 -e png -o /tmp/image.png 

Si esto está operando bien podemos pasar a trabajar en convertir la imagen en un archivo de audio SSTV y lo haremos mediante un código escrito en C que se instala por medio de los siguientes comandos:

Creamos un directorio en donde instalaremos todo lo necesario:

cd
mkdir sstv
cd sstv

Descargamos  con el comando:

git clone https://github.com/AgriVision/pisstv

Y nos arroja esto:

Cloning into 'pisstv'...
remote: Counting objects: 39, done.
remote: Total 39 (delta 0), reused 0 (delta 0), pack-reused 39
Unpacking objects: 100% (39/39), done.
Checking connectivity... done.

Luego instalamos (ojo que demora):

sudo apt-get install libgd2-xpm-dev
sudo apt-get install libmagic-dev 
cd  pisstv/
gcc -lm -lgd -lmagic -o pisstv pisstv.c

Para correr el código vamos al directorio donde se instaló y damos el comando siguiente:

 ./pisstv /tmp/image.png 22050 

El comando nos arrojará algo como esto:

Constants check:
        rate = 22050
        BITS = 16
      VOLPCT = 20
       scale = 6553
     us/samp = 45.351474
     2p/rate = 0.000285
 Checking filetype for file [/tmp/image.png]
 File is a PNG image.
 Input  file is [/tmp/image.png].
 Output file is [/tmp/image.png.wav].  
 Writing audio data to file.
 Got a total of [2589556] samples.
 Done writing to audio file.
 Created soundfile in 4 seconds.

Esto nos dice que se creo un archivo de audio en 4 segundos usando el input: /tmp/image.png y generando un ouput: /tmp/image.png.wav

Ahora podemos reproducir el audio en la raspberry usando un reproductor de audio que se ejecuta por la linea de comando (omxplayer) y lo hacemos así (si quieres escuchar algo debes de tener un audifono o parlante conectado a la raspberry):

omxplayer /tmp/image.png.wav

Para transmitir por radio la imagen debes simplemente conectar la salida de audio de la raspberry a la entrada de microfono de un handy que tenga VOX configurado. En mi caso me funcionó con un handy Baofeng UV5R con VOX=2.

En este punto tenemos todo operativo pero si queremos dejar esto autónomo son necesarios los siguientes pasos. Generaremos un archivo de texto que tenga todo los comandos necesarios para:

    1. Tomar la imagen
    2. Convertirla en SSTV
    3. Reproducir el SSTV

Para esto vamos al directorio donde tenemos operando nuestro sistema SSTV mediante el comando:

cd /home/pi/sstv

y creamos un archivo ejecuta_sstv.sh usando el editor de comando nano:

nano ejecuta_sstv.sh 

Copiamos en el archivo el siguiente texto en rojo:

#!/bin/bash

raspistill -t 1 --width 320 --height 256 -e png -o /tmp/image.png
/home/pi/sstv/pisstv/pisstv /tmp/image.png 22050
sleep 4
omxplayer /tmp/image.png.wav

Lo guardamos tecleando “CTRL+x” (Control y x al mismo tiempo). Luego cambiamos los permisos de este archivo para que el sistema automáticamente lo pueda ejecutar mediante el comando:

chmod 777 ejecuta_sstv.sh 

En este punto generamos un archivo de comandos llamdo “ejecuta_sstv.sh “ que tiene los tres pasos para operar el sistema. Se puede ejecutar para hacer una prueba con el siguiente comando:

./ejecuta_sstv.sh 

El último paso que nos queda es hacer que la raspberry ejecute automáticamente los comandos del archivo ejecuta_sstv.sh. Esto lo realizamos utilizando el Crontab.

Damos el comando:

crontab -e  

La que si aún no tenemos ninguna tarea programada en él nos debería arrojar lo siguiente:

# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command

Bajamos con la flecha del teclado hasta la ultima línea y copiamos lo siguiente
 
*/10 * * * * /bin/bash /home/pi/sstv/ejecuta_sstv.sh

Lo guardamos tecleando “CTRL+x” (Control y x al mismo tiempo).
El crontab se puede revisar mediante el comando crontab –l y nos debe aparecer lo siguiente en donde se subraya lo importante que está en la ultima línea:

# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command

*/10 * * * * /bin/bash /home/pi/sstv/ejecuta_sstv.sh

En donde “*/10” significa que se ejecuta cada 10 minutos. Si, por algún motivo, queremos que esto deje de operar debemos comentar la línea agregando un “#” al inicio de la línea.

Desde este momento la raspoberry estará ejecutando todos los comandos del archivo ejecuta_sstv.sh cada 10 minutos. Si tenemos el handy con VOX configurado podremos hacer la Tx por radio. 


Comentarios: 

    • Recordar que para transmitir estas imágenes se debe de poseer una licencia de radio aficionado.
    • En cada sector existe un plan de bandas, es decir no se puede transmitir una imagen SSTV en cualquier frecuencia. Te debes de informar antes.

    • Dentro de los archivos que descargamos del git hay un programa python que detecta cambios entre dos imágenes consecutivas y genera una transmisión en caso de existir cambios. Es decir opera como un detector de movimientos. Yo no lo he probado.

    • El sistema radio + raspberry se puede energizar por baterías y accionar su operación por un relé con temporizador. 

    • Si se va operar se debe de agregar la señal distintiva del radioaficionado a la imagen transmitida. 

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

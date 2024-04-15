# Practica Integradora con Big Data
Durante esta practica la idea es emular un ambiente de trabajo, desde un área de innovación solicitan construir un MVP(Producto viable mínimo) de un ambiente de Big Data donde se deban cargar unos archivos CSV que anteriormente se utilizaban en un datawarehouse en MySQl, pero ahora en un entorno de Hadoop.

Desde la gerencia de Infraestructura no están muy convencidos de utilizar esta tecnología por lo que no se asigno presupuesto alguna para esta iniciativa, de forma tal que por el momento no es posible utilizar un Vendor(Azure, AWS, Google) para implementar dicho entorno, es por esto que todo el MVP se deberá implementar utilizando Docker de forma tal que se pueda hacer una demo al sector de infraestructura mostrando las ventajas de utilizar tecnologías de Big Data.


Esta practica fue desarrollado en una máquna virtual en VirtualBox con un Servidor Ubuntu ya prediseñado, en el cual ya viene instalado Docker.
Las herramientas que utilice en esta practica son VirtualBox, Putty, WinSCP. 

# Entorno Docker con Hadoop, Spark y Hive
Se pesenta un entorno Docker con Hadoop (HDFS) y la implementación de:

* Spark
* Hive
* HBase
* MongoDB
* Neo4J
* Zeppelin
* Kafka

Es importante mencionar que el entorno completo consume muchos recursos de su equipo, motivo por el cuál, se propondrán ejercicios pero con ambientes reducidos, en función de las herramientas utilizadas.

# Pasos iniciales

Deberiamos ejecutar el siguiente comando para clonar el repositorio que vamos a utilizar:

	git clone https://github.com/javyleonhart/Practica_integradora_herramientasBigData.git

Una vez descargadas las herramientas que utilizaremos, 
entraremos a la carpeta con el comando "cd"

    cd herramientas_big_data

Con el siguiente codigo levantaremos el contenedor correspondiente a la actividad indicada:

    sudo docker-compose -f docker-compose-vX.yml up -d

Nota: Tendremos que reemplazar la X con el numero deseado

## 1) HDFS

Se debe utilizar el entorno docker-compose-v1.yml

    sudo docker-compose -f docker-compose-v1.yml up -d

Una vez inicializado el contenedor, entraremos al namenode

    sudo docker exec -it namenode bash

Nos debemos posicionar en "home" y crearemos el directorio Datasets con el comando mkdir

    cd home
    mkdir Datasets

Luego hacemos la confirmacion de que se creo correctamente 
y salimos.

Para verificar si se creo ponemos ls
    ls
Y para salir ponemos:
    exit

Y copiremos los archivos ubicados en la carpeta Datasets, 
dentro del contenedor "namenode"

Utilizaremos el archivo "Paso00.sh" provisto en los materiales para mover todos los archivos. Para usarlo, primero hay que darle permisos con el comando chmod.

    chmod u+x Paso00.sh

luego ejecutarlo

    ./Paso00.sh

Ubicarse en el contenedor "namenode"

    sudo docker exec -it namenode bash

Crear un directorio en HDFS llamado "dato"

    hdfs dfs -mkdir -p data

Copiar los archivos csv provistos a HDFS:

    hdfs dfs -put /home/Datasets/* data

En este proceso creamos una carpeta llamada data y copiando de los archivos.

Para verificando si se ejecuto correctamente podemos entrar al hdfs namenode mediante 

    http://<IP_Anfitrion>:9870

Nota: Debemos cambiar la parte de "<IP_Anfitrion>" por la ip de la maquina virtual.

## 2) Hive

Para esta actividad deberiamos utilizar el entorno docker-compose-v2.yml

	sudo docker-compose -f docker-compose-v2.yml up -d

Con el comando anterior creamos un entorno con Hive 

Crearemos tablas en Hive, a partir de los csv ingestados en HDFS.

Para esto, tendriamos que ubicarnos dentro del contenedor Hive y ejecutar desde allí los scrips necesarios.

Antes que nada tendremos que pasar el archivo Paso02.hql al servidor de hive con el siguiente comando:

    sudo docker cp ./Paso02.hql hive-server:/opt/

Ahora nos metemos a Hive:

	sudo docker exec -it hive-server bash

Una vez dentro tendremos que ejecutar el archivo hql de la siguiente manera:

	hive -f Paso02.hql

Ahora tendremos que ingresar al entonrno hive con el siguiente comando:

    hive

Nota: Saber que dentro de hive solo acepta Querys entonces a la hora de hacer un comando sin ";" el mismo servidor te manda a la siguiente linea porque espera que vos termines de escribir un comando.

Para salir de hive tendremos que pone "exit;"

## 3) Formato de Almacenamiento

Trabajaremos con las tablas creadas en el punto 2, los archivos en formatos tipo csv los almacenaremos en formato Parquer con una compresion de tipo Snappy.

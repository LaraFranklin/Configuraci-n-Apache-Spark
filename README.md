# Configuración-Apache-Spark
 
 A continuación se presenta una descripción de las configuraciones necesarias para la instalación de Apache Spark en Ubuntu 16.04.
 
 En esta práctica se realiza la configuración de **Apache Spark**  para realizar un **cluster** con N PC´s.
 
 ***
 
 **Se debe seguir los siguientes pasos.**

1. Se descarga apache spark desde la página oficial <https://spark.apache.org/> version 2.4.0

2. Se instaló última versión de java, scala, python. Se recomienda utilizar la versión de python 3.6 

3. El master instala Open SSH Server-Client, ésto nos permite controlar o transferir archivos de forma remota a computadoras. OpenSSH proporciona un servidor daemon y herramientas de cliente para facilitar el control remoto encriptado seguro y las operaciones de transferencia de archivos.

`sudo apt-get install openssh-server` 

Comprobamos que nuestro servicio esta activo

```
 sudo service ssh status
```

Tenemos que permitir la comunicación del ssh por el puerto 24, por lo que ingresamos a al archivo sshd_config, vamos a utilizar nano.

```
 sudo nano /etc/ssh/sshd_config
```
y descomentamos la linea del archivo que tenga **port 24**

Ahora es importante reiniciar el servicio

```
 sudo service ssh restart
```

Generamos nuestra clave pública y privada para la comunicación.

```
 ssh-keygen -t rsa -P ""

```


4. Se modifica el archivo /etc/hosts en slaves y master. Los slaves son los encargados de realizar los trabajos y el master el que va a delegar los resultados, en esta ocación tenemos un red de clase C. Se configura la ip y nombre de la máquina

```
192.168.0.5 master
192.168.0.9 slave1
```
Comprobamos si tenemos conexión conesta con los slaves, por lo que hacemos un ping. Tenemo que tener ping entre las máquinas.

```
ping slave1
ping master
```

5. Se comparte la clave ssh desde el master a los slaves, no es necesario pero no hace ningun daño compartir los la clave virtual desde los slaves hasta los master.

en esta ocación vamos a pasar las claves del master hacia los slaves.
```
ssh-copy-id -i ~/.ssh/id_rsa.pub usuario@slave1
```
y adicional copiamos a nuestra propia máquina
```
cp .ssh/id_rsa.pub .ssh/authorized_keys
```

para comprobar si tenemos conexión, en el terminal ingresamos 


```
ssh usuario@slave1
```
una vez comprobada, salimos ingresando 

```
exit
```


6. Instalamos scala 2.11.6, con el siguiente comando
```
sudo apt-get install scala2.11.6
```

7. Se descomprime y se mueve la carpeta de spark a /usr/local

```
tar xvf /home/usuario/Descargas/spark-2.4.0-bin-hadoop2.7.tgz
sudo mv spark-2.4.0-bin-hadoop2.7 /usr/local/spark

```

7. Se edita el ~/.bashrc agregando el path de spark, ésto se debe realizar en todas las máquinas, tanto en slaves como master
El master edita el archivo spark-env.sh localizado en /usr/local/spark/conf
```
export SPARK_MASTER_HOST=’192.168.0.5’
export SPARK_HOME='/usr/local/spark’'
export JAVA_HOME='/usr/local/jvm/java-8-oracle/’

```

y recargamos la configuración

```
source ~/.bashrc
```
8. Vamos a darle permisos de propietario a nuestro usuario, que en nuestro caso se llama usuario y el grupo hadoop.

```
sudo chown -R hduser:hadoop /usr/local/spark/
```

9. En /usr/local/spark/conf el master agrega sus slaves, en esta ocación en importante tener en cuenta que si se tiene usuarios con diferentes nombres, se tiene que establecer de la siguiente forma **usuario@slave** o **usuario@master**.

```
master
slave1
slave2

```

10. Para correr el servicio, ingresamos al directorio /usr/local/spark/sbin y arrancamos el cluster 

```
start-all.sh
```
o dentro corremos el siguiente comando

```
/usr/local/spark$ bin/spark-shell
```
11. Vamos a ver si esta corriendo los procesos, ingresamos el comando jps en el terminal para ver la lista los procesos de spark, en el master se debe ver los procesos master y worker y en el slave debe tener el worker

```
jps
```
12. Para ver como trabaja en gráficos podemos verle en Master http://master:8080/ permite ver los workers, donde se ve el tráfico.


***
Ahora les dejamos un enlace para que puedan probar como trabaja apache spark.

[ejemplo](<http://www.godatafy.com/tech-blog/apache-spark-word-count-program/>)

Se puede utilizar la siguiente base de datos <https://drive.google.com/file/d/1cCPYdJBEbb9TZ7LJ6Nq9kvVSDXrxZa_g/view>

Ahora les voy  poner el ejemplo para contar el número de vuelos entre dos aeropuertos con una base de datos libre, esta base de datos ha sido modificada para tener las entradas como "AeropuertoSalida,AeropuertoLlegada,mes", en donde podemos aplicar el siguiente código dentro de python y nos retorna la solución esperada. Para obtener esta base de datos descargar los datos desde 

```Python
text_file = sc.textFile("/home/lara/Descargas/vuelos")
counts = text_file.flatMap(lambda line: line.split("/n")).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a + b)
counts.collect()

```
***

Para finalizar, les vamos a dejar a disposición una máquina virtual para que se descarguen la cual ya cuenta con las configuraciones para un slave, para convertirle en master univamenete agregar el archivo ../spark/conf 

descargar máquina virtual desde Drive
<https://drive.google.com/file/d/10tr3z_HLS9E3zouY8PvcLLua9CNPVI2G/view?usp=sharing>

Notas importantes para la importar la máquina virtual dentro de Virtual Box

Recursos: Virtual Box
Importar la máquina virtual, dejar todo por defecto y marcar la casilla Reinicializar la dirección MAC localizada en la parte inferior.

La configuración de la red establecer en modo puente para tener una ip de la misma red que nuestro supervisor

desde el archivo /etc/hosts, en el nombreMáquina colocar un nombre que identifique su computadora del resto del cluster

```
127.0.0.1 localhost
127.0.1.1 nombreMáquina
```

Además, hay que configurar el nombre de la máquina desde las configuraciones. se recomienda establecer el mismo nombre.


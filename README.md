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
 ssh-keygen -b 4096 -t rsa
```


4. Se modifica el archivo /etc/hosts en slaves y master. Los slaves son los encargados de realizar los trabajos y el master el que va a delegar los resultados, en esta ocación tenemos un red de clase C. Se configura la ip y nombre de la máquina

```
192.168.0.5 master
192.168.0.9 slave1
```

5. Se comparte la clave ssh desde el master a los slaves, no es necesario pero no hace ningun daño compartir los la clave virtual desde los slaves hasta los master.

en esta ocación vamos a pasar las claves del master hacia los slaves.
```
ssh-copy-id usuario@slave1
```

para comprobar si tenemos conexión, en el terminal ingresamos 


```
ssh usuario@slave1
```

6. Se descomprime y se mueve la carpeta de spark a /usr/local

```


```

7. Se edita el ~/.bashrc agregando el path de spark, ésto se debe realizar en todas las máquinas, tanto en slaves como master

```

El master edita el archivo spark-env.sh localizado en /usr/local/spark/conf
export SPARK_MASTER_HOST=’192.168.0.5’
export JAVA_HOME='/usr/local/jvm/java-8-oracle/’

```

8. En /usr/local/spark/conf el master agrega sus slaves, en esta ocación en importante tener en cuenta que si se tiene usuarios con diferentes nombres, se tiene que establecer de la siguiente forma **usuario@slave** o **usuario@master**.

```
master
slave1
slave2

```

9. Para correr el servicio, ingresamos al directorio /usr/local/spark/sbin y arrancamos el cluster 

```
start-all.sh
```
10. Vamos a ver si esta corriendo los procesos, ingresamos el comando jps en el terminal para ver la lista los procesos de spark, en el master se debe ver los procesos master y worker y en el slave debe tener el worker

```
jps
```
11. Para ver como trabaja en gráficos podemos verle en Master http://master:8080/ permite ver los workers, donde se ve el tráfico.

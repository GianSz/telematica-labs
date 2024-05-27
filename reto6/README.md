## Usando el hdfs

Primero debemos ingresar a hue en el servicio de hive, y cerciorarnos de que la sesión que se esté usando sea 'hive'

![](./images/sesion.jpeg)

![](./images/sesion2.png)

Estando aquí dentro ejecutamos el siguiente código SQL para crear la tabla 'hdi'

```bash
CREATE TABLE HDI (id INT, country STRING, hdi FLOAT, lifeex INT, mysch INT, eysch INT, gni INT)  
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
STORED AS TEXTFILE 
```

Deberíamos obtener un resultado como el siguiente, donde al lado izquierdo podemos ver la nueva tabla creada:

![](./images/creacion-hdi.png)

Si hacemos select de esta tabla que se acabó de crear vemos que no hay ningún resultado: 

![](./images/select-hdi.png)

Así pues, para poblar nuestra tabla nos dirigimos a la pestaña ‘files’ y a la carpeta ‘/user/hive/warehouse/’, allí veremos que tenemos la carpeta hdi creada:

![](./images/hdi-carpeta.png)

Dentro de esta carpeta debemos subir el archivo ‘hdi-data2.csv’ que se encuentra en la carpeta ‘bigdata/datasets/onu/hdi’ del repositorio de github de la materia

![](./images/hdi-data2-subido.png)

Ahora, si hacemos un select de la tabla ‘hdi’ obtendremos todos los registros de la tabla (los cuales fueron rellenados usando el csv que acabamos de subir) 

![](./images/select-hdi-correcto.png)

Probemos ahora ejecutando unas consultas y cálculos para ver que todo está funcionando correctamente:

![](./images/query1-hdi-hdfs.png)

![](./images/query2-hdi-hdfs.png)

Ahora, creamos una tabla llamada ‘expo’ de la siguiente manera:

```bash
CREATE EXTERNAL TABLE EXPO (country STRING, expct FLOAT)  
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','  
STORED AS TEXTFILE  
LOCATION 's3://bucket-lab0/datasets/onu/export/' 
```

Deberíamos obtener un resultado como el siguiente, donde al lado izquierdo podemos ver la nueva tabla creada

![](./images/creacion-expo.png)

Después, haremos la ejecutaremos la siguiente consulta (la cuál es un join entre la tabla 'hdi' y la nueva tabla 'expo')

![](./images/join-hdi-expo.png)


Ahora ejecutamos el siguiente código SQL para crear la tabla 'docs'

```bash
CREATE EXTERNAL TABLE docs (line STRING) 
STORED AS TEXTFILE 
LOCATION 'user/hadoop/datasets/gutenberg-small/';
```
Deberíamos obtener un resultado como el siguiente, donde al lado izquierdo podemos ver la nueva tabla creada

![](./images/creacion-docs.png)

Luego ejecutamos la siguiente consulta para obtener el conteo de palabras ordenado alfabéticamente

![](./images/query1-docs.png)

Por último ejecutamos la siguiente consulta para obtener el conteo de palabras ordenado por frecuencia

![](./images/query2-docs.png)


## Usando el S3 Bucket

Realizando los siguientes passo logramos lo mismo que lograríamos haciendo los anteiores, lo único que cambia es que los datos ahora no serán obtenidos usando el hdfs, sino directamente desde el Bucket de S3 que fue creado en pasados laboratorios y el cual tiene los archivos de los datasets (si no los tiene, debe subirlos allí).

Primero debemos ingresar a hue en el servicio de hive, y cerciorarnos de que la sesión que se esté usando sea 'hive'

![](./images/sesion.jpeg)

![](./images/sesion2.png)

Estando aquí dentro ejecutamos el siguiente código SQL para crear la tabla 'hdi2'

```bash
CREATE EXTERNAL TABLE HDI2 (id INT, country STRING, hdi FLOAT, lifeex INT, mysch INT, eysch INT, gni INT) 
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
STORED AS TEXTFILE 
LOCATION 's3://bucket-lab0/datasets/onu/hdi/'
```

Deberíamos obtener un resultado como el siguiente, donde al lado izquierdo podemos ver la nueva tabla creada:

![](./images/creacion-hdi2.png)

Ahora, si hacemos un select de la tabla ‘hdi2’ obtendremos todos los registros de la tabla (los cuales fueron insertados usando el csv del archivo almacenado en un Bukcet S3 en la ubicación indicada en el SQL)

![](./images/select-hdi2.png)

Probemos ahora ejecutando unas consultas y cálculos para ver que todo está funcionando correctamente:

![](./images/query1-hdi2.png)

![Segunda query de prueba de hdi2](./images/query2-hdi2.png)

Ahora, creamos una tabla llamada ‘expo’ de la siguiente manera:

```bash
CREATE EXTERNAL TABLE EXPO (country STRING, expct FLOAT)  
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','  
STORED AS TEXTFILE  
LOCATION 's3://bucket-lab0/datasets/onu/export/' 
```

Deberíamos obtener un resultado como el siguiente, donde al lado izquierdo podemos ver la nueva tabla creada

![](./images/creacion-expo.png)

Después, haremos la ejecutaremos la siguiente consulta (la cuál es un join entre la tabla 'hdi2' y la nueva tabla 'expo')

![](./images/join-hdi2-expo.png)


Ahora ejecutamos el siguiente código SQL para crear la tabla 'docs2' (la cual va a obtener los datos desde el Bucket S3)

```bash
CREATE EXTERNAL TABLE docs (line STRING) 
STORED AS TEXTFILE 
LOCATION 's3://bucket-lab0/datasets/gutenberg-small/';
```

Deberíamos obtener un resultado como el siguiente, donde al lado izquierdo podemos ver la nueva tabla creada

![Creacion de la tabla docs2](./images/creacion-docs2.png)

Luego ejecutamos la siguiente consulta para obtener el conteo de palabras ordenado alfabéticamente

![](./images/query1-docs2.png)

Por último ejecutamos la siguiente consulta para obtener el conteo de palabras ordenado por frecuencia

![](./images/query2-docs2.png)


## Reto propuesto en el laboratorio

Creamos una tabla que va a obtener y subir los datos usando hadoop en la direccipón `/user/hadoop/datasets/reto`

![](./images/creacion-wordcount.png)

Luego, ejecutamos el siguiente código SQL el cual insertará el resultado de una consulta (en este caso el de la frecuencia de cada palabra ordenada de mayor a menor) en la tabla que acabamos de crear

```bash
INSERT OVERWRITE TABLE wordcount
SELECT word, count(1) AS count
FROM (
    SELECT explode(split(line, ' ')) AS word
    FROM docs
)
GROUP BY word
ORDER BY count DESC;
```

![](./images/query-wordcount.png)

Ahora, si hacemos un select para observar los registros que tenemos en la tabla 'wordcount' veremos cada palabra con su frecuencia respectiva y además ordenado de mayor a menor:

![](./images/select-wordcount.png)

También podremos ver como en la carpeta `/user/hadoop/datasets/` se ha creado la carpeta `reto` donde si entramos vamos a encontrar los datos de la tabla:

![](./images/carpeta-reto.png)

![](./images/archivos-reto.png)
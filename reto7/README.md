## Ejecución con jupyter

Primero ingresamos a hue en la pestaña de S3, esto lo haremos para subir el cuaderno de jupyter al Bucket (aún así cabe recalcar que no es necesario ingresar al hue, sino quie también se podría hacer desde la interfaz de aws, sin embargo el paso a paso se hizo desde hue)

![](/images/s3-hue.png)

Luego, ingresamos a la carpeta `/jupyter/jovyan` del `bucket-lab0` y allí subiremos el cuaderno de jupyter llamado `Data_processing_using_PySpark.ipynb` el cuál se encuentra en esta carpeta (este es una modificación al cuaderno de Data_processing del repositorio general de la materia, debido a que fue necesario añadir ciertas lineas de código para que todo funcionara correctamente).

![](/images/archivo-data-subido.png)

Después de haber subido el archivo lo que haremos será ingresar a jupyterhub para empezar a ejecutar el archivo

![](/images/login-jupyterhub.png)

Al ingresar encontraremos el login, allí en el usuario pondremos `jovyan` y en la contraseña pondremos `jupyter`. Después de ingresar correctamente nos encontraremos con los cuadernos de jupyter que hayamos subido con anterioridad, en nuestro caso entonces veremos el archivo `Data_processing_using_PySpark.ipynb`

![](/images/archivo-jupyterhub.png)

Damos clic sobre el archivo y ya estaríamos viendo el código del cuaderno. Simplemente bastaría con comenzar a ejecutar el cuaderno bloque por bloque para ir viendo las salidas del programa. Se debería ver así:

![](/images/jupyter1.png)

![](/images/jupyter2.png)

![](/images/jupyter3.png)

![](/images/jupyter4.png)

![](/images/jupyter5.png)

![](/images/jupyter6.png)

![](/images/jupyter7.png)

![](/images/jupyter8.png)

![](/images/jupyter9.png)

![](/images/jupyter10.png)

![](/images/jupyter11.png)

Por último, ya podemos ver como después de ejecutar todo el código del cuaderno de jupyter se han creado dos nuevas carpetas(df_csv y df_parquet) en la carpeta datasets de nuestro Bucket S3, las cuales contienen datos obtenidos en las consultas y demás operaciones realizadas en el código.

![](/images/s3-nuevos-archivos.png)

---

IMPORTANTE!! Para que algunos bloques funcionaran se hicieron las siguientes modificaciones al cuaderno de jupyter original:

1. Primero debemos agregar al cominezo de todo un bloque con el siguiente código

```bash
%%configure -f
{ "conf":{
"spark.pyspark.python": "python3",
"spark.pyspark.virtualenv.enabled": "true",
"spark.pyspark.virtualenv.type":"native",
"spark.pyspark.virtualenv.bin.path":"/usr/bin/virtualenv"
}}
```

Esto nos permitirá configurar unas variables de entorno necesarias para la ejecución correcta del todo los bloques del cuaderno (es decir, el programa en su totalidad)

2. Añadir también las siguientes dos lineas de código:

```bash
sc.install_pypi_package("pandas==1.0.5")
sc.install_pypi_package("pyarrow")
```

Sin embargo, hay que tener en cuenta que estas no pueden ser añadidads en cualquier lugar, sino que deben estar ubicadas después del bloque que dice:

```bash
#create python function
def remaining_yrs(age):
    yrs_left=100-age

    return yrs_left
```

Y antes del bloque que dice:

```bash
#create udf using python function
length_udf = pandas_udf(remaining_yrs, IntegerType())
#apply pandas udf on dataframe
df.withColumn("yrs_left", length_udf(df['age'])).show(10,False)
```

Se debería ver algo así:

![](/images/instalaciones-paquetes.png)

3. Por último se modifico el bloque donde se obtenía el direcotrio de trabajo haciendo `pwd` ya que no funcionaba, y se cambio por este código:

```bash
#current working directory
import os
current_dir = os.getcwd()
print("Current working directory:", current_dir)
```

## Ejecución con Google colab

Lo pimero que debemos hacer es ingresar a google colab e importar el archivo `Data_processing_using_PySpark_google_colab.ipynb`. Luego lo siguiente que debemos hacer es dentro de `/content/gdrive/MyDrive/` crear una carpeta para alojar los datos que vayamos a usar, en este caso se creó la carpeta llamada `datasets` y se subió el archivo `sample_data.csv` (el cuál es el mismo archivo utilizado en el cuaderno de jupyter para hacer las operaciones)

![](/images/carpeta-nueva-google.png)

Luego, debemos cambiar la ruta de donde se está obteniendo los datos y poner la ruta de la carpeta con el archvo que acabamos de crear:

![](/images/ruta-archivo-colab.png)

Luego ejecutamos todo el código de la misma manera que lo hicimos como con el cuaderno de jupyter y ya podríamos ver todos los resultados que se muestran en cada bloque del cuaderno. Además podemos ver como en el bucket S3 en la carpeta `datasets` se han creado las carpetas `df_csv_colab` y la carpeta `df_parquet_colab`, las cuales contienen datos obtenidos en las consultas y demás operaciones realizadas en el código.
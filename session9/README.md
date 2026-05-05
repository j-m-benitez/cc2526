# Session 9: Hadoop: HDFS y MapReduce

Textos origianles de Manuel Parra <manuelparra@decsai.ugr.es> y José Manuel Benítez <jm.benitez@decsai.ugr.es>

Concontribuciones de Carlos Cano <carloscano@ugr.es>

Tabla de contenido:

* [Introducción a Hadoop](#introduction-to-hadoop)
* [Setting up Hadoop and connecting](#setting-up-hadoop-and-connecting)
   * [Setting up a hadoop test install locally or on our current server with docker](#setting-up-a-hadoop-test-install-locally-or-on-our-current-server-with-docker)
* [Working with HDFS](#working-with-hdfs)
   * [Connecting to the HDFS of your cluster](#connecting-to-the-hdfs-of-your-cluster)
      * [Connecting to the local emulation](#connecting-to-the-local-emulation)
   * [HDFS basics](#hdfs-basics)
   * [HDFS storage space](#hdfs-storage-space)
   * [Usage HDFS](#usage-hdfs)
   * [Exercises](#exercises)
   * [References:](#references)
* [Working with Hadoop Map-Reduce](#working-with-hadoop-map-reduce)
   * [Structure of Map-Reduce code](#structure-of-map-reduce-code)
      * [Mapper](#mapper)
      * [Reducer](#reducer)
      * [Main](#main)
   * [Word Count example](#word-count-example)
   * [Running Hadoop applications](#running-hadoop-applications)
   * [Results](#results)
   * [Calculate MIN of a row in Hadoop](#calculate-min-of-a-row-in-hadoop)
   * [Compile MIN in Hadoop](#compile-min-in-hadoop)
   * [Word Count example for Hadoop in Python:](#word-count-example-for-hadoop-in-python)

# Introducción a Hadoop

Hadoop es un marco trabajo de código abierto desarrollado por la Apache Software Foundation que permite el procesamiento distribuido de grandes conjuntos de datos a través de clústeres de ordenadores utilizando modelos de programación sencillos. Está diseñado para ampliarse desde un único servidor hasta miles de máquinas, cada una de las cuales ofrece capacidad de cálculo y almacenamiento local. En esencia, Hadoop está concebido para gestionar enormes cantidades de datos de una manera tolerante a fallos, fiable y rentable, lo que lo hace especialmente adecuado para aplicaciones de big data.

El ecosistema de Hadoop se compone de varios módulos clave. Los dos principales son el **Sistema de Archivos Distribuidos de Hadoop (HDFS)**, que proporciona acceso de alto rendimiento a los datos, y **MapReduce**, un modelo de programación para el procesamiento paralelo de datos. HDFS almacena los datos en grandes bloques repartidos por múltiples nodos, lo que garantiza la redundancia y la disponibilidad. MapReduce, por su parte, procesa los datos en paralelo dividiendo las tareas en funciones de «map» y «reduce».

Más allá de sus componentes básicos, Hadoop incluye un amplio ecosistema de herramientas y marcos de trabajo, como Hive (para consultas de tipo SQL), Pig (para la transformación de datos) y YARN (para la gestión de recursos). Gracias a su capacidad para procesar grandes volúmenes de datos estructurados y no estructurados, Hadoop se ha convertido en una tecnología fundamental en muchos sectores.

## Configuración de hadoop en local con contenedores


Puedes configurar una instalación de prueba de Hadoop localmente o en el servidor que hemos estado utilizando hasta ahora, con el siguiente archivo `docker-compose.yaml`.

```
services:
  namenode:
    image: docker.io/bde2020/hadoop-namenode:2.0.0-hadoop3.2.1-java8
    container_name: namenode
    ports:
      - "9870:9870"  # HDFS Web UI
    environment:
      - CLUSTER_NAME=test
      - HDFS_CONF_dfs_namenode_datanode_registration_ip___hostname___check=false
    volumes:
      - namenode:/hadoop/dfs/name

  datanode:
    image: docker.io/bde2020/hadoop-datanode:2.0.0-hadoop3.2.1-java8
    container_name: datanode
    ports:
      - "9864:9864"  # DataNode Web UI
    environment:
      - CLUSTER_NAME=test
      - CORE_CONF_fs_defaultFS=hdfs://namenode:8020
    volumes:
      - datanode:/hadoop/dfs/data
    depends_on:
      - namenode

volumes:
  namenode:
  datanode:  
```

Has de editar la sección `ports` para cambiar los puertos 9870 y 9864 por otros del rango que se te ha asignado.

El conjutno se puede ejecutar con (podman):

```bash
podman-compose up -d
```

En el caso de usar docker el mandato sería `docker compose up -d`.

A continuación, puedes comprobar si todo funciona correctamente accediendo con un navegador web a las siguientes direcciones URL:

- **HDFS NameNode UI**: [http://localhost:9870](http://localhost:9870)
- **DataNode UI**: [http://localhost:9864](http://localhost:9864)



# Trabajando con HDFS


El Sistema de Archivos Distribuido de Hadoop (HDFS) es un sistema de archivos distribuido diseñado para ejecutarse en hardware estándar. Presenta muchas similitudes con los sistemas de archivos distribuidos existentes. Sin embargo, las diferencias con respecto a otros sistemas de archivos distribuidos son significativas. HDFS es altamente tolerante a fallos y está diseñado para implementarse en hardware de bajo coste. HDFS proporciona un acceso de alto rendimiento a los datos de las aplicaciones y es adecuado para aplicaciones que tienen grandes conjuntos de datos. HDFS relaja algunos requisitos POSIX para permitir el acceso en streaming a los datos del sistema de archivos. HDFS se creó originalmente como infraestructura para el proyecto del motor de búsqueda web Apache Nutch. HDFS es ahora un subproyecto de Apache Hadoop. La URL del proyecto es http://hadoop.apache.org/. Este material se ha elaborado a partir del manual de referencia de HDFS: https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/ . 

![HDFS](http://www.glennklockwood.com/data-intensive/hadoop/hdfs-magic.png)

HDFS tiene una arquitectura maestro/esclavo. Un clúster HDFS consta de un único NameNode, un servidor maestro que gestiona el espacio de nombres del sistema de archivos y regula el acceso a los archivos por parte de los clientes. Además, hay varios DataNodes, normalmente uno por cada nodo del clúster, que gestionan el almacenamiento asociado a los nodos en los que se ejecutan. HDFS expone un espacio de nombres del sistema de archivos y permite almacenar los datos de los usuarios en archivos. Internamente, un archivo se divide en uno o más bloques y estos bloques se almacenan en un conjunto de DataNodes. El NameNode ejecuta operaciones del espacio de nombres del sistema de archivos, como abrir, cerrar y renombrar archivos y directorios. También determina la asignación de bloques a los DataNodes. Los DataNodes se encargan de atender las solicitudes de lectura y escritura de los clientes del sistema de archivos. Los DataNodes también realizan la creación, eliminación y replicación de bloques siguiendo las instrucciones del NameNode.


![HDFS Arch](https://hadoop.apache.org/docs/r1.2.1/images/hdfsarchitecture.gif)

**Replicación de datos**

HDFS está diseñado para almacenar de forma fiable archivos de gran tamaño repartidos entre los equipos de un clúster de gran tamaño. Almacena cada archivo como una secuencia de bloques; todos los bloques de un archivo, excepto el último, tienen el mismo tamaño. Los bloques de un archivo se replican para garantizar la tolerancia a fallos. El tamaño de los bloques y el factor de replicación se pueden configurar para cada archivo. Una aplicación puede especificar el número de réplicas de un archivo. El factor de replicación se puede especificar en el momento de crear el archivo y se puede modificar posteriormente. Los archivos en HDFS son de escritura única y solo pueden tener un escritor en cada momento.

![DataNodes](https://hadoop.apache.org/docs/r1.2.1/images/hdfsdatanodes.gif)


## Conexión al cluster HDFS

### Conexión al despliegue local

Desde una shell te conectas al contenedor lanzado con:

```bash
podman exec -it namenode bash
```

Ahora podemos crear una estructura de archivos:

```
hdfs dfs -mkdir /user
hdfs dfs -mkdir /user/CCSA/
hdfs dfs -chown spark:spark /user/CCSA
```


## Mandatos básicos

La gestión de los archivos en HDFS funciona de forma diferente a la de los archivos del sistema local. El sistema de archivos se almacena en un espacio especial destinado a HDFS. La estructura de directorios de HDFS es la siguiente:

```
/tmp     Temp storage
/user    User storage
/usr     Application storage
/var     Logs storage
```

## Espacio de almacenamiento HDFS

Cada usuario debería tener su carpeta propia en ``/user/``. Por ejemplo, el con nombre mcc50600265 en HDFS debe tener:

```
/user/CCSA/mcc50600265/
```

¡Atención! El espacio de almacenamiento de HDFS es diferente del espacio de almacenamiento local del usuario en docker.ugr.es
```
/user/CCSA2/mcc50600265/  NOT EQUAL /home/mcc506000265/
```


## Uso de  HDFS

```
hdfs dfs <commands>
```

Los mandatos son (versión simplificada):

```
-ls         List of files 
-cp         Copy files
-rm         Delete files
-rmdir      Remove folder
-mv         Move files or rename
-cat        Similar to Cat
-mkdir      Create a folder
-tail       Last lines of the file
-get        Get a file from HDFS to local
-put        Put a file from local to HDFS
```

Listar el contenido de na carpeta:

```
hdfs dfs -ls /user/CCSA2425/mcc50600265
```

Crear un fichero:

```
echo "HOLA HDFS" > fichero.txt
```

Copiar un ``fichero.txt`` del esapcio local a HDFS:

```
hdfs dfs -put fichero.txt /user/your-username/.
```

Comprobamos:

```
hdfs dfs -ls /user/your-username
```

Crear una carpeta de prueba:

```
hdfs dfs -mkdir /user/your-username/test
```

Mover ``fichero.txt`` a la carpeta de prueba:

```
hdfs dfs -mv /user/your-username/fichero.txt /user/your-username/test/.
```

Mostrar el contenido:

```
hdfs dfs -cat /user/your-username/test/fichero.txt
```

Borrar fichero y carpeta:

```
hdfs dfs -rm -skipTrash /user/your-username/test/fichero.txt
```

y 

```
hdfs dfs -rmdir /user/your-username/test
```

Crear dos ficheros:

```
echo "HOLA HDFS 1" > f1.txt
```

```
echo "HOLA HDFS 2" > f2.txt
```

Almacenar en HDFS:

```
hdfs dfs -put f1.txt /user/your-username/.
```

```
hdfs dfs -put f2.txt /user/your-username/.
```

Concatenarlos:

```
hdfs dfs -getmerge /user/your-username/ merged.txt
```

## Ejercicios

- Crea 5 archivos en tu espacio local:
  - part1.dat, part2.dat, part3.dat, part4.dat, part5.dat
- Cópialos a HDFS
- Crea la siguiente estructura de carpetas:
  - /test/p1/
  - /train/p1/
  - /train/p2/
- Copia part1 en /test/p1/ y part2 en /train/p2/ 
- Mueve part3 y part4 a /train/p1/
- Finalmente, fusiona folder /train/p2 y alamacénalo como data_merged.txt


## Referencias:

- http://www.glennklockwood.com/data-intensive/hadoop/overview.html


# Trabajando con Hadoop Map-Reduce

Este es ejemplo es para Hadoop con Java: 3.2.1. Para ejemplos con python [consulta este enlace
(#word-count-example-for-hadoop-in-python)

## Estructura del código

La estructura principal consiste en un mapeador y un reductor, que presentaremos a continuación.

### Mapper


Asigna los pares clave/valor de entrada a un conjunto de pares clave/valor intermedios.
Los Maps son tareas individuales que transforman los registros de entrada en registros intermedios. No es necesario que los registros intermedios transformados sean del mismo tipo que los registros de entrada. Un par de entrada determinado puede asignarse a cero o a muchos pares de salida.

El marco Hadoop Map-Reduce genera una tarea de mapeo por cada InputSplit generado por el InputFormat del trabajo. Las implementaciones de mapeo pueden acceder a la configuración del trabajo a través de JobContext.getConfiguration().

```
public class TokenCounterMapper 
     extends Mapper<Object, Text, Text, IntWritable>{
    
   private final static IntWritable one = new IntWritable(1);
   private Text word = new Text();
   
   public void map(Object key, Text value, Context context) throws IOException, InterruptedException {
     StringTokenizer itr = new StringTokenizer(value.toString());
     while (itr.hasMoreTokens()) {
       word.set(itr.nextToken());
       context.write(word, one);
     }
   }
 }
```

### Reducer

Reduce un conjunto de valores intermedios que comparten una clave a un conjunto más pequeño de valores.

El reductor tiene tres fases principales:

- Shuffle: el reductor copia la salida ordenada de cada mapeador mediante HTTP a través de la red.
- Sort: el marco realiza una ordenación por fusión de las entradas del Reducer por claves (ya que diferentes Mappers pueden haber generado la misma clave). Las fases de shuffle y sort se producen simultáneamente, es decir, mientras se recogen las salidas, estas se fusionan. Para realizar una ordenación secundaria de los valores devueltos por el iterador de valores, la aplicación debe ampliar la clave con la clave secundaria y definir un comparador de agrupación. Las claves se ordenarán utilizando la clave completa, pero se agruparán utilizando el comparador de agrupación para decidir qué claves y valores se envían en la misma llamada a reduce. El comparador de agrupación se especifica mediante Job.setGroupingComparatorClass(Class). El orden de clasificación se controla mediante Job.setSortComparatorClass(Class). 
- Reduce: En esta fase se invoca el método reduce(Object, Iterable, org.apache.hadoop.mapreduce.Reducer.Context) para cada <clave, (colección de valores)> de las entradas ordenadas.

La salida de la tarea de reducción se escribe normalmente en un RecordWriter mediante TaskInputOutputContext.write(Object, Object).

```
public class IntSumReducer<Key> extends Reducer<Key,IntWritable,
                                                 Key,IntWritable> {
   private IntWritable result = new IntWritable();
 
   public void reduce(Key key, Iterable<IntWritable> values,
                      Context context) throws IOException, InterruptedException {
     int sum = 0;
     for (IntWritable val : values) {
       sum += val.get();
     }
     result.set(sum);
     context.write(key, result);
   }
 }
 
```

### Main

La función Main

```
...
    Configuration conf = new Configuration();
    Job job = Job.getInstance(conf, "word count");
	job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
...
```

## Ejemplo de recuento de palabras (Word Count)

Full example of Word Count for Hadoop 3.2.1. Copy the code and save it to your local path as `WordCount.java`.

```
import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

  // Mapper
  public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }

  // Reducer 
  public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

      public void reduce(Text key, Iterable<IntWritable> values, Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }

  // Main
  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    Job job = Job.getInstance(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}
```


## Ejecución de aplicaciones Hadoop


En tu carpeta, crea la subcarpeta `wordcount_classes`:

````
mkdir wordcount_classes
````

Compila la aplicación WordCount (a partir del código `WordCount.java`):

```
javac -classpath `yarn classpath` -d wordcount_classes WordCount.java
```

Y después, (*atención al espacio entre / . *) 
```
jar -cvf WordCount.jar -C wordcount_classes / .
```

Finalmente, la ejecución se hace así:

```
hadoop jar <Application> <MainClassName> <Input in HDFS> <Output in HDFS>
```

**Ejemplos de execución**

*Atención: Cada ejecución requiere una carpeta nueva, independient. La carpeta de salida se crea sobre la marcha. *

Sobre el fichero quijote.txt in /tmp (HDFS):

```
hadoop jar WordCount.jar WordCount /tmp/quijote.txt /user/CCSA/<folder>/
```

Con un fichero de texto en tu carpeta:

```
hadoop jar WordCount.jar WordCount /user/CCSA/<yourFile>  /user/CCSA/<folder>/
```




## Resultados 

Comprueba la carpeta de salida:

```
hdfs dfs -ls /user/your-username/<folder>
```

Return ...:

```
Found 2 items
-rw-r--r--   2 root mapred          0 2019-05-13 17:23 /user/.../_SUCCESS
-rw-r--r--   2 root mapred       6713 2019-05-13 17:23 /user/.../part-r-00000
```

Mostar el contenido de ``part-r-00000``:

```
hdfs dfs -cat /user/your-username/<folder>/part-r-00000
```




## Recuento de palabras en python:

Para implementaciones en python, consulta: 

- https://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/
- https://glennklockwood.com/data-intensive/hadoop/streaming.html




<!--
cp /tmp/lorem.txt /home/CCSA/<userFolder>/lorem.txt
hdfs dfs -put lorem.txt /user/CCSA/<userFolder>/
hdfs dfs -put /home/<userFolder>/lorem.txt /user/CCSA/<userFolder>/

hdfs dfs -ls /user/CCSA/<userFolder>/lorem.txt
cat lorem.txt
hdfs dfs -cat /user/CCSA/<userFolder>/lorem.txt
-->






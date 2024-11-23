# MMDS_RecommenderSystem
Desarrollo de un modelo de recomendacion de libros de Amazon

## Descripción Codigo:

El procesamiento de los datos requiere una alta capacidad de recursos, por lo que es necesario ejecutar este código en un clúster con un kernel de PySpark. El clúster debe contar con al menos cinco máquinas (descritas en el documento: Proyecto.pdf) para garantizar un funcionamiento óptimo y eficiente.

### Configuracion de area de trabajo:

Se debe de aumentar la capacidad de memoria del driver de la sesion de spark a 9000 MB para que no colapse las ejecuciones del notebook.

Para el despliegue del EMR se requiere utilizar el archivo de requirements.txt para que se aplica en todos los nodos esa configuracion.

### Lectura de Datos:

Se debe de poner el archivo en un S3 con el siguiente nombre:
s3://mybigdatapablo/Books_rating.parquet

o garantizar que la ruta del archivo en s3 coincida con la ruta de lectura del archivo (en la celda #4 en la variable file_path).

### Preprocesamiento:

Primero se elimina cualquier valor nulo que tenga el df. Adcional, Convertir las columnas user ID y ID en un valor numerico. Separar los set de entrenaimiento y test garantizando que en el set de entrenamiento exista informacion de usarios e items presentes en el dataset y evitar el problema de coldstart.

### Modelo NAIVE:

Este modelo consiste en recomendar a todos los usuarios el top 10 de los libros mas calificados y con mayor calificacion. Se define una metrica de evaluacion como el top k precisión.

### Modelo Propuesto:

Se instancia el modelo ALS de factores latentes con la libreria ml de pyspark y se realiza una hiperparametrizacion con la metodologia de gridsearch para encontrar la mejor combinacion de los hiperparametros:
- rank (# de factores latentes)
- maxIter (# de iteraciones maxima)
- regParam (parametro de regularizacion)

La metrica de evaluacion de los mejores parametros del modelo fue el RMSE.

Los resultados fueron:
Mejor # de iteraciones: 20
Mejor # de factores latentes (rank): 15
Mejor valor de regParam: 0.1

El mejor modelo fue guardado para posteriormente utilizarlo y hacer las recomendaciones.

Con las recomendaciones del modelo, se realiza la validacion de los resultados con la metrica del top k precision y obtenemos un mejor resultado que el NAIVE.

Finalmente, se guarda las 10 recomendaciones por usuario en un bucket de s3 en formato .parquet.

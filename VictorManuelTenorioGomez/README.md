# Datos de calidad del aire de la Comunidad de Madrid

## Introducción

La siguiente memoria describe el proceso de transformación de datos sobre calidad del aire y estaciones de medición en la Comunidad de Madrid. Estos datos han sido obtenidos en el [portal de datos abiertos de la Comunidad de Madrid](https://www.comunidad.madrid/gobierno/datos-abiertos), el cual contiene muy diversos conjuntos de datos de la comunidad.

Por un lado, la transformación de los datos a datos enlazados, uniendo estos datos con los de otros repositorios de información de datos enlazados, como puede ser DBPedia, nos va a permitir realizar consultas más complejas sobre este conjunto de datos en concreto. Así, podríamos consultar si los municipios con un mayor número de habitantes son los que mayor contaminación presentan como cabría esperar, por poner un ejemplo.

Por otro lado, las estadísticas de calidad del aire son especialmente importantes en el mundo en el que vivimos donde la contaminación y el cambio climático son problemas muy relevantes en la sociedad. Por ello, toda la información que podamos obtener sobre ellos y todas las formas de realizar consultas que podamos añadir y permitan aportar un poco de luz al problema son más que bienvenidas.

## Proceso de transformación

### Selección y obtención de acceso a la fuente de datos

Los datos seleccionados se pueden dividir en dos conjuntos independientes:

* [Conjunto de datos de estaciones](http://datos.comunidad.madrid/catalogo/dataset/calidad_aire_estaciones): describe las estaciones de medida de datos de calidad del aire presentes en la comunidad, incluyendo datos como la zona, el municipio, la fecha de alta o las partículas contaminantes que puede analizar. El conjunto se actualiza anualmente, y su última actualización data del 19 de mayo de 2020, por lo que parece que los datos están actualizados.
* [Conjunto de datos de calidad del aire](http://datos.comunidad.madrid/catalogo/dataset/calidad_aire_datos_historico): contiene las medidas de los datos de calidad del aire desde el año 2005 hasta hoy. Para cada medida, contiene datos sobre el lugar de la medición (estación y municipio), el gas medido y las mediciones para las 24 horas del día. Los datos se actualizan diariamente.

Para el segundo conjunto, encontramos en la página web de los datos [el siguiente documento en PDF](https://datos.comunidad.madrid/catalogo/dataset/a770d92c-c513-4974-b1a7-2b15be1dd91f/resource/f743eacc-5e89-4591-a0fc-4caebfe22557/download/descripcion-fichero-datos-de-contaminantes.pdf) que nos permite interpretar la información contenida en cada una de sus columnas.

En cuanto al acceso a los mismos, ambos conjuntos se encuentran disponibles para descargar en el mismo portal en formato CSV y JSON, por lo que son fácilmente importables en herramientas de análisis de datos como OpenRefine.

### Análisis de la licencia de los datos

En primer lugar, analizaremos la licencia que aplica sobre los conjuntos de datos seleccionados. En la página de ambos datasets, podemos ver que la licencia de los mismos es la de [**Atribución** de Creative Commons (Open Data)](https://creativecommons.org/licenses/by/4.0/legalcode.es), con el código **CC BY 4.0**. Esto significa que los datos son libres de ser utilizados por cualquier persona con los fines que se deseen (comerciales o no), y se pueden compartir con la licencia deseada (no necesariamente la misma), pero con la condición de que es necesario atribuir los datos al portal del que son obtenidos.

Aunque la licencia de los datos no incluya explícitamente la condición "Compartir igual", considero que lo más adecuado, ya que únicamente estamos transformando los datos a otro formato, es utilizar la misma licencia que tienen los datos de origen.

### Análisis de la fuente de datos



### Estrategia de nombrado de recursos

### Desarrollo del vocabulario

### Proceso de transformación de los datos

### Enlazado

### Publicación de los datos

## Aplicación y explotación

## Conclusiones

## Bibliografía

* [Portal de datos abiertos de la Comunidad de Madrid](https://www.comunidad.madrid/gobierno/datos-abiertos)

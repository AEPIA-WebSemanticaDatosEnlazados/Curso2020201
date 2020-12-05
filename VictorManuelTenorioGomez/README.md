# Datos de calidad del aire de la Comunidad de Madrid

## Introducción

La siguiente memoria describe el proceso de transformación de datos sobre calidad del aire y de estaciones de medición en la Comunidad de Madrid.Estos datos han sido obtenidos en el [portal de datos abiertos de la Comunidad de Madrid](https://www.comunidad.madrid/gobierno/datos-abiertos), el cual contiene muy diversos conjuntos de datos de la comunidad.

Por un lado, la transformación de los datos a datos enlazados, uniendo estos datos con los de otros repositorios de información de datos enlazados como puede ser DBPedia, nos va a permitir realizar consultas más complejas sobre este conjunto de datos en concreto. Así, podríamos consultar si los municipios con un mayor número de habitantes son los que mayor contaminación presentan como cabría esperar, por poner un ejemplo.

Por otro lado, las estadísticas de calidad del aire son especialmente importantes en el mundo en el que vivimos donde la contaminación y el cambio climático son problemas muy relevantes en la sociedad. Por ello, toda la información que podamos obtener sobre ellos y todas las consultas que podamos hacer que permitan aportar un poco de luz al problema son más que bienvenidas.

## Proceso de transformación

### Selección y obtención de acceso a la fuente de datos

Los datos seleccionados se pueden dividir en dos conjuntos independientes, con un tercero complementario que aporta información que ayuda a comprender los datos:

* [**Conjunto de datos de estaciones**](http://datos.comunidad.madrid/catalogo/dataset/calidad_aire_estaciones): describe las estaciones de medida de datos de calidad del aire presentes en la comunidad, incluyendo datos como la zona, el municipio, la fecha de alta o los gases que puede analizar. El conjunto se actualiza anualmente, y su última actualización data del 19 de mayo de 2020, por lo que parece que los datos están actualizados.
* [**Conjunto de datos de calidad del aire**](http://datos.comunidad.madrid/catalogo/dataset/calidad_aire_datos_historico): contiene las medidas de los datos desde el año 2005. Para cada medida, contiene datos sobre el lugar de la medición (estación y municipio), el gas medido y las mediciones para las 24 horas del día. Además, encontramos en la página web de los datos [el siguiente documento en PDF](https://datos.comunidad.madrid/catalogo/dataset/a770d92c-c513-4974-b1a7-2b15be1dd91f/resource/f743eacc-5e89-4591-a0fc-4caebfe22557/download/descripcion-fichero-datos-de-contaminantes.pdf) que nos permite interpretar la información contenida en cada una de sus columnas.
* **Conjunto de datos de magnitudes**: en el pdf del segundo conjunto de datos, más concretamente en la Tabla 3 del mismo, vemos que existe un tercer conjunto de datos sobre magnitudes. En el mismo existen datos sobre los distintos gases que se pueden medir en las estaciones, unidades de medida y técnicas de medición. Creo que aporta mucha información interesante y completa los datos que quiero transformar. Por ejemplo, aporta la información sobre el nombre del gas que se está midiendo, ya que en el conjunto de datos de mediciones únicamente aparece el código de las mismas, por lo que creo que es razonable realizar el esfuerzo de transformar también este conjunto de datos en datos enlazados.

En cuanto al acceso a los mismos, los dos primeros conjuntos se encuentran disponibles para descargar en el mismo portal en formato CSV y JSON, por lo que son fácilmente importables en herramientas de análisis y adecuación de datos como OpenRefine. En cuanto al tercero, se trata de una tabla en un PDF, pero dada su limitada extensión, es fácilmente exportable a formato Excel o CSV para su importación en las herramientas de análisis.

### Análisis de la licencia de los datos

En primer lugar, analizaremos la licencia que aplica sobre los conjuntos de datos seleccionados. En la página de ambos datasets, podemos ver que la licencia de los mismos es la de [**Atribución** de Creative Commons (Open Data)](https://creativecommons.org/licenses/by/4.0/legalcode.es), con el código **CC BY 4.0**. Esto significa que los datos son libres de ser utilizados por cualquier persona con fines los fines que se deseen (comerciales o no), y se pueden compartir con la licencia deseada (no necesariamente la misma), pero con la condición de que es necesario atribuir los datos al portal del que son obtenidos.

Aunque la licencia de los datos no incluya explícitamente la condición "Compartir igual", considero que lo más adecuado, ya que únicamente estamos transformando los datos a otro formato, es utilizar la misma licencia que tienen los datos de origen.

### Análisis de la fuente de datos

El primer paso para poder transformar los datos es su análisis, para poder entender el conjunto y así obtener un mejor resultado final tras la transformación. Tenemos que comprender qué información contiene cada una de las columnas de los datos para así ver cuál es su representación más adecuada en el mundo de los datos enlazados.

Para el análisis de los datos, he utilizado por un lado LibreOffice Calc, dada su facilidad para obtener información sobre los datos de diferentes maneras, y por otro OpenRefine, debido a que será la misma que utilizaré para el posterior adecuamiento de los mismos y las facetas ofrecen una muy buena herramienta para su exploración, la detección de anomalías, etc. Cómo ya hemos dicho, tanto los datos de las estaciones como los de mediciones están disponibles en CSV, por lo que la carga en ambas herramientas es sencilla.

A continuación, resumiré en tablas el significado y el rango que pueden tomar los valores de cada una de las columnas de nuestros tres conjuntos de datos.


##### Conjunto de datos de mediciones

| Nombre de la columna | Tipo | Descripción | Rango de valores | 
|:------|:------|:------|:------|
| provincia | número | Código numérico de la provincia (siempre será Madrid)  | Siempre valdrá 28, código de la Comunidad de Madrid | 
| municipio | número |  Código numérico del municipio. Se pueden consultar en la Tabla 2 del [pdf que describe el conjunto](https://datos.comunidad.madrid/catalogo/dataset/a770d92c-c513-4974-b1a7-2b15be1dd91f/resource/f743eacc-5e89-4591-a0fc-4caebfe22557/download/descripcion-fichero-datos-de-contaminantes.pdf)  | 5 (Alcalá de Henares) - 180 (Villarejo de Salvanés)  | 
| estacion | número | Código numérico de la estación. Es correlativo, lo asigna el ministerio y sirve para identificar las posibles ubicaciones de las estaciones en ese municipio  | 1 - 14  | 
| magnitud | número | Código numérico del contaminante de la medición. Los distintos valores que puede tomar son los del conjunto de datos de magnitudes, también transformado  | 1 (Dióxido de carbono) - 431 (MetaParaXileno) | 
| punto\_muestreo | string | Código alfanumérico (contiene el caracter '\_') con la siguiente estructura: "{Código de la provincia con 2 números}{Código del municipio con 3 números}{Código de la estación con 3 números}\_{Código de la medida}\_{Código de la técnica de medida}" | N/A  | 
| ano | número | Año de la realización de la medición  | Debido a que únicamente he descargado los datos de los meses de marzo, abril y mayo de 2020, este campo siempre tendrá el valor 2020 | 
| mes | número | Mes de la realización de la medición  | Podrá tomar los valores 3, 4 y 5, debido a que son los meses que hemos descargado | 
| dia | número | Día de la realización de la medición  | 1 - 31 | 
| h01-h24 | número | Valor de las mediciones para cada una de las horas del día. Se realiza una medición por hora, de ahí que tengamos 24 columnas de este tipo  | 0 - 375 | 
| v01-v24 | caracter | De acuerdo al PDF en el que se explican este conjunto de datos, estas columnas indican si la medición de la hora correspondiente es válida (en ese caso toma el valor V), temporalmente válida (valor T) o inválida (valor N) | Puede tomar los valores V, T y N  | 
Tabla 1: Descripción de las columnas del conjunto de datos de mediciones


##### Conjunto de datos de estaciones

| Nombre de la columna | Tipo | Descripción | Rango de valores | 
|:-----|:-----|:------|:------| 
| estacion\_codigo | número | Código numérico de la estación, que incluye: "{Código de la provincia con dos números}{Código del municipio con 3 números}{Código de la estación con dos números}"  | N/A | 
| zona\_calidad\_aire\_descripcion | string | Zona donde se encuentra la estación  | N/A  |
| estacion\_municipio | string | Municipio donde se encuentra la estación  | N/A | 
| estacion\_fecha\_alta | fecha | Fecha de alta de la estación en el sistema  | 19-1-2000 - 1-1-2019  |
| estacion\_tipo\_area | string | Tipo de área donde se encuentra la estación  | Urbana, Rural y Subrural |
| estacion\_tipo\_estacion | string | Tipo de estación  | Fondo, Industrial y Tráfico  |
| estacion\_subarea\_rural | string | Cómo es el área para las estaciones en un área de tipo "Rural"  | Remota o Regional para estaciones con tipo de área rural, en blanco para el resto  |
| estacion\_direccion\_postal | string | Dirección postal de la estación | N/A |
| estacion\_coord\_UTM\_ETRS89\_x  | número | Coordenada x del sistema de coordenadas UTM ETRS89  | 381298 - 481205  |
| estacion\_coord\_UTM\_ETRS89\_y  | número | Coordenada y del sistema de coordenadas UTM ETRS89  | 4431618 - 4528767  |
| estacion\_coord\_longitud  | string | Longitud de la estación  | 3º 13' 16'' W - 4º 23' 53,2'' W  |
| estacion\_coord\_latitud  | string | Latitud de la estación  | 40º 01' 59,8'' N - 40º 54' 32,6'' N  |
| estacion\_altitud  | número | Altitud de la estación  | 360 - 1870  |
| estacion\_analizador\_xx | string | Representa si la estación tiene posibilidad de medir el gas que especifica la columna  | Sí o en blanco, si la columna puede medirlo o no, respectivamente  | 
Tabla 2: Descripción de las columnas del conjunto de datos de estaciones


##### Conjunto de datos de mediciones

| Nombre de la columna | Tipo | Descripción | Rango de valores | 
|:-----|:-----|:------|:------| 
| codigo\_magnitud | número | Código numérico de la magnitud a medir. Se puede unir con el código numérico del conjunto de mediciones  | 1 - 431  | 
| descripcion\_magnitud | string | Nombre del gas que mide  | N/A  |
| codigo\_tecnica\_medida  | número | Código de la técnica de medida utilizada para la magnitud  | 2, 7, 8, 28, 48, 49 y 59 | 
| descripcion\_tecnica\_medida  | string | Nombre de la técnica de medida  | N/A  |
| unidad | string | Nombre abreviado de la unidad utilizada para la técnica de medida  | $mg/m^3$ o $\mu / m^3$ |
| descripcion\_unidad | string | Nombre (texto) de la unidad utilizada para la técnica de medida  | miligramos o microgramos por metro cúbico  |
Tabla 3: Descripción de las columnas del conjunto de datos de mediciones

Como vemos, es un conjunto de datos muy consistente, que no tiene errores de tipado ni anomalías. Únicamente faltan ciertas medidas, las cuales están claramente identificadas debido al campo complementario que lo indica con una 'N'.

En cuanto al esquema de los mismos, en la página web no se puede encontrar un esquema propiamente dicho, y tampoco provienen de una base de datos relacional para que lo tengan implícito. De todas formas, no es difícil identificar cómo enlazar los tres conjuntos de datos:
* Los conjuntos de magnitudes y de mediciones son fácilmente enlazables mediante los atributos "codigo\_magnitud" del primero y "magnitud" del segundo. No es necesario ningún procesamiento previo a estas columnas para realizar este enlazado.
* En cuanto a los conjuntos de estaciones y magnitudes, los campos "estacion\_codigo" del primero, y la primera parte, es decir, hasta la primera '\_' del campo "punto\_muestreo" del segundo nos permiten hacer este enlazado. Para ello, tendremos que dividir esta columna en varias utilizando como delimitador el caracter '\_'.


### Estrategia de nombrado de recursos

A continuación, definiré la estrategia que utilizaré para nombrar los recursos de este conjunto. El dominio que he elegido para las URIs de los recursos y los posibles términos ontológicos que se necesiten es el mismo utilizado en los ejemplos, http://smartcity.linkeddata.es, debido a que las medidas de contaminación considero que están muy relacionadas con el concepto de Smart Cities.

La estrategia a utilizar para nombrar los distintos elementos del vocabulario será la siguiente:

* Para identificar los términos ontológicos, en caso de que sea necesario crear algún elemento por no encontrar otro concepto a reutilizar, utilizaré la '#'. La ruta de los mismos y el patrón para la construcción de las URIs será "http://smartcity.linkeddata.es/airQuality/ontology#\<nombre del término\>".
* Para los identificar las estaciones utilizaremos el carácter '/', debido a que, aunque no es un conjunto muy grande (24 estaciones) y no es dinámico (no tiene ningún parámetro que se actualice cada poco tiempo), utilizaremos estos recursos posteriormente para referirnos a los sensores. La ruta y el patrón se definirán como "http://smartcity.linkeddata.es/airQuality/resources/stations/\<Código de la estación\>".
* Para las magnitudes vamos a utilizar en su lugar el carácter '#', debido a que son pocos datos y no cambian a menudo. La URI para representarlas seguirá el patrón "http://smartcity.linkeddata.es/airQuality/resources/magnitudes#\<Código de la magnitud\>".
* Para los datos de mediciones, utilizaremos la '/', utilizando el punto de muestreo, la magnitud medida y la fecha de la medición. El patrón será "http://smartcity.linkeddata.es/airQuality/resources/measurement/\<Campo "punto\_muestreo" de la tabla sin la información sobre la técnica de medida, contiene información sobre la estación y sobre la magnitud a medir\>/\<Fecha de la medición\>".
* Para los datos de los sensores de las distintas magnitudes alojados en cada una de las estaciones, utilizaremos la '#', debido a que son pocos sensores dentro de una estación. Así, el patrón de definición de las URIs será "http://smartcity.linkeddata.es/airQuality/resources/stations/\<Código de la estación\>#\<Código de la magnitud que mide\>".
* En cuanto a los municipios y las zonas donde están ubicados, serán representados igualmente con la '#', con el patrón "http://smartcity.linkeddata.es/airQuality/resources/towns#\<Nombre del municipio\>" o "http://smartcity.linkeddata.es/airQuality/resources/zones#\<Nombre de la zona\>", respectivamente.

### Desarrollo del vocabulario

Los datos de mediciones de calidad del aire se ajustan a una red de sensores que miden el grado de presencia de ciertos elementos contaminantes en el aire. Por ello, he decidido utilizar la ontología estándar del W3C **Semantic Sensor Network Ontology (SSN)**, más concretamente la ontología derivada de la misma y que compone su núcleo **Sensor, Observation, Sample and Actuator (SOSA)**, que contiene todos los elementos ontológicos necesarios para la descripción de nuestros datos. El esquema utilizado para representar los datos de este conjunto se puede observar en la siguiente imagen:

![Vocabulario utilizado como soporte para el conjunto de datos de mediciones](ontology/ontology.jpg)

Las equivalencias con nuestro conjunto de datos son las siguientes:

* Las **mediciones** de datos de calidad del aire estarían representadas por individuos de la clase **sosa:Observation**. Estas tendrán asociadas
	* Un elemento **sosa:hasSimpleResult**, con el resultado de la medición (como **xsd^float**).
	* Una validez descrita por la propiedad definida en nuestra ontología **isValidResult** que tomaría el valor de un rdfs:Literal debido a que puede tomar los valores 'V' para indicar que la medida es válida, 'T' que indica que la medición es válida temporalmente y 'N' que indica que no es válida.
	* La fecha y hora de la medición, mediante la propiedad **sosa:resultTime** y objetos de la clase **xsd:dateTime**.
* Las **estaciones** de medición de calidad del aire estarían representadas por la clase **sosa:Platform**, debido a que alojan varios sensores para medir las distintas magnitudes de calidad del aire. Este elemento tendrá asociado:
	* Están ubicadas en un municipio representado por la clase **schema:Place**, que a su vez está contenido (**schema:containedInPlace**) en una zona, también descrita por la clase **schema:Place**.
	* Fecha de alta mediante la propiedad definida en mi ontología **fechaAlta**, y de tipo **xsd^date**.
	* El tipo de estación, utilizando la propiedad **tipoEstacion**.
	* El tipo de zona donde está ubicada la estación mediante la propiedad **zonaEstacion**.
	* Dirección mediante la propiedad **schema:address** y la clase **schema:PostalAddress**.
	* Punto de coordenadas, descrito por las propiedades **geo:lat**, **geo:long** y **geo:alt** del vocabulario WGS84. Debido a que en el conjunto de datos vienen como los grados, minutos y segundos, he optado por utilizar **rdfs:Literal** para describir la latitud y la longitud, aunque normalmente vengan como floats.
* Los **sensores** para cada una de las magnitudes alojados por las distintas estaciones serán representados por **sosa:Sensor**. Estos estarán asociados a las observaciones, las propiedades y las estaciones (Platform) mediante las propiedades estándar del vocabulario **sosa**.
* Por último, las **magnitudes** a medir serán **sosa:ObservableProperty**. Estarán descritas por:
	* Un título o nombre de la magnitud mediante la propiedad **dc:title**, representado por un **rdfs:Literal**.
	* Una unidad de medida mediante la propiedad **om:hasUnit** de la **Ontology of units of Measure** (link [aquí](https://github.com/HajoRijgersberg/OM)). Sin embargo, en dicha ontología no se encontraba la magnitud que más aparecía en nuestros datos, microgramos por metro cúbico, por lo que he definido mi propia clase **Unidad**. Esta tiene una **rdfs:label** que se asocia con un **rdfs:Literal** que nos dice el nombre de la unidad.
	* Para las distintas técnicas de medición especificadas para las magnitudes no he podido encontrar una ontología que se ajustase de forma correcta a las mismas, por lo que he definido la clase **Tecnica** con la propiedad **hasTecnica**. Esta tiene un título (propiedad **dc:title**) que lo relaciona con un **rdfs:Literal**.

### Proceso de transformación de los datos

### Enlazado

### Publicación de los datos

## Aplicación y explotación

## Conclusiones

## Bibliografía

* [Portal de datos abiertos de la Comunidad de Madrid](https://www.comunidad.madrid/gobierno/datos-abiertos)
* [Best Practices for Publishing Linked Data](https://www.w3.org/TR/ld-bp/)
* [Protegé](https://protege.stanford.edu/)
* [OpenRefine](https://openrefine.org/)
* [Semantic Sensor Network Ontology](https://www.w3.org/TR/vocab-ssn/)
* [Ontology of units of Measure](https://github.com/HajoRijgersberg/OM)
* [Geo (WGS84 lat/long) Vocabulary](https://www.w3.org/2003/01/geo/)
* [Schema.org](https://schema.org/)
* [Dublin Core Metadata Initiative](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/)

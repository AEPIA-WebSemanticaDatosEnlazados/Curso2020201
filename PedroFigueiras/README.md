# MEMORIA - WEB SEMÁNTICA Y DATOS ENLAZADOS

## 1. INTRODUCCIÓN

El propósito de este trabajo es desarrollar y explicar el proceso de enlazar datos de acuerdo a lo aprendido a lo largo de la asignatura. Una vez los datos estén adecuadamente 
enlazados se comentará también cómo se podrían explotar. En todo momento, se ha utilizado la herramienta OpenRefine en su variante LODRefine.

La idea base del proyecto ha sido la de utilizar una lista de las mejores películas de la historia con la idea de enlazarlas sobre una base de conocimiento existente que 
facilitase más detalles sobre cada título. Se simplificará el trabajo (se considerarán solo las películas de las últimas dos décadas) para ganar eficiencia, pero se pondrá especial atención en que todos los puntos de la memoria sean satisfechos.

## 2. PROCESO DE TRANSFORMACIÓN

### A. FUENTE DE DATOS

Se ha utilizado la lista el ranking TSPDT’s 1,000 Greatest Films que se puede encontrar en el siguiente enlace en formato Microsoft Excel: https://www.theyshootpictures.com/resources/1000GreatestFilms.xls. Esta lista se actualiza con carácter anual y es utilizada frecuentemente por los medios de comunicación.

### B. ANÁLISIS DE LOS DATOS

El conjunto de datos consiste en 10 columnas y 1.0001 registros, siendo el primero una cabecera. A continuación se muestra un resumen de la información de cada columna:
- Pos: muestra la posición numérica de la película en un rango de 1 a 1000. 
- 2019: lo mismo que la columna anterior, pero en la lista de 2019.
- 2018: lo mismo que la columna anterior, pero en la lista de 2018.
- Title: el título de la película. Los artículos van al final, después de ",".
- Director: el director de la película en formato: Apellido, Nombre.
- Year: el año de lanzamiento de la película.
- Country: el país de la producción.
- Length: duración de la película.
- Genre: el género al que pertenece la película. Puede ser multigénero separado por "-".
- Colour: indica si es en color (Col), blanco y negro (BW) o ambos (Col-BW).

Con respecto a la licencia de los datos, la web no especificaba el tipo de licencia bajo el que podían ser usadas las listas. Se ha contactado al administrador (Bill Georgaris) para aclarar este punto. Se actualizará en cuanto responda, pero por ahora se han tratado los datos como si tuvieran licencia CC-0, esto es Creative Commons Public domain, que implica que los datos de pueden utilizar libremente y que el autor cede sus derechos de uso completamente.

### C. ESTRATEGIA DE NOMBRADO

Se seguirá la siguiente:
- Dominio: dado que no se planea publicar, se ha tomado: http:/example.com.
- Forma de las URIs: # para términos ontológicos y / para individuos.
- Ruta para individuos (las películas): http:/example.com/movies/
- Patrón para individuos: http:/example.com/movies/<título de la película>
- Ruta para términos ontológicos (propiedades - en este caso año de lanzamiento): http://example/ontology/year#
- Patrón para términos ontológicos: http://example/ontology/year#<año de estreno>

### D. VOCABULARIO

Solo se ha considerado una propiedad (presente en la lista de datos original), el año de estreno. Para poder añadirla al vocabulario, se ha procedido de la siguiente manera:
- Se ha creado un nuevo Prefix denominado "onto" para poder añadir términos ontológicos.
- El predicado de cada película se ha definido como onto:releasedIn, que tendrá como objeto el año de estreno.

### E. PROCESO DE TRANSFORMACIÓN

- En primer lugar se ha procedido a un filtrado de datos utilizado la herramienta "Facet > Numeric facet" de LODRefine sobre la columna "Year" para seleccionar y eliminar todas las películas anteriores al año 2000. 
![ScreenShot](https://github.com/magnuspit/Curso2020201/blob/patch-1/PedroFigueiras/An1.jpg)

- A continuación se han eliminado todas las columnas diferentes a "Year" y "Title". La idea es que la información de cada una de las películas provenga del enlazado con una base de conocimiento. 
- Para facilitar el siguiente proceso de reconciling se han transformado los datos de la columna "Title", reemplazando ',The' y ',A' por '' (espacio vacío). Esta operación se ha realizado dos veces (una sobre cada artículo) sobre la columna "Title" con la herramienta "Edit cells > Transform" utilizando las siguientes expresiones de lenguaje GREL: value.replace(',The', '') y value.replace(',A', '').
![ScreenShot](https://github.com/magnuspit/Curso2020201/blob/patch-1/PedroFigueiras/An2.jpg)

### F. ENLAZADO

Siguiendo los ejemplos del curso y que la extensión de DBpedia ya estaba incluida en LODRefine se pensó en utilizar esta base de conocimiento para enlazar los títulos de las películas. El resultado al utilizar la herramienta "Reconcile > Start reconciling" y seleccionar el servicio "DBpedia" y el type "dbo:Film" fue el siguiente:
- Antes de eliminar ", The" y ", A", 65 títulos fueron identificados correctamente. Para 28 de ellos, no se encontró equivalencia.
- Al eliminar los artículos, la situación mejoró, pudiéndose identificar 70 títulos. El problema se dio al intentar identificar los 23 títulos restantes, siendo inviable su clasificación manual ya sea por falta de respuesta de DBpedia o de opciones correctas.

Antes de continuar, después de investigar un poco sobre servicios activos, se decidió gestionar el mismo proceso con la base de conocimiento Wikidata. Como primer paso, se añadió el servicio a la lista predifinida. Para ello, dentro de la pantalla "Reconcile > Start reconciling > Add standard Service" se utilizó https://wikidata.reconci.link/en/api. A continuación se seleccionó el type: Film (Q11424).
![ScreenShot](https://github.com/magnuspit/Curso2020201/blob/patch-1/PedroFigueiras/An3.jpg)
- Con esta opción se consiguiço identificar sin problema los 93 títulos, aunque en algún caso hubo que gestionar el proceso de match manualmente porque la primera opción propuesta no era la adecuada.

Al no haber utilizado DBpedia, la extracción de enlaces no estaba asistida por LODRefine, por ello hubo que consultar la documentación de Wikidata (accesible aquí: https://wikidata.reconci.link/) para ver cómo poder generar los enlaces en una columna. Se detalla a continuación el proceso seguido:
- Lo primero es añadir una columna basada en la que se han enlazado los datos utilizando sobre la columna Title "Edit column > Add column based on this column".
- Una vez dentro, se añade la expresión en GREL cell.recon.match.id que nos dará las referencias únicas de Wikidata (del tipo QXXXXXX).
![ScreenShot](https://github.com/magnuspit/Curso2020201/blob/patch-1/PedroFigueiras/An4.jpg)
- Se nombra a la columna como WikiRef.
- Con la herramienta "Edit cells > Transform" y la expresión GREL: 'https://www.wikidata.org/wiki/'+value las celdas quedan transformadas a los enlaces de Wikidata.
![ScreenShot](https://github.com/magnuspit/Curso2020201/blob/patch-1/PedroFigueiras/An5.jpg)
- Finalmente se decidió cambiar el nombre de la columna Title a "Film".

El último paso ha consistido en utilizar la extensión RDF (ya integrada en LODRefine) para editar la estructura del RDF, generar los enlaces correctamente y poder exportarlos en formato Turtle. Se detalla el proceso a continuación:
- Se utiliza la herramienta "Edit RDF Skeletonn" para definir dos tripletas de acuerdo a los apartados anteriores 2C y 2D.
![ScreenShot](https://github.com/magnuspit/Curso2020201/blob/patch-1/PedroFigueiras/An6.jpg)
- Se comprueba que los enlaces generados son correctos en "Preview".
![ScreenShot](https://github.com/magnuspit/Curso2020201/blob/patch-1/PedroFigueiras/An7.jpg)
- Finalmente, se genera el archivo Turtle a través de "Export > RDF as Turtle".
- Se nombra al archivo como "Films_S21", disponible aquí: https://github.com/magnuspit/Curso2020201/blob/patch-1/PedroFigueiras/Films_S21.ttl

### E. PUBLICACIÓN

No se ha procedido a publicar los datos enlazados.

## 3. APLICACIÓN Y EXPLOTACIÓN

Aunque se ha trabajado con un número de datos y propiedades muy limitado, el enlazado con la base de conocimiento Wikidata tiene las siguientes ventajas:
- Muchas de las películas en la lista no son precisamente mainstream, por lo que el enlazado con Wikidata proporciona una sinopsis, director, enlaces a trailers, fotografías, etc. En definitiva, sirve para ampliar considerablemente la información sobre cada título y decidir si la película resulta de interés.
- Igualmente, al haber generado grafos RDF, se ha facilitado la posibilidad de hacer consultas tipo SPARQL.

A continuación se presenta un ejemplo de SPARQL query que se podría aplicar sobre los datos publicados en la que se busca como respuesta los títulos y año de las películas que se estrenaron durante la primera década del siglo XXI (2000-2010):
```
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix onto: <http://example.com/ontology/year#> .
SELECT  ?Title ?Year
WHERE  { 
  <http://example.com/movies> rdf:Title ?Title .
  onto:releasedIn ?Year .
FILTER (?Year<2011 && ?Year>1999)
}
```
## 4. CONCLUSIONES

Partiendo de un conjunto de datos de películas plano y sin demasiada información sobre cada título, se ha conseguido enlazar cada título con una base de conocimiento (Wikidata) que aporta muchísima más información sobre cada una. Igualmente, dicho trabajo ha hecho posible realizar consultas para que cada usuario filtre la información que desea obtener fácilmente.

La principal conclusión es que se han demostrado las ventajas de aplicar conceptos de web semántica y enlazado de datos para explotar conjuntos de datos.

## 5. BIBLIOGRAFÍA

- El material de la asignatura y lecturas recomendadas.
- LODRefine (descarga): https://sourceforge.net/projects/lodrefine/
- Documentación de Wikidata para OpenRefine: https://wikidata.reconci.link/
- Web desde la que se han descargado los datos: https://www.theyshootpictures.com
- Guía y ejemplos de queries SPARQL de w3.org: https://www.w3.org/2009/Talks/0615-qbe/

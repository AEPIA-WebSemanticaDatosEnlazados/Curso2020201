1.INTRODUCCIÓN:

Este trabajo consistirá en la generación de datos enlazados en formato RDF, a partir de un conjunto de datos estructurado relativo a los accidentes sucedidos en la ciudad de Madrid. Para ello se realizará un análisis y preparación de los datos, todo ello orientado a una explotación posterior de los mismos mediante Sparql. Así mismo se definirá una ontología adecuada al dominio de los datos y se llevará a cabo el enlazado con otros conjuntos de datos.
Una vez transformados a RDF los datos podrán ser consultados para obtener información relevante.

2.PROCESO DE TRANSFORMACIÓN

a) Selección de la fuente de datos:

Como punto de partida analicé varios de los portales de datos abiertos sugeridos en las lecciones de la asignatura. En el caso de los datos abiertos del Gobierno de España (datos.gob.es) me encontré con multitud de datos publicados con contenidos muy pobres, así como datasets de tipo resumen o demasiado generales. Al no contar con información interesante que analizar y transformar decidí usar otras portales, en concreto el Portal de datos abiertos del Ayuntamiento de Madrid (https://datos.madrid.es/portal/site/egob/). Aquí ya se mostraban datasets con mayor riqueza en la información contenida, lo que permite realizar consultas finales más interesantes sobre los datos. Entre varios que preseleccioné (reservas de motos, objetos perdidos en taxis, incidencias de bomberos, etc.) el que me pareció más útil para ser explotado, tanto por los campos contenidos y por el número de registros, fue el de Accidentes de tráfico de la Ciudad de Madrid:
-https://datos.madrid.es/portal/site/egob/menuitem.c05c1f754a33a9fbe4b2e4b284f1a5a0/?vgnextoid=7c2843010d9c3610VgnVCM2000001f4a900aRCRD&vgnextchannel=374512b9ace9f310VgnVCM100000171f5a0aRCRD&vgnextfmt=default

Este conjunto de datos se puede obtener para periodos de un año completo, por lo que trabajaré con el año 2018, ya que los datos de 2019 y 2020 son provisionales.
Cada registro corresponde a una persona afectada en un accidente dado, y los campos de información se corresponden con datos sobre la ubicación, el estado del asfalto, lesividad, sexo, edad, etc. En el apartado siguiente (Análisis de los datos) se verá cada campo en detalle

b) Análisis de los datos:

Para realizar el análisis y pre-procesado de los datos utilizaré la herramienta OpenRefine, junto a la extensión RDF.
Los diferentes campos que presenta el dataset son:

-FECHA: contiene un texto concatenando el día de la semana y el mes (expresados en inglés con tres carácteres), el número de día del mes, la hora CET y el año. Si se aplica una transformación común a fecha de este campo mediante la edición de celdas se obtiene una transformación errónea, por lo que es necesario realizar cambios primero para poder obtener un campo fecha válido por separado.

![Screenshot](Imagenes/Fecha_transformacionDirecta.jpg)

Al aplicar una faceta de texto a este campo observamos que la información de la hora no es útil, ya que en todos los registros se muestra el valor 00:00:00. Para contar con la posibilidad de trabajar con una información tan relevante como la hora existe otro campo con información del rango horario, que podremos utilizar para aportar riqueza y mayor nivel de detalle en las consultas.

![Screenshot](Imagenes/Fecha_faceta.jpg)

No obstante, dicha información horaria sería conveniente guardarla en un campo aparte, ya que puede que futuros datasets publicados para nuevos años sí contengan la hora real.
Adicionalmente, la información del día de la semana ya está disponible en otro campo, con lo que podemos elilminarla del campo 'FECHA', sin necesidad de guardarla en una nueva columna.

-RANGO HORARIO: se muestra un rango horario de una hora para expresar el momento del accidente. Aunque no tenemos la precisión que aportaría la hora exacta, con esta información podría ser útil para trabajar en consultas finales, utilizando este campo con transformaciones adicionales y/o con el uso de propiedades.

-DIA SEMANA: presenta el día de la semana en mayúsculas

-DISTRITO: incluye el nombre del distrito de la ciudad de Madrid. Este campo se podría usar con un servicio de reconcilicación posteriormente para realizar el enlazado de datos, ya que nos podría aportar algún tipo de información relevante de cada lugar y que no está presente en el dataset original, como su población, localización,etc.

-LUGAR ACCIDENTE: este campo se destina a la información de la calle, o cruce de dos calles, donde tuvo lugar el accidente. A la hora de transformar el dataset, este campo debería ser tratado para separar las dos calles en el caso de que el accidente corresponda a un cruce. En estos casos se separan con un guión en la columna original. En los accidentes cuya localización se exprese con una sola calle encontramos al final el texto "NUM", indicando que en el campo siguiente ("Nº"), veremos un valor numérico asociado al número de la calle.
También podemos encontrarnos con el texto "KM." al final, indicando que se trata de un accidente en una vía o carretera, no en una calle. En estos casos el campo "Nº" presentará un valor numérico

-Nº: el número de la calle donde se ubica el accidente, cuando proceda al no corresponder a un cruce. Si aplicamos una faceta numérica a este campo combinada con un faceta de texto que nos permitan filtrar y seleccionar registros, vemos que hay 494 valores no-numéricos y que deberíamos convertir para homogeneizar la información. Corresponden a valores vacíos de accidentes en cruces, cuyo valor debería ser 0, como vemos en el resto de casos con registros de accidentes en dos calles.

![Screenshot](Imagenes/Valores_N.jpg)

También se observan valores numéricos muy elevados, que corresponden aparentemente a indicaciones del kilómetro de la vía en el que se produjo el accidente. Parece ser que en estos casos el valor númerico del campo corresponde a metros y no a kilómetros, como se podría pensar de la información del campo "LUGAR ACCIDENTE" adyacente. Se podría pensar en algún tipo de restricción a la hora de introducir los valores en ese campo que impedía trabajar con decimales, por ejemplo, y obligó a introducir la información como entero.
Se identifica algún caso conflictivo, como expresar el texto con el indicativo "NUM" y encontrarnos con valores asociables al tipo kilómetro ("KM.")

![Screenshot](Imagenes/Ejemplo_Valores_N.jpg)

-Nº PARTE: indica el número de parte del accidente, expresado con el formato AAAA/número secuencial. Este campo lo podremos utilizar como el clave para identificar cada accidente de manera única. Cada registro de accidente puede tener varias filas, que corresponden a diferentes victimas o testigos.

-CPFA Granizo: granizo en el momento del accidente. Campo de condiciones ambientales con posibles valores SI o NO

-CPFA Hielo: presencia de hielo en el momento del accidente. Campo de condiciones ambientales con posibles valores SI o NO

-CPFA Lluvia: lluvia en el momento del accidente. Campo de condiciones ambientales con posibles valores SI o NO

-CPFA Niebla: niebla en el momento del accidente. Campo de condiciones ambientales con posibles valores SI o NO

-CPFA Seco: ambiente seco en el momento del accidente. Campo de condiciones ambientales con posibles valores SI o NO

-CPFA Nieve: nieve en el momento del accidente. Campo de condiciones ambientales con posibles valores SI o NO

-CPSV Mojada: estado mojado de la vía. Campo de estado de la vía con posibles valores SI o NO

-CPSV Aceite: estado con aceite de la vía. Campo de estado de la vía con posibles valores SI o NO

-CPSV Barro:  vía con barro. Campo de estado de la vía con posibles valores SI o NO

-CPSV Grava Suelta: vía con grava suelta. Campo de estado de la vía con posibles valores SI o NO

-CPSV Hielo: vía con hielo. Campo de estado de la vía con posibles valores SI o NO

-CPSV Seca Y Limpia: vía seca y limpia. Campo de estado de la vía con posibles valores SI o NO

-Nº VICTIMAS: número de víctimas del accidente, el cual es diferente de los implicados. Este valor se repite en cada fila asocida a un mismo parte, y muestra el total de heridos leves, graves o muertos. Cada fila de por sí ya nos estaría aportando esta información y en mayor detalle, al incluir campos para identificar el tipo de persona y la lesividad. Pero este campo puede resultar interesante para simplificar posibles consultas en las que queramos filtrar de alguna manera los accidentes por las personas implicadas.

-TIPO ACCIDENTE: se especifica a qué tipo de accidente corresponde el registro, con una serie de valores posibles prefijados (colisión doble, múltiple, etc.) Al analizar este campo con una faceta de texto se encuentran 130 valores correspondientes a "OTRAS CAUSAS" y ningún registro vacío. Habría que tener en cuenta ésto en posibles transformaciones o consultas, por ejemplo de agrupación por tipo de accidentes, para que no desvirtue el resultado.

-Tipo Vehículo: se identifica el tipo de vehículo afectado en el accidente, de nuevo mediante una serie de valores prefijados (turismo, furgoneta,etc.). El contenido de este campo está relacionado con el siguiente ("TIPO PERSONA"), ya que el vehículo debería estar identificado en las filas correspondientes a conductores, y puede estar identificado en el caso de viajeros o testigos. En los casos de peatones sólo deberíamos encontrarnos con el valor "NO ASIGNADO", aunque se obervan 3 filas con valor incorrecto. Aunque se deberían corregir, ya que se deben seguramemte a errores en la introducción de datos, no supone un problema ya que no cambia el tipo de consultas que se querrían hacer para el caso de los peatones (no tendría sentido querer saber algo del tipo de vehículo, salvo para detectar estos valores inesperados). Además puede que ese información corresponda a alguna particularidad en el accidente que desconocemos.

![Screenshot](Imagenes/Peaton_vehiculo.jpg)

-TIPO PERSONA: observamos el tipo de persona implicada en el accidente, con los valores posibles: conductor, peatón, testigo o viajero. Siguiendo con el análisis del campo anterior, al filtrar por viajeros nos encontramos con 15 valores "NO ASIGNADO" en el tipo de vehículo. No queda claro si son errores ya que aparentemente al identificar a una persona como viajero se indica que se encontraba en un vehículo en el momento del accidente, pero tal vez las circunstancias del accidente (vehículo parado, retirado del lugar, etc.) requerían identificarlo así. Se les podría asignar el valor más común en los casos de viajeros("TURISMO"), por dejar el campo totalmente estandarizado, pero podríamos estar perdiendo información real.

-SEXO: identificación del sexo de la persona, con los valores: hombre, mujer o no asignado. Al estudiar los posibles valores vemos que hay casi un 5% de casos sin identificar (valor "NO ASIGNADO"). Como se ha visto en campos anteriores, habrá que tenerlo en cuenta a la hora de devolver resultados en las consultas para no mostrar información desvirtuada (por ejemplo, un porcentaje irreal de accidentes leves en mujeres), si no queremos recurrir a eliminar de nuestro dataset directamente estos datos con información sesgada.

-LESIVIDAD: se indica el tipo de lesión que ha sufrido la persona implicada en el accidente, con un código de valores: IL(ilesa), HL(herida leve), HG(herida grave), MT(muerte) o NO ASIGNADA. En este caso no nos encontramos con incongruencias (por ejemplo, todos los testigos resultaron ilesos), pero sí de nuevo con falta de información en muchos casos, lo que parece corresponderse a accidentes que no se pudieron documentar correctamente, pero que debemos considerar.

-Tramo Edad: nos encontramos con una serie de rangos establecidos de edades para identificar cada caso, y el valor "DESCONOCIDA" para las personas a las que no se pudo determinar su edad en el momento de registrar el accidente (un 7.6% de los casos).
Para poder trabajar con mayores posibilidades en las consultas finales, se podría añadir campos creados a partir de éste. Con la edad mínima y máxima, por ejemplo, realizaríamos consultas más simplificadas sobre rangos de edades mayores, etc.


Licencia de uso de los datos:
El conjunto de datos se ha obtenido del Portal de datos abiertos del Ayuntamiento de Madrid, por lo que son de uso público y abierto para su explotación.

![Screenshot](Imagenes/Licencia_dataset.jpg)

Las condiciones de uso de la licencia estipulan : "Las condiciones generales permiten la reutilización de los documentos para fines comerciales y no comerciales. Se entiende por reutilización el uso de documentos que obran en poder del Ayuntamiento de Madrid, siempre que dicho uso no constituya una actividad administrativa pública."

Dada la naturaleza de este trabajo, el objetivo es favorecer la reutilización de los datos mediante una transformación a la web semántica y enlazarlos con otros datos de interés. Su uso, por tanto, quedaría igualmente enmarcado en el campo de los datos abiertos y siempre con la idea de ser utilizados de forma transparente y gratuita por otros.

c) Estrategia de nombrado:

Para definir la estrategia de nombrado, en primer lugar elegimos la forma de las URIs que vamos a emplear. Para la definición de las ontología a emplear en el mapping usaré almohadilla (#), ya que de esta manera se podrá acceder a todos los términos disponibles en cada vocabulario. En el caso de los identificadores únicos de cada persona implicada en un accidente (columna 'ID') se empleará la barra inclinada (/), debido a que contamos con muchos registros en el dataset.
El dominio seleccionado para la definición de nuestros recursos es: 'http://example.org/'.
La ruta de las URIs será: 'http://example.org/resources/'.
El patrón para los recursos a definir es 'http://example.org/resource/<identificador>'; en el caso de los términos ontológicos que haya que definir será 'http://example.org/ontology/accidents#<término>'.
Para los recursos que identifican a cada persona se utilizará el patrón 'http://example.org/resource/PersonInAccident/<identificador>', mientras que para los accidentes será 'http://example.org/resource/Accident/<identificador>'.
En algunos casos los términos a utilizar para definir las propiedades implica usar '/', ya que se trata de vocabularios muy extensos, como es el caso de dbpedia-owl o schema.

d) Desarrollo del vocabulario:

Primero se establecen las relaciones directas con la columna ID, es decir todos los campos que describen a una persona implicada en el accidente.
Para la definición de las URIs de cada individuo implicado en un accidente usaremos la expresión en grel: "PersonInAccident/"+value
En el caso de las URIs de cada Nº de parte: "Accident/"+value
A continuación fijamos las relaciones entre un nùmero de parte y sus campos asociados, que muestran las características que tuvo un accidente dado. Mediante la edición del RDF Skeleton establecemos dichas relaciones, mediante los términos y vocabularios implicados. Los términos a emplear serán en la medida de lo posible reutilizados de vocabularios ya existentes.

![Screenshot](Imagenes/skeleton.jpg)

Una vez creado el mapping del conjunto de datos, podemos observar el esquema resultante mediante un visualizador de Turtle. En el siguiente ejemplo vemos un recurso 'Accidente' con dos recursos 'Personas' aspciados, junto con todos sus parámetros y valores establecidos. Los términos específicos asociados a la ontología 'ont' que he definido los he omitido en la visualización, ya que dicha ontología no está publicada, al igual que la relación 'owl:sameas' del distrito con wikidata, la cual entorpecía la visión del esquema principal.

![Screenshot](Imagenes/esquema.jpg)

e) Proceso de transformación:

Mediante la herramienta OpenRefine se han realizado una serie de transformaciones sobre los datos originales para adecuarlos a su uso posterior.
A continuación se detallan los campos que han sufrido algún tratamiento de datos, para adecuarlos a la estructura deseada.

-FECHA: el objetivo es separar el contenido de este campo para poder darle utilidad posterior.
En primer lugar se separan los distintos valores que contiene en varias columnas (Split into several columns...) de información mediante el espacio separador que delimita los diferentes campos. A continuación, de las nuevas columnas creadas, eliminamos la que contiene el día de la semana, ya que este campo ya se encuentra en el dataset. El siguiente paso es renombrar las columnas creadas (MES, DIA_MES, AÑO, HORA, HUSO HORARIO) para poder referenciarlas mejor. Las columnas 'HORA' y 'HUSO_HORARIO' se mantienen a pesar de no aportar información ya que en siguientes años pueden contener datos reales.
Creamos una nueva columna 'FECHA' a partir de la coumna 'MES' mediante grel, concatenando la información de dia, mes, año para poder reconstruir correctamente la fecha:

value+" "+cells["DIA_MES"].value+" "+cells["AÑO"].value

Por último, se aplica una transformación común a fecha para cambiar el formato de esta nueva columna 'FECHA' y se reordenan las columnas para una mejor visualización.

![Screenshot](Imagenes/Tratamiento_Fecha.jpg)

-RANGO HORARIO: para poder contar con la información por separado del rango horario, se aplica una separación en varias columnas por el separador " ". Después de borrar las dos columnas de los textos "DE" y "A" y renombrar las otras dos columnas obtenidas, contamos con las horas de inicio y fin por separado. Así se podrán realizar consultas de acontecimientos en rangos mayores de tiempo, usando las horas inicial y final que se deseen.

![Screenshot](Imagenes/Tratamiento_RangoHorario.jpg)

-LUGAR ACCIDENTE: en los casos en los que este campo presenta la intersección entre las dos calles donde se produjo el accidente, el orden en que se muestran las calles puede no ser el mismo. Para normalizar la información de este campo aplicamos una agrupación y edición (Cluster and edit...). Aplicamos todos los cambios que sugiere Openrefine, ya que no afectan más que a los casos descritos (intersección entre dos calles)

![Screenshot](Imagenes/Tratamiento_Lugar.jpg)

Para disponer de la información de las calles separadas aplicamos a continuación la división de la columna por el separador " - ". De este modo contamos con información mejor estructurada. Así sabemos por ejemplo, qué accidentes están referenciados a una sóla calle, usando la columna 'LUGAR DEL ACCIDENTE 2' y observando los casos en los que esté vacía. Además esta operación nos permitirá aplicar posteriormente un posible enlazado de estos datos mediante un proceso de reconciliación.
Aplicamos a las dos columnas creadas una transformación común para colapsar los espacios en blanco consecutivos, presentes en la mayoría de valores. Así podemos crear dos nuevas columnas, que luego uniremos para crear la columna 'TIPO VALOR', para mostrar el valor "NUM", "KM" o vacío según corresponda al accidente en cuestión. Estos valores ("NUM", "KM") sólo aparecen en la primera calle que se indica, y en estos casos además no hay segunda calle indicada.
Para crear las columnas 'KM' y 'NUM', a partir de la columna 'LUGAR DEL ACCIDENTE 1', y unirlas posteriormente usamos grel:

columna 'KM': endsWith(value," KM. ")
columna 'NUM': endsWith(value," NUM ")
columna 'TIPO VALOR', a partir de la columna 'NUM': value+cells["KM"].value

Para finalizar, reemplazamos los valores " NUM " y " KM. " de la columna 'LUGAR DEL ACCIDENTE 1', para limpiar los datos ante un posible enlazado posterior mediante la reconciliación de las calles.

![Screenshot](Imagenes/Tratamiento_Lugar_2.jpg)

-Nº: para corregir los valores no numéricos de este campo, aplicamos un filtro de texto para seleccionar las celdas con valor "   " y las reemplazmos por un 0. Después se aplica una transformación común a número para asegurar que todo esta correcto. Si aplicamos ahora una faceta numérica observamos que ya no hay valores no numéricos. De este modo estandarizamos los casos de accidentes con el valor 0, que corresponden a accidentes en los que no se anotó un número por carecer de sentido o por otro motivo.

- Nº PARTE: para poder trabajar con un identificador único en cada fila que nos permita realizar correctamente la estrategia de nombrado y el mapeado de los datos, creamos una nueva columna llamada 'ID'. Esta columna la creamos en base a ala columna 'Nº PARTE', aplicando el código en grel: cell+value. A continuación limpiamos el texto central que se genera ("com.google.refine.expr.CellTuple"), reemplazándolo por texto vacío. Por último se reemplazan las barras inclinadas por barra baja ("/" por "_"), tanto en la columna 'ID' como en 'Nº PARTE', para evitar posibles confusiones en el esquema RDF a la hora de aplicar la estrategia de nombrado.

![Screenshot](Imagenes/Columna_ID.jpg)

-Tramo Edad: al igual que en el caso del rango horario, separamos el campo por sus espacios y obtenemos la edad mínimaa y máxima, una vez eliminadas las columnas irrelevantes.
En el caso del tramo de edad "DE MAS DE 74 AÑOS" tenemos que filtrar y editar los valores obtenidos en la columna 'EDAD MINIMA', para poner un valor de 75 en estas celdas, donde nos encontramos con el valor "MAS". Para terminar creamos una nueva columna basada en el campo 'EDAD MAXIMA' que habíamos creado anteriormente para poner el valor null si el tramo de edad es de más de 74 años:

if (cells["EDAD MINIMA"].value==75,null,value)

Así se podrán seleccionar los rangos de edades sobre los que consultar información, directamente en el campo original 'Tramo Edad' o con tramos mayores de edades usando estas dos nuevas columnas.

![Screenshot](Imagenes/Tratamiento_TramoEdad.jpg)

Para finalizar el proceso de transformación, se eliminan los espacios en blanco innecesarios en las columnas 'Tipo vehículo', 'TIPO PERSONA', 'SEXO' y 'LESIVIDAD'. Para ello utilizamos la transformación común correspondiente en la edición de celdas.

f) Enlazado:

Usando OpenRefine se ha realizado el enlazado de los datos con Wikidata en los campos que se detallan a continuación:

- Distrito: la reconciliación de los datos en este caso se produce al 100%, pudiéndose enlazar los 21 valores posibles de distritos de la ciudad de Madrid
En base a los datos reconciliados podemos crear una columna con la referencia en wikidata, mediante la expresión en grel: 'https://www.wikidata.org/wiki/'+ cell.recon.match.id

![Screenshot](Imagenes/wikidata.jpg)

- Lugar accidente 1: una vez separados los datos del campo original 'Lugar accidente' podemos enlazar estos datos, obteniendo una reconciliación del 47% en este caso para los 2263 valores posibles. Este porcentaje se puede mejorar progresivamente seleccionando las sugerencias de OpenRefine en las calles con una reconciliación en duda.
- Lugar accidente 2: al reconciliar estos datos se obtiene un 39% de los 1771 valores existentes, lo que podemos asociar a que hay más calles secundarias en este campo, las cuales no se referencian en wikidata.

![Screenshot](Imagenes/Enlazado.jpg)

A partir de los datos enlazados podemos obtener dos nuevas columnas (Add columns from reconciled values...) con la información del área que ocupa cada distrito, expresada en kilómetros cuadrados, y con su población. Dicha información puede resultar útil para realizar consultas de ranking de incidencias por distrito atendiendo a su tamaño o población, por ejemplo en qué distrito se producen más accidentes mortales por kilómetro cuadrado. Sabemos que los datos de la columna 'Distrito' están totalmente reconciliados, por lo que en todas las filas obtendremos un valor de área y población válido. Una vez creadas las dos nuevas columnas, se renombran a 'POBLACION' y 'AREA' para una mejor identificación.

![Screenshot](Imagenes/Poblacion_Area.jpg)


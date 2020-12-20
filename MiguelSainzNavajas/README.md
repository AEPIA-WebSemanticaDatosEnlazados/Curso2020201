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

-LUGAR ACCIDENTE: en este campo se destina a la información de la calle, o cruce de dos calles, donde tuvo lugar el accidente. A la hora de transformar el dataset, este campo debería ser tratado para separar las dos calles en el caso de que el accidente corresponda a un cruce. En estos casos se separan con un guión en la columna original. En los accidentes cuya localización se exprese con una sola calle encontramos al final el texto "NUM", indicando que en el campo siguiente ("Nº"), veremos un valor numérico asociado al número de la calle.
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

-Nº VICTIMAS: número de víctimas del accidente, el cual es diferente de los implicados. Este valor se repite en cada fila asocida a un mismo parte, y muestra el total de heridos leves, graves o muertos. Cada fila de por sí ya no estaría aportando esta información y en mayor detalle, al incluir campos para identificar el tipo de persona y la lesividad. Pero este campo puede resultar interesante para simplificar posibles consultas en las que queramos filtrar de alguna manera los accidentes por las personas implicadas.

-TIPO ACCIDENTE: se especifica a qué tipo de accidente corresponde el registro, con una serie de valores posibles prefijados (colisión doble, múltiple, etc.) Al analizar este campo con una faceta de texto se encuentran 130 valores correspondientes a "OTRAS CAUSAS" y ningún registro vacío. Habría que tener en cuenta ésto en posibles transformaciones o consultas, por ejemplo de agrupación por tipo de accidentes, para que no desvirtue el resultado.

-Tipo Vehículo: se identifica el tipo de vehículo afectado en el accidente, de nuevo mediante una serie de valores prefijados (turismo, furgoneta,etc.). El contenido de este campo está relacionado con el siguiente ("TIPO PERSONA"), ya que el vehículo debería estar identificado en las filas correspondientes a conductores, y puede estar identificado en el caso de viajeros o testigos. En los casos de peatones sólo deberíamos encontrarnos con el valor "NO ASIGNADO", aunque se obervan 3 filas con valor incorrecto, y que se deberían corregir ya que se deben seguramemte a errores en la introducción de datos

![Screenshot](Imagenes/Peaton_vehiculo.jpg)

-TIPO PERSONA: observamos el tipo de persona implicada en el accidente, con los valores posibles: conductor, peatón, testigo o viajero. Siguiendo con el análisis del campo anterior, al filtrar por viajeros nos encontramos con 15 valores "NO ASIGNADO" en el tipo de vehículo. No queda claro si son errores ya que aparentemente al identificar a una persona como viajero se indica que se encontraba en un vehículo en el momento del accidente, pero tal vez las circunstancias del accidente (vehículo parado, retirado del lugar, etc.) requerían identificarlo así. Tal vez se les podría asignar el valor más común en los casos de viajeros("TURISMO"), por dejar el campo totalmente estandarizado, pero podríamos estar perdiendo información real.

-SEXO: identificación del sexo de la persona, con los valores: hombre, mujer o no asignado. Al estudiar los posibles valores vemos que hay casi un 5% de casos sin identificar (valor "NO ASIGNADO"). Como se ha visto en campos anteriores, habrá que tenerlo en cuenta a la hora de devolver resultados en las consultas para no mostrar información desvirtuada (por ejemplo, un porcentaje irreal de accidentes leves en mujeres), si no queremos recurrir a eliminar de nuestro dataset directamente estos datos con información sesgada.

-LESIVIDAD: se indica el tipo de lesión que ha sufrido la persona implicada en el accidente, con un código de valores: IL(ilesa), HL(herida leve), HG(herida grave), MT(muerte) o NO ASIGNADA. En este caso no nos encontramos con incongruencias (por ejemplo, todos los testigos resultaron ilesos), pero sí de nuevo con falta de información en muchos casos, lo que parece corresponderse a accidentes que no se pudieron documentar correctamente, pero que debemos considerar.

-Tramo Edad: nos encontramos con una serie de rangos establecidos de edades para identificar cada caso, y el valor "DESCONOCIDA" para las personas a las que no se pudo determinar su edad en el momento de registrar el accidente (un 7.6% de los casos).
Para poder trabajar con mayores posibilidades en las consultas finales, se podría añadir campos creados a partir de éste. Con la edad mínima y máxima, por ejemplo, realizaríamos consultas más simplificadas sobre rangos de edades mayores, etc.


Licencia de uso de los datos:
El conjunto de datos se ha obtenido del Portal de datos abiertos del Ayuntamiento de Madrid, por lo que son de uso público y abierto para su explotación.

![Screenshot](Imagenes/Licencia_dataset.jpg)

Las condiciones de uso de la licencia estipulan : "Las condiciones generales permiten la reutilización de los documentos para fines comerciales y no comerciales. Se entiende por reutilización el uso de documentos que obran en poder del Ayuntamiento de Madrid, siempre que dicho uso no constituya una actividad administrativa pública."

Dada la naturaleza de este trabajo, el objetivo es favorecer la reutilización de los datos mediante una transformación a la web semántica y enlazarlos con otros datos de interés. Su uso, por tanto, quedaría igualmente enmarcado en el campo de los datos abiertos y siempre con la idea de ser utilizados de forma transparente y gratuita por otros.




1.INTRODUCCIÓN:

Este trabajo consistirá en la generación de datos enlazados en formato RDF, a partir de un conjunto de datos estructurado relativo a los accidentes sucedidos en la ciudad de Madrid. Para ello se realizará un análisis y preparación de los datos, todo ello orientado a una explotación posterior de los mismos mediante Sparql. Así mismo se definirá una ontología adecuada al dominio de los datos y se llevará a cabo el enlazado con otros conjuntos de datos.
Una vez transformados a RDF los datos podrán ser consultados para obtener información relevante.

2. PROCESO DE TRANSFORMACIÓN

a) Selección de la fuente de datos:

Como punto de partida analizé varios de los portales de datos abiertos sugeridos en las lecciones de la asignatura. En el caso de los datos abiertos del Gobierno de España (datos.gob.es) me encontré con multitud de datos publicados con contenidos muy pobres, así como datasets de tipo resumen o demasiado generales. Al no contar con información interesante que analizar y transformar decidí usar otras portales, en concreto el Portal de datos abiertos del Ayuntamiento de Madrid (https://datos.madrid.es/portal/site/egob/). Aquí ya se mostraban datasets con mayor riqueza en la información contenida, lo que permite realizar consultas finales más interesantes sobre los datos. Entre varios que preseleccioné (reservas de motos, objetos perdidos en taxis, incidencias de bomberos, etc.) el que me pareció más útil para ser explotado, tanto por los campos contenidos y por el número de registros, fue el de Accidentes de tráfico de la Ciudad de Madrid:
-https://datos.madrid.es/portal/site/egob/menuitem.c05c1f754a33a9fbe4b2e4b284f1a5a0/?vgnextoid=7c2843010d9c3610VgnVCM2000001f4a900aRCRD&vgnextchannel=374512b9ace9f310VgnVCM100000171f5a0aRCRD&vgnextfmt=default

Este conjunto de datos se puede obtener para periodos de un año completo, por lo que trabjaré con el año 2018, ya que los datos de 2019 y 2020 son provisionales.
Cada registro corresponde a una persona afectada en un accidente dado, y los campos de información se corresponden con datos sobre la ubicación, el estado del asfalto, lesividad, sexo, edad,etc. En el apartado siguiente (Análisis de los datos) se verá cada campo en detalle


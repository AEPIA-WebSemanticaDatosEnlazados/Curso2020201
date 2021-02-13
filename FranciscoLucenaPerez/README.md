Memoria
Datos climáticos obtenidos por Red de Estaciones Agroclimáticas [2002-2020]. Red de Alerta e Información Fitosanitaria (RAIF)

1.- Introducción:
En este trabajo, transformaremos un conjunto de datos basados en el registro de distintas variables meteorológicas en estaciones meteorológicas de Andalucía. Concretamente se trata de datos climáticos obtenidos por la red de estaciones agroclimáticas de la Red de Alerta e Información Fitosanitaria (RAIF). 
La transformación a datos enlazados permitirá la explotación de los mismos.

2.- Proceso de transformación

2.1.- Selección de la fuente de datos
Estos datos se obtienen del portal de la Junta de Andalucía de datos abiertos (https://www.juntadeandalucia.es/datosabiertos/portal/dataset/raif-clima).
Los requisitos de selección de la fuente de datos serán:

  2.1.1.- Escenario real: datos climáticos de la red de estaciones agroclimáticas de Andalucía.
  
  2.1.2.- Disponibilidad de los datos: 
  
  2.1.2.1.- Datos en un formato procesable automáticamente: los datos están comprimidos en un .zip, que contiene ficheros .xml.
  
  2.1.2.2.- Licencia. Reconocimiento 4.0 Internacional (CC BY 4.0) 
  
  This is a human-readable summary of (and not a substitute for) the license. Advertencia. 
  Usted es libre de:
  •	Compartir — copiar y redistribuir el material en cualquier medio o formato
  •	Adaptar — remezclar, transformar y crear a partir del material
  •	para cualquier finalidad, incluso comercial.
  •	El licenciador no puede revocar estas libertades mientras cumpla con los términos de la licencia.
  Bajo las condiciones siguientes:
  • Reconocimiento — Debe reconocer adecuadamente la autoría, proporcionar un enlace a la licencia e indicar si se han realizado cambios<. Puede hacerlo de cualquier manera         razonable, pero no de una manera que sugiera que tiene el apoyo del licenciador o lo recibe por el uso que hace. 
  •	No hay restricciones adicionales — No puede aplicar términos legales o medidas tecnológicas que legalmente res trinjan realizar aquello que la licencia permite.
  Avisos :
  •	No tiene que cumplir con la licencia para aquellos elementos del material en el dominio público o cuando su utilización esté permitida por la aplicación de una excepción o       un límite.
  •	No se dan garantías. La licencia puede no ofrecer todos los permisos necesarios para la utilización prevista. Por ejemplo, otros derechos como los de publicidad, privacidad,     o los derechos morales pueden limitar el uso del material.
  2.1.3.- Posibilidad de enlazar con entidades genéricas (lugares)

2.2.- Análisis de datos
Al descomprimir el zip, disponemos de 3 tipos de ficheros:
•	Datos: Clim_Diario_2020.xml, Clim_Diario_2019.xml … Cargaremos sólo 2020, por agilidad en los procesos y capacidad de computación.
•	Descripción de los campos: Descripcion_Campos.xml.
•	Datos sobre las estaciones: Identificacion_EstacionesClima.xml.
En la descripción de los campos, los nombres no coinciden completamente con los campos que después encontramos en el fichero de datos, pero son fácilmente reconocibles:

![descripcion campos](imagenes/descripcion_campos.png)

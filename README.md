# RETO 3 - Procesamiento

## ¿Qué hay que hacer?

Obtener información (resumida) sobre un flujo de datos que supera la capacidad de almacenamiento en disco del sistema.

La información que tenemos de los retos anteriores, parece decir que podemos almacenar varios cientos de líneas por segundo en nuestra base de datos MySQL. En este reto, el objetivo será procesar 5000 HTTP request por segundo y obtener el número de mensajes recibidos por el sistema cada 10 milisegundos, cada segundo y cada minuto. El resultado de esas estadísticas se podrá obtener por consola ejecutando un comando distinto para cada uno de los tres periodos de tiempo.

## ¿Cómo hacerlo?

Durante la sesión del 25/05 se discutieron las alternativas de implementación. Se pasó por los siguientes puntos.

### Procesamiento codificado en el mismo servidor web

Podría plantaerse obtener los resúmenes en el servidor web y almacenarlos. Habría que tener en cuenta que intentar almacenar todos los eventos en un log y después sacar las estadísticas puede no dar el rendimiento requerido (en el momento en el que se pasa la información por disco duro, hay que afinar mucho para llegar a las 5000 TPSs). Se llegará más fácil al objetivo si en el disco duro se almacenan solamente los resúmenes (aunque esto tiene la desventaja de que se pierde información que podría ser útil en el futuro). Eso nos lleva a plantearnos un servidor web más ligero y en el que podamos cacharrear al máximo, por ejemplo, los que teníamos implementados en retos anteriores. Parece que con ellos se podría obtener los resúmenes y alcanzar el rendimiento requerido.

### Procesamiento usando Stream Processing

Los motores de Stream Processing (Storm, Flink, Samza, Apama, IBM InfoSphere Streams, ...) sirven para procesar grandes flujos de información con baja latencia. En la sesión se hablo de su funcionamiento basado en ventanas de memoria y flexibilidad en el número de veces que se procesa cada evento. Son una buena herramienta para almacenar solamente un resumen de lo que sucede en tiempo real y descargar así a la capa de almacenamiento, como se requiere en este caso ... aunque su mayor ventaja es la de obtener resultados con muy baja latencia.

### Cambio de herramienta de almacenamiento

Las alternativas anteriores no consideran que el cuello de botella puede estar en el uso de una base de datos relacional como MySQL como almacenamiento. Habría que ver si una base de datos más optimizada para inserciones sencillas y rápidas puede o no con los 5000 mensajes por segundo. Obtener después las estadísticas no parece complicado.

## Herramientas adicionales

### Generador de carga

Se seguirá usando el Tsung, subiendo el ritmo de mensajes generados. La imagen de docker correspondiente estará en este repositorio Git. El Tsung marca cada uno de los eventos con el timestamp en el que fue generado con resolución de milisegundos.

### Gestor de colas

Un gestor de colas puede hacer la función de buffer intermedio para amortiguar picos de tráfico en la entrada del sistema. También se podría plantear como un almacenamiento a más largo plazo de todo lo que entra en el sistema.

## ¿Qué tareas hay?

### Modo de trabajo

Esta vez se esperan varias soluciones independientes, pero no tienen porqué ser una por persona. Se pueden formar grupos de trabajo.

Cada grupo de trabajo creará un repositorio en GitHub con la solución (e.g. de nombre RETO3). Ese repositorio contendrá:
- un README.md en el que se plantee la arquitectura general de la solución.
- el generador de carga (lo voy a poner en este repositorio para que todos usemos exactamente la misma configuración).
- una o varias imagenes docker que implementen la solución.
- un docker-compose.yml que lance el sistema.
- tres scripts que saquen por consola el número de mensajes recibidos por cada uno de los tres periodos (pondré tres scripts de ejemplo en este repositorio para que se vea el formato y usemos el mismo).

### Hitos

- El sábado día 28 de Mayo deben estar disponibles los README.md. Eso nos permitirá comentar las soluciones antes de empezar (aunque esperar o considerar los comentarios es opcional) y si es posible repartir las soluciones para que no sean todas iguales. No hace falta explayarse mucho. En cuanto lo vayáis teniendo, anunciarlo en el slack.

- Si puede ser, informad de una versión intermedia, aunque falle o esté incompleta, durante la semana que viene.

- Si puede ser, tened algo que funcione antes del lunes día 6 de Junio. Así podemos echar todos un vistazo a lo que han hecho los demás antes de la sesión siguiente. Además permitirá instalar las soluciones en un par de máquinas en BlueTab para hacer pruebas.

## Criterios de evaluación

### Escalabilidad Horizontal

El principal criterio de evaluación va a ser sobre lo preparada que esté la solución para procesar un tráfico aún mayor (20000 HTTP requests, 100000 HTTP requests) si añadimos más servidores al sistema.

### Flexibilidad de cambio del procesamiento

Evaluación de lo difícil que es cambiar el algoritmo que procesa los datos para implementar otro tipo de informes. Esto incluye el detalle con el que se almacena la información. Es decir, si es posible almacenar todos los eventos en alguna parte, eso da una flexibilidad total de procesar de cualquier manera los eventos en el futuro.

### Rendimiento

Con los siguientes aspectos:
- HTTP requests que procesa como máximo el sistema
- Retardo desde que se produce el HTTP requests hasta que ese evento se puede ver en las estadísticas.
- CPU consumida por el sistema
- Memoria consumida por el sistema
- Uso de disco duro
 
## A LA CARGA!!!!

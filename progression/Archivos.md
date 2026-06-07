# ESTRUCTURAS PARA LOS ARCHIVOS>
Aca vamos a ver como se va a estructurar los archivos escenciales para el programa.
## Archivo: main.py
### Funcionamiento y Ubicacion
El archivo main.py sera el motor de arranque para este sistema, en el estaran las lineas de codigo que arrojen respuestas positivas y negativas, ademas que desde este archivo controlaremos inputs y cuando ejecutar a la crew para poder poner a los agentes en encendido.
Este archivo debe de alojarse en la carpeta principal del proyecto.
### Sintaxis y Semantica del archivo
En este archivo no abran metodos solamente una verificacion que hara que este archivo (main.py) sea el archivo donde comienza la ejecucion del programa:

if __name__ == "__main__":

__name__ no es mas que una variable que te dara como resultado el nombre del modulo donde esta escrito ese codigo, que en nuestro caso, es main.py, entonces al ser "main.py" == "main.py" se ejecuta el codigo dentro de esa estructura if, el cual claro, es el codigo inicializador, con preguntas, inputs, etc.
Para que esto funcione realmente claro que ademas de programar inputs y mensajes deberemos de llamar al metodo que se encarga de ejecutar a nuestra crew que hara el trabajo. Para eso creamos un objeto con la clase de tu archivo crew.py:
crew = claseConstructoraDeCrew()
Luego escribimos:
crew.run()
De esta manera ejecutamos la crew y empiezan su labor los agentes
*ACLARACION*: Obviamente el metodo .run() debe de estar correctamente definido en tu archivo crew.py


## Archivo: crew.py
### Funcionamiento y Ubicacion

Este archivo debe de alojarse en la carpeta principal del proyecto.
### Sintaxis y Semantica del archivo
Este archivo contendra clases. La mas importante es, claro, la clase que tiene el control de ese archivo y sus clases/metodos, ademas es esta misma clase la que se coloca como valor de crew en el archivo main.py
Ahora bien como dije este archivo (o modulo tambien se puede llamar) tiene varias clases no solo esta clase "manager".
Las otras clases que se definen son los conjuntos de agentes y tareas, usados para poder darle la informacion necesaria al framework CrewAI.
*¿Como?* - Usamos algo llamado 'decoradores', los cuales son una funcion especial que se encargan de agregar o extender contenido a un metodo o clase ya hecha sin cambiar su codigo fuente. Por ejemplo si queremos a una clase agregarle un metodo extra sin necesariamente modificar su codigo base en su archivo simplemente usamos el decorador para agregarlo. En este caso tendremos una clase en crew.py que extendera a la clase CrewBase de crewai el cual en su codigo base tiene los metodos para poder ejecutar a los agentes.
*¿Que es lo necesario para poder tener mi conjunto de agentes?* - La clase CrewBase tiene un metodo: kickoff(inputs)
Ese metodo se encuentra dentro de .crew() y esta clase se encuentra justamente en CrewBase, por eso al nosotros extender desde CrewBase heredaremos naturalmente esa clase y ese metodo.
Dentro de nuestra clase que extendera de CrewBase hara falta especificar un metodo que retorne un objeto Crew(). Este metodo debera de especificar los agentes, las tareas, el tipo de proceso y si activa el modo verboso (verbose).
Los valores para agents y tasks (agentes y tareas) seran heredados de la clase mayor CrewBase entonces para ambos simplemente se hace:
agents=self.agents
tasks=self.tasks
De esta forma establecemos que para este objeto Crew() los agentes que hereda CrewBase y lo mismo con las tareas.
Ahora te preguntaras cuales son esos agentes y cuales son esas tareas, bueno esos agentes y tareas deben de definirse antes del metodo que extiende de crew.
Estos metodos extenderan de agent y task y serviran para poder agregar instancias de agentes y tareas, mismas instancias que se almacenan en las variables agents y tasks de CrewBase, por eso ponemos agents=self.agents y tasks=self.tasks (ya que recordemos, self es una variable especial que refiere a ti mismo por lo que busca agents y tasks en el metodo, al no encontrarlo va recorriendo a su clase padre (crew) y al no encontrarlo tampoco accede a su clase abuelo (CrewBase) donde encuentra correctamente las propiedades).
Luego esta la propiedad process la cual dice como los agentes colaboraran dentro de la crew. Se debe de especificar que tipo de proceso usara esa crew, solo tiene dos posibles valores:
process=Process.sequential - Al decirle sequential (secuencial) los agentes siguen un orden lineal donde las tareas se ejecutan una tras otra y toman como contexto o entrada la salida de la tarea anterior. Mas predecible y facil de controlar.
process=Process.hierarchical - Este valor establece un proceso jerarquico (hierarchical) donde se emula una estructura corporativa donde un agente gerente - de ahora en adelante agente orquestador - analiza la tarea y delega sub-tareas a otros agentes en base a sus habilidades, luego revisa y valida el resultado final antes de finalizar.
Para poder tener nuestros propios agentes y tareas se hacen como dije con metodos que extiendan de sus respectivas clases.
#### Para agentes
Para poder crear nuestro agente haremos un metodo que extienda de @agent y retorne una instancia de Agent() con los siguientes valores:
config - Aqui va un arreglo con datos del agente: rol, objetivo e historia de contexto del agente.
allow_delegation - Esta es una opcion para permitir la delegacion de tareas. Sus posibles valores son True o False.
tools - De forma opcional se puede colocar una lista con las herramientas que el agente puede utilizar, tales como lector de archivos, transformador de archivos, creacion de archivos especiales como de grafos y diagramas, etc.
verbose - Posibles valores True o False (verdadero o falso). Esta opcion permite establecer si el agente enviara constantemente notificaciones de estado (solucionando tal tarea, ya solucionada, siguiendo con tarea X, etc) y logs.
#### Para tareas
Las tasks son un tantito similares:
config - Aqui va un arreglo con datos del la tarea: descripcion de la tarea, output esperado
agent - Agente al que le delegaremos esta tarea. Usaremos los nombres establecidos en los metodos que extienden de "agent". Podemos tambien dejar esta opcion vacia pero tendra distintos impactos dependiendo de que valor sea nuestro process:
Si process es Process.sequential - En este caso CrewAI arrojara un error ya que en una ejecucion secuencial se debe de tener una tarea como minimo ya que la entrada de una tarea es la salida de otra.
Si process es Process.hierarchical - En este caso sucede algo bastante distinto: el agente orquestador dara por hecho que la tarea es para el y podra ir delegando sub tareas para esta tarea a otros agentes.
Por ultimo esta la propiedad verbose que puede ser true o false (verdadero o falso) y esta sirve para establecer si el modelo enviara constantemente notificaciones de estado (solucionando tal tarea, ya solucionada, siguiendo con tarea X) y logs.
#### Forma sencilla para importar agents y tasks
Una via util de poder configurar agentes y tareas es usando archivos .YAML en el directiorio del proyecto.
Basicamente crearemos una carpeta llamada config donde dentro de ella tendremos los dos archivos "agents.yml" y "tasks.yml" son un conjunto de la misma estructura especificada pero en formato .YAML:
#### config para Agentes
nombreDelAgente:
  role: "Rol del agente",
  goal: "Objetivo del agente",
  backstory: >
   Aca va toda la historia detras de este agente. La idea esa abusar de esta funcion y escribir inclusive parrafos de informacion con tal de que la IA adopte la personalidad que queramos.
  verbose:True (Para poder ver todas las interacciones entre tus agentes, y recibir notificaciones.
####config de Tasks
nombreDeLaTarea:
  description: >
   Descripcion de la tarea se puede ser tan especifico como se requiera.
  expected_output: >
   Esto seria que esperas recibir al final de la ejecucion de la tarea
### Resumen
En nuestro archivo crew.py definiremos una clase con metodos para ejecutar a nuestra crew de agentes, metodos que extiendan de @CrewBase para tener una base para CrewAI y dentro de esos metodos los metodos necesarios para poder instanciar agentes (Agents), tareas (Tasks) y la crew (Crew).

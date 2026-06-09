# Funcionamiento logico del framework _CrewAI_
Para saber como funciona al minimo este framework me base en algunos de los proyecto del repositorio "crewAI-examples" en GitHub. En el hay varios ejemplos variados y excelentes para entender el funcionamiento de este framework, lo recomiendo completamente:\
[Acceder a CrewAI Examples](https://github.com/crewAIInc/crewAI-examples)

## Ejecucion del framework
Para poder ejecutar este framework hace falta un metodo especial: ".kickoff()". Este metodo se encuentra en "crew()" el cual esta dentro de "CrewBase", la clase padre principal de todo el framework.\
Como digo se puede hacer todo desde el main, desde definir un CrewBase hasta seleccionar el modelo LLM (modelo de IA) que queremos usar para nuestro agente, pero claro, esto no es la mejor manera, para hacer eso hay un orden bastante importante, en la que el framework se entiende y funciona mucho mejor. Asi que para eso hice este archivo, expresar un orden claro para los elementos del framework y asi que el codigo pueda ser escalable (agregar mas agentes, funcionamientos), limpio, ordenado y mejor entendido.

# Orden recomendado (y usado en los ejemplos oficiales)
## Archivo main.py
Comencemos con el archivo principal: 'main.py'.\
En este archivo se encuentra el metodo main por excelencia de python para detectar que desde aqui se comenzara el flujo de ejecucion:
```
if __name__ == "__main__":
  ..codigo..
```
Dentro del metodo main estara todo el codigo requerido para la ejecucion.\
Como primer codigo, y dependiendo del uso que le des a tu crew, estaria bueno implementar dos condicionales donde te asegures que haya una carpeta para los archivos, y me refiero a cualquier tipo: mp3, imagenes, pdfs, etc.
Esto porque asi podes tener un unico lugar donde tener todo el contenido y asi no toparte con errores como `'FileNotFoundError'`:
```
if not os.path.exists("./workdir"):
  os.mkdir("./workidr")
```
Para esta condicional hace falta importar el modulo 'os'. No hace falta instalar nada, el modulo 'os' viene incluido con python, solo hace falta importarlo con `import os` al inicio del archivo.\
Tambien en algunos casos es necesario la presencia de plantillas (por ejemplo para una crew que se encarga de crear paginas web) entonces tambien es aqui donde se ejecuta algun metodo verificador que se encargue de responder si hay o no plantillas y dependiendo de su respuesta cierres o no la ejecucion.

Ahora si, saliendo de esas condicionales nos topamos con obviamente los llamados, ¿Llamados a que? te preguntaras, los llamados a los metodos que hayamos definido para la ejecucion de los agentes. A veces la ejecucion se hace desde aqui, se establecen variables de `agent_config` y `task_config` y como valor claro tienen un arreglo con todos los agentes y tareas. Si es uno se puede poner simplemente agents o agent, pero cuando son varios (tanto agentes como tareas) se usan esas variables que dije antes, ¿Porque? por defecto la clase padre 'CrewBase' hace que esas dos variables apunten por defecto a la ruta `config/agents.yml` y `config/tasks.yml`. Es decir que ya por definicion hay una ruta practica para definir los agentes y las tareas, ademas hacer las definiciones en un archivo YAML es mucho mas comodo y simplemente tenes que seguir correctamente la estructura del archivo.
Entonces en resumidas cuentas, el archivo 'main.py' es donde el flujo de ejecucion comienza, donde definimos todo lo que se hace antes o despues de llamar a una crew, donde llamamos a los metodos que ejecutan la crew o mismo podemos nosotros llamarlos desde alli.
## Archivo crew.py
En este archivo es donde se extiende a task, agent, crew y tambien con opcion a tools.\
Es decir que es en este archivo donde verdaderamente se crean a los agentes, tareas, a la crew y claro, si asi se necesitara, a las tools.\
Tipicamente se crea una clase base con un nombre X:
```
class nombreDeLaClase():
  ...codigo...
```
Es en esta clase donde se definen otras clases que extienden de 'CrewBase' y de esa forma completar esa plantilla para poder tener ordenado todo de mejor manera, ya que la clase 'CrewBase' es la clase padre de 'CrewAI', es en ella donde estan las definiciones de crew, agent, y task, ademas adicionalmente como dije se pueden definir las distintas tools, ya sean las integradas con 'crewai_tools' o importar otras, como tambien crear las nuestras propias.\
Las distintas crews, se separan en distintas clases que extiendan de 'CrewBase'.\
Algo comun que se suele hacer en los proyectos es separar grupos por funcion, es decir, una crew se encarga de reunir informacion, otra crew se encarga de resumirla, otra de presentarla y asi con todo. Recordemos que cuanto mas facil sea la tarea para la IA mas eficaz resultara.\
Como ejemplo pongo esto:
```
class MarketStrategy():        [clase principal de crew.py]
  ...codigo...
@CrewBase
class MarketingPostsCrew():    [clase encargada de reunir a agentes y tareas en crew]
  ...codigo...
```
En el ejemplo de arriba podemos observa como 'MarketStrategy()' realmente lo que va a hacer es ser una clase de control, que ira controlando el flujo de ejecucion y dando herramientas (tols) si asi se necesitara.\
Luego la otra clase, 'MarketingPostsCrew()' es mas un funcionamiento especifico que se necesita lograr, que obviamente dentro de el yaceran las definiciones con extension de agentes, tareas (tasks) y claro, la crew.\
## Definicion de agentes
Para poder definir a nuestros agentes hay varias formas, pero todas terminan siendo mas o menos la misma. Primero comencemos por el hecho de importar a "agent" la clase necesaria para poder hacer esto. Hay que importar 2:
* Agent: Perteneciente al modulo crewai. Este es el objeto "Agent" como tal.
* agent: Perteneciente al modulo crewai_project. Esta es la clase a la que extenderemos.
Como dije hay varias formas de definirlo pero la logica es la misma ya que el objeto "Agent" tiene ciertas propiedades.
1. Primera forma: En el archivo __crew.py__
  Naturalmente se definen dentro de una clase especifica. Como dije es comun ver que se separen tareas compuestas o complejas en distintas clases para tener una crew diferente con distintos agentes especializados y asi lograr un mejor resultado. Para definir a los agentes de esta forma utilizaremos los decoradores y definiremos sus propiedades:
  ```
  @agent
  def nombreDelAgente(self) -> Agent:
    return Agent(
      definir todas las propiedades del agente
    )
  ```
  De esta manera creamos el metodo que extendera a `@agent`.\
  Para ver una explicacion de las propiedades de los agentes ve a [Agentes.md y a 'Propiedades del Agente'](Agentes.md#propiedades-del-agente).\

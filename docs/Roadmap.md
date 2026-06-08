# Linea de aprendizaje para CrewAI
CrewAI permite el funcionamiento en conjunto de varios agentes de IA, y para lograr eso son necesarios algunos de los objetos que el framework utiliza.\
Para el funcionamiento minimo es necesario:
- __agent:__ Son los agentes de IA al que le damos un LLM (modelo), rol, objetivo y personalidad. Tienen varias propiedades tanto obligatorias como opcionales para que funcionen correctamente, les recomiendo pasar por "docs/Agentes".
- __task:__ Son las tareas que nosotros configuramos y podemos delegar a los distintos agentes que hayamos definido. Podemos tener tantas tareas como queramos y claro tiene varias propiedades que hay que saber para poder crear una tarea especifica y correcta. Para saber el funcionamiento de "task" recomiendo pasar por "docs/Tareas".
- __crew:__ Es el conjunto de los agentes y las tareas que hayamos definido. Es necesario para poder usar en una "CrewBase" y poder asi ejecutar todo en "main.py".
- __CrewBase:__ Es una base que no es mas que el conjunto de crews. Para un mayor orden se puede establecer varios "CrewBase" para asi tener separados de una mejor manera a los distintos agentes y tareas con nombres claves. Por ejemplo: Tener las "CrewBase" entenderPeticion, separarEnTemas, investigacion, conclusion, seria una buena forma de separar un proceso de investigacion profunda con indice.\

# Orden de archivos y carpetas
Estos son los objetos minimos necesarios, por poder se puede definir todos los objetos solo en el archivo main.py, pero ademas de que es una mala practica, no fomenta la escabilidad, codigo ordenado y si asi se quiera el profesionalismo de un buen codigo modular.\
La mejor forma sin lugar a dudas para ordenar tu codigo es en distintos archivos y carpetas, por eso sugiero este orden:\
Tu carpeta proyecto donde tengas todos los archivos y dentro de ella:\
carpeta "__config__" - Aqui guardaras los archivos de definicion de agentes (__agents.yml__) y tareas (__tasks.yml__)\
carpeta "__tools__" - __Si es que usas herramientas__ te conviene dejarlas definidas o importadas en una carpeta externa.\
archivo "__main.py__" - Archivo de ejecucion principal. Aqui estaran los metodos para iniciar a nuestra IA MAS (Multi-agent System)\
archivo "__crew.py__" - Archivo __MUY UTIL__. Es utilizado para formar el conjunto de agentes y tareas, definiendolos en una sola "tripulacion" cohesiva delegandoles tambien herramientas si las fueran a usar y una forma de trabajo (flujo de trabajo) (jerarquica o secuencial).

Para saber mas acerca de esto recomiendo ir a "docs/" alli encontraran el material para cada cosa (Agentes, Tareas, Tripulaciones (crew), CrewBase, Herramientas, etc)

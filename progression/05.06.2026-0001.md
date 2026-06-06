# INICIOS
Para comenzar utilizaremos un gestor de paquetes muy comodo: 'uv'.
Para eso debemos de instalarlo con 'curl -LsSf https://astral.sh/uv/install.sh | sh', de esta forma se instalara en nuestro sistema este gestor.
Con el comando "uv init" estaremos creando un nuevo projecto de Python desde la terminal. Cabe recalcar que el nombre del proyecto sera el mismo que el de la carpeta, en mi caso es 'project'.
Ahora importamos el paquete de CrewAI para comenzar con el comando "uv add 'crewai[tools]'. Me gustaria aclarar que segun fuentes lo recomendable al trabajar con CrewAI es con la version de Python 3.13.13 o menor.
Agregamos la libreria python-dotenv para poder almacenar las API keys sin que se suban realmente a GitHub y quitando todo el riesgo: "uv add python-dotenv"
Agregamos tambien la libreria de LiteLLM la cual es una libreria con varios LLM (agentes de IA) esto es necesario ya que cree un archivo para poder ejecutarlo y buscar agentes segun palabra clave: "uv add litellm"
El framework CrewAI funciona en base a 4 pilares fundamentales:
1. Agent: Es el "quién". Define la personalidad, el rol, el objetivo y el modelo (LLM) que usará un agente.
2. Task: Es el "qué". Define qué es lo que el agente debe hacer, qué resultados se esperan y qué agente es el responsable de ejecutarla.
3. Crew: Es el "equipo". Es la clase orquestadora que agrupa a los agentes y a las tareas, definiendo cómo deben colaborar (el proceso).
4. Tools: Son las habilidades como las funciones que permiten que los agentes interactuen con el mundo real (como buscar en Google o leer archivos). Esto es opcional pero es altamente recomendado y en mi caso lo usare.
Y claro que todo esto se iniciara gracias a una clase "main.py"
## ESTRUCTURA DE ARCHIVO
Estas son las estructuras a seguir para cada tipo de archivo.
### Archivo: agents.yml
Este archivo contendra las caracteristicas de un agente en especifico, su nombre, rol, objetivo, historia base para su personalidad, entre otras propiedades.
#### SINTAXIS DE ESTRUCTURA (al ser archivo YAML debe de respetarse la estructura y no inferir en la forma correcta de escribir en el archivo ya que el minimo error causaria que la ejecucion falle)
nombreDelAgente:
  role: "Que rol debera cumplir este agente"
  goal: "Que objetivo tiene este agente"
  backstory: >
   Aca puede ir un parrafo muy extenso como unas breves lineas, la idea es definir la personalidad de este agente mediante una "historia pasada", es decir, definiendo su personalidad en base a una historia. Por ejemplo decir "Eres un contador que ama su trabajo y no erra ningun calculo, inclusive realiza hasta el ultimo calculo aunque sea el mas tedioso"
   verbose: true/false (Tendra valor verdadero o falso y sirve para especificar si el agente ira diciendo que va haciendo, por ejemplo "Estructurando lotes", "Resumiendo idea X", etc)
   allow_delegation: true/false (Esta opcion permite establecer si el agente puede o no delegar la tarea a otro agente en casos especificos)
   memory: true/false (Permite o no al agente tener acceso al almacenar memoria y recordar conversaciones pasadas)
   max_iter: n (Tiene un valor numerico entero cualquiera (0-+). Especifica cuantas iteraciones hara por problema en caso de que sea uno especifico que obligue a iterar en la ejecucion)
   max_rpm: n (Tiene un valor numerico entero cualquiera (0-+). Sirve para poder establecer un limite en las consultas por minuto a la API del agente. Esto se vuelve util cuando utilizamos cuotas finitas y necesitamos ahorrar)
   allow_code_execution: true/false (Permite o no al agente ejecutar codigo python)
   tools: (Lista de herramientas (Tools) que el agente puede utilizar)
     - tool1
     - tool2
     - tool...

### Archivo: tasks.yml
Este archivo tendra las tareas a realizar por los agentes. Cada tarea estara directamente relacionada a un agente en especifico.
#### SINTAXIS DE ESTRUCTURA (al ser archivo YAML debe de respetarse la estructura y no inferir en la forma correcta de escribir en el archivo ya que el minimo error causaria que la ejecucion falle)
nombreDeLaTarea:
  description: >
   Aca se explica con lujo de detalle que debe de hacer el agente. Recomendacion: utiliza verbos de accion y se preciso y especifico.
  expected_output: >
   Aca se especifica que retorno quieres conseguir, es decir cual y como sera la salida final (mensaje respuesta final) del agente. Ejemplos: informe, archivo MarkDown, diagrama de grafos, etc.
  agent: nombreDelAgente (Donde el nombreDelAgente debe claramente coincidir con el nombre que le diste en el archivo 'agents.yml')
  async_execution: true/false (Permite lanzar/ejecutar esta tarea en segundo plano. Es decir que puede ejecutarse y quedarse en segundo plano para que otras tareas tambien puedan ejecutarse)
  context: (Permite utilizar tareas anteriores como contexto en esta)
    - nombreDeLaTarea (Claramente los nombres deben de coincidir como los definiste anteriormente)
    - nombreDeLaTarea
    - .....
  human_input: true/false (Esta opcion permite modificar si el agente pide o no confirmacion humana antes de proseguir, es decir recibir feedback de su trabajo y en base a este seguir o finalizar su tarea)
  tools: (Herramientas especificas para esta tarea)
    - nombre_herramienta
    - nombre_herramienta
    - .....


## Practica(5 ejercicios)
1. Crear un proyecto con uv y ejecutar `main.py`.
> -->
> Ejecuta `uv run python -c "import main"`. Explica por qué no consulta el catálogo.
Identifica también la condición que impide iniciar el transporte al importar
`server.py`. Distingue registrar una herramienta de iniciar el servidor.
>  >no funciona porque falta especificar qué función, módulo o bloque de código dentro de main.py deseas ejecutar con el argumento -c (code).  <br><br>
><br>Para que funcione  es mejor usar [uv run python main.py]  o  [uv run python -c "import main; main.main()"]
> y añadir sus respectivos argumentos<br><br>
>  >usage: -c [-h] [--catalog CATALOG] [--log-level {DEBUG,INFO,WARNING,ERROR}] [--log-file LOG_FILE] query
-c: error: the following arguments are required: query

2. Consultar un archivo JSON desde un módulo reutilizable.
>-->
> Para realizar dicha consulta es necesario agregar los argumentos a la llamada 
> uv run python -c "import main; main.main()" --catalog data/courses.json python
> >[Llamada]<br>
> >(course-catalog) PS E:\1 CLASES MAESTRIA\Maestria en IA\1er semestre\Apuntes\github\Programacion para IA\remoto_tarea\notebooks\Unidad 1\sesion-04-reproducibilidad\ivan\course-catalog> uv run python -c "import main; main.main()" --catalog data/courses.json python                                                                                                                                                                                               
INFO | catalog.search | Search completed: 2 matches
> > `[
> >  {
> >    "code": "PY01",
> >    "title": "Python foundations",
> >    "hours": 12
> >  },
> >  {
> >    "code": "PY02",
> >    "title": "Python for data analysis",
> >    "hours": 16
> >  }
> >]`

3. Incorporar argumentos y logging.
> Compara una misma consulta con DEBUG, INFO y ERROR. Después usa
`--log-level ERROR --log-file logs/app.log`. Explica por qué el archivo conserva
mensajes que no aparecen en consola y por qué stdout debe reservarse al protocolo
en `server.py` <br> 
> >Tras incorporar elementos para login la consulta sera posible con los siguientes argumentos
> > uv run python main.py python --log-level DEBUG
> > uv run python main.py python --log-level ERROR --log-file logs/app.log
> > uv run python main.py python --catalog data/missing.json
> <br>Ejemplo:<br>
> [salida: DEBUG]<br>
> >DEBUG | catalog.search | Reading catalog: E:\1 CLASES MAESTRIA\Maestria en IA\1er semestre\Apuntes\github\Programacion para IA\remoto_tarea\notebooks\Unidad 1\sesion-04-reproducibilidad\ivan\course-catalog\data\courses.json
INFO | catalog.search | Search completed: 2 matches
> >`[
> >  {
> >    "code": "PY01",
> >    "title": "Python foundations",
> >    "hours": 12
> >  },
> >  {
> >   "code": "PY02",
> >    "title": "Python for data analysis",
> >    "hours": 16
> >  }
> >]`
> <br><br>
> [salida: ERROR]<br>
> uv run python main.py python --log-level ERROR --log-file logs/app.log <br>
> >DEBUG | catalog.search | Reading catalog: E:\1 CLASES MAESTRIA\Maestria en IA\1er semestre\Apuntes\github\Programacion para IA\programacion_ai\unidad-01-python-moderno\sesion-04-reproducibilidad\ivan\course-catalog\data\courses.json
> >INFO | catalog.search | Search completed: 2 matches
> >DEBUG | catalog.search | Reading catalog: E:\1 CLASES MAESTRIA\Maestria en IA\1er semestre\Apuntes\github\Programacion para IA\programacion_ai\unidad-01-python-moderno\sesion-04-reproducibilidad\ivan\course-catalog\data\courses.json
> >INFO | catalog.search | Search completed: 2 matches

4. Exponer la consulta mediante un servidor MCP local.
> > una vez todo configurado
> se puede exponer una tool al levantar el servidor mediante el codigo que ya se implemento
> <br><br>
> > `mcp = MCPServer("Course catalog")
> > logger = logging.getLogger(__name__)
> > @mcp.tool()
> >def find_courses(query: str) -> list[Course]:
> >    """Find courses by words in their title, ignoring letter case."""
> >    try:
> >         return search_courses(query)
> >
> >    except (OSError, ValueError) as error:
> >        logger.exception("Catalog search failed")
> >         raise ToolError(
> >            "Cannot search the catalog. Check the query and server logs."
> >     ) from error`


5. Conectar un cliente y reproducir el proyecto desde una copia limpia.

> >\notebooks\Unidad 1\sesion-04-reproducibilidad\ivan\course-catalog> uv run --locked python client.py python 
> <br><br>
> >`Tools: ['find_courses']
> >{
> >  "meta": {
> >    "io.modelcontextprotocol/serverInfo": {
> >      "name": "Course catalog",
> >      "version": ""
> >    }
> >  },
> >  "content": [
> >    {
> >      "type": "text",
> >      "text": "{\n  \"code\": \"PY01\",\n  \"title\": \"Python foundations\",\n  \"hours\": 12\n}",
> >      "annotations": null,
> >      "meta": null
> >    },
> >    {
> >      "type": "text",
> >      "text": "{\n  \"code\": \"PY02\",\n  \"title\": \"Python for data analysis\",\n  \"hours\": 16\n}",
> >      "annotations": null,
> >     "meta": null
> >    }
> >  ],
> >  "structured_content": {
> >    "result": [
> >      {
> >        "code": "PY01",
> >        "title": "Python foundations",
> >        "hours": 12
> >      },
> >      {
> >        "code": "PY02",
> >        "title": "Python for data analysis",
> >        "hours": 16
> >      }
> >    ]
> >  },
> >  "is_error": false,
> >  "result_type": "complete"
> >}` 

## Ejecucion en copia limpia resultados: 

- Lee el archivo uv.lock y recrea el entorno virtual (.venv) instalando exactamente las dependencias y 
versiones bloqueadas especificadas, garantizando un entorno idéntico y reproducible.

- Resultados al ejecutar client.py con diferentes entradas con la consulta "python":
  - El cliente se comunica con el servidor MCP, ejecuta la herramienta de búsqueda 
  y devuelve la lista con los cursos coincidentes.
  
### ¿Qué aportan los archivos de configuración y por qué no se entrega el .venv?
- pyproject.toml: Define los metadatos del proyecto, las dependencias principales requeridas y la configuración 
de las herramientas de desarrollo.

- **uv.lock**:<br> Fija las versiones exactas y las dependencias transitivas de todo el proyecto, asegurando que cualquier 
instalación posterior sea 100% determinista.

- **.python-version**:<br> Especifica la versión exacta de Python que requiere el proyecto para funcionar correctamente.

- **Por qué no se entrega .venv**:<br> El directorio .venv contiene ejecutables específicos del sistema operativo local
y rutas absolutas que no son portables. Como uv sync reconstruye este entorno de forma automática y rápida a  
partir de uv.lock, incluir el .venv en el repositorio es innecesario y redundante.


## Calidad:
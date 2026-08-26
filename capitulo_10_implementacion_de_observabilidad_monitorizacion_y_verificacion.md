# Parte 3: Aplicación de Clean Architecture en Python

## Capítulo 10: Implementación de observabilidad: Monitorización y verificación

En capítulos anteriores, establecimos los principios fundamentales de Clean Architecture a través de nuestro sistema de gestión de tareas. Construimos entidades de dominio, implementamos casos de uso y creamos tanto interfaces de línea de comandos (CLI) como interfaces web que demuestran cómo los límites de Clean Architecture permiten una clara separación entre nuestra lógica de negocio central y las preocupaciones externas. Si bien estos límites hacen que nuestro sistema sea más mantenible, cumplen otro propósito crucial: hacen que nuestro sistema sea más observable y que su integridad arquitectónica sea más verificable.

A través de nuestro sistema de gestión de tareas, demostraremos cómo Clean Architecture transforma la observabilidad del sistema de una preocupación transversal (*cross-cutting concern*) en una capacidad estructurada. Debido a que nuestro sistema está construido con capas arquitectónicas claras e interfaces explícitas, la monitorización se convierte en una extensión natural de nuestra estructura existente. Esta misma organización que simplifica la monitorización también permite la verificación continua, ayudando a garantizar que nuestro sistema mantenga su integridad arquitectónica a medida que evoluciona.

Al final de este capítulo, comprenderás cómo implementar una observabilidad efectiva en sistemas basados en Clean Architecture y cómo verificar que los límites arquitectónicos permanezcan intactos a lo largo del tiempo. Aprenderás técnicas prácticas para detectar y prevenir la desviación arquitectónica (*architectural drift*), ayudando a garantizar que tus sistemas mantengan su estructura limpia incluso cuando los requisitos y los equipos evolucionen.

En este capítulo, vamos a cubrir los siguientes temas principales:

- **Comprensión de la observabilidad en Clean Architecture**
- **Implementación de instrumentación a través de los límites**
- **Mantenimiento de la integridad arquitectónica mediante monitorización**

---

### Sección 10.1: Requisitos técnicos

Los ejemplos de código presentados en este capítulo y a lo largo del resto del libro se han probado con Python 3.13. Por razones de brevedad, la mayoría de los ejemplos de código en el capítulo están implementados solo de forma parcial. Las versiones completas de todos los ejemplos se pueden encontrar en el repositorio de GitHub que acompaña al libro en [https://github.com/PacktPublishing/Clean-Architecture-with-Python](https://github.com/PacktPublishing/Clean-Architecture-with-Python).

---

### Sección 10.2: Comprensión de los límites de observabilidad en Clean Architecture

Los perímetros explícitos de las capas de Clean Architecture proporcionan puntos naturales para la observación del sistema, lo cual constituye una ventaja significativa que muchos equipos pasan por alto. Si bien las arquitecturas en capas pueden introducir cierta complejidad, estas mismas divisiones que ayudan a gestionar las dependencias también permiten una monitorización y observabilidad sistemáticas. Primero exploremos cómo los principios fundamentales de Clean Architecture crean oportunidades para una mejor instrumentación del sistema, sentando las bases para las implementaciones prácticas que exploraremos más adelante. Al comprender estos conceptos, verás cómo Clean Architecture hace que los sistemas no solo sean más mantenibles, sino también más observables.

#### Puntos naturales de observación en Clean Architecture

La estructura en capas de Clean Architecture crea de forma natural puntos estratégicos para la observación del sistema. Antes de explorar estos puntos de observación, comprendamos qué entendemos por observabilidad en sistemas de software. La observabilidad moderna combina el registro de logs (*logging*), métricas (*metrics*) y el rastreo de solicitudes (*request tracing*) para proporcionar una imagen completa del comportamiento del sistema. En los sistemas tradicionales donde estas preocupaciones atraviesan todos los componentes de forma transversal, implementar una monitorización integral a menudo se convierte en un ejercicio de sortear dependencias enredadas.

Clean Architecture transforma esta complejidad en claridad al proporcionar puntos de observación consistentes en cada transición de capa. Considera cómo fluye la información a través de nuestro sistema de gestión de tareas: cuando un usuario crea una tarea a través de la interfaz web, podemos observar la solicitud a medida que se mueve a través de nuestras capas arquitectónicas, desde el manejo inicial de HTTP, pasando por las operaciones de negocio, hasta la persistencia final. Cada límite de capa proporciona una perspectiva específica:

- **Nuestra interfaz web** rastrea las solicitudes entrantes y sus transformaciones.
- **Los casos de uso** monitorizan las operaciones de negocio y sus resultados.
- **Las entidades de dominio** capturan los cambios de estado y la aplicación de reglas de negocio.
- **Los componentes de infraestructura** miden la utilización de recursos y las interacciones externas.

Este enfoque sistemático garantiza que tengamos visibilidad de cada aspecto crucial del comportamiento de nuestro sistema, manteniendo al mismo tiempo una separación limpia entre las preocupaciones técnicas y las de negocio. Esto transforma no solo la monitorización, sino todo nuestro enfoque hacia el mantenimiento del sistema. Al investigar incidencias o analizar el rendimiento, sabemos exactamente dónde buscar la información relevante. Como veremos en las siguientes secciones, este mismo enfoque estructurado que permite la monitorización también proporciona la base para verificar nuestra integridad arquitectónica.

#### Comprensión de la observabilidad en Clean Architecture

Habiendo visto cómo Clean Architecture proporciona puntos naturales de observación, exploremos cómo aprovechar eficazmente estos puntos en la práctica. Mientras que los capítulos anteriores se centraron en establecer principios arquitectónicos fundamentales, los sistemas del mundo real requieren observabilidad desde el principio. La instrumentación temprana resulta crucial. Sin ella, la depuración se vuelve más desafiante, los problemas de rendimiento pasan desapercibidos y comprender el comportamiento del sistema en diferentes entornos se vuelve casi imposible.

Considera cómo se desarrolla esto en nuestro sistema de gestión de tareas. La Figura 10.1 muestra cómo una operación aparentemente simple como la finalización de una tarea involucra múltiples transiciones arquitectónicas, cada una de las cuales proporciona distintas necesidades de observabilidad:

> **Figura 10.1:** Flujo de finalización de tareas con puntos de observación.

La figura ilustra cómo las preocupaciones de monitorización se alinean de forma natural con nuestras capas arquitectónicas. En cada transición, capturamos aspectos específicos del comportamiento del sistema, desde métricas técnicas en nuestros límites exteriores hasta operaciones de negocio en nuestras capas centrales. Este enfoque sistemático asegura que mantengamos una visibilidad integral respetando al mismo tiempo la separación de responsabilidades de Clean Architecture.

Un enfoque de monitorización por capas proporciona beneficios claros. Al investigar incidencias, podemos rastrear operaciones a través de nuestro sistema con precisión. Si un cliente informa sobre fallos intermitentes al completar tareas, podemos seguir la operación desde la solicitud web a través de la lógica de negocio para identificar exactamente dónde salieron mal las cosas. Los cuellos de botella de rendimiento se vuelven más fáciles de localizar dado que sabemos qué capa está manejando cada aspecto de la operación.

Cada capa contribuye con lo que mejor conoce. Las interfaces web rastrean el manejo de solicitudes, los casos de uso monitorizan las operaciones de negocio y la infraestructura captura métricas técnicas. Al respetar estas divisiones naturales, mantenemos una separación limpia entre las preocupaciones técnicas y las de negocio, asegurando al mismo tiempo una visibilidad completa del comportamiento de nuestro sistema.

Estos principios de monitorización se traducen directamente en patrones de implementación. En nuestro sistema de gestión de tareas, utilizaremos el framework estándar de logging de Python para implementar esta observabilidad por capas. Veremos cómo los límites de Clean Architecture nos guían hacia soluciones de monitorización simples pero efectivas que mantienen la integridad arquitectónica mientras proporcionan la información que nuestro sistema necesita.

---

### Sección 10.3: Implementación de instrumentación a través de los límites

Traduzcamos nuestra comprensión de los beneficios de observabilidad de Clean Architecture en una implementación práctica. Los frameworks web modernos como Flask proporcionan su propia infraestructura de registro de logs (*logging*), lo que puede tentar a los desarrolladores a acoplar estrechamente las operaciones de negocio con el logging específico del framework. Veremos cómo trabajar eficazmente con estos mecanismos del framework manteniendo nuestra lógica de negocio central independiente de él. A través de una implementación cuidadosa de logging estructurado y rastreo de solicitudes (*request tracing*), demostraremos patrones que mantienen los límites de Clean Architecture mientras ofrecen una observabilidad integral del sistema.

#### Evitar el acoplamiento al framework en el registro de logs

Como mencionamos, los frameworks web a menudo proporcionan su propia infraestructura de logging. Flask, por ejemplo, fomenta el uso directo de su logger de aplicación (`app.logger`):

```python
@app.route('/tasks/new', methods=['POST'])
def create_task():
    task = create_task_from_request(request.form)
    app.logger.info('Created task %s', task.id)  # Framework-specific logging
    return redirect(url_for('index'))
```

Aunque conveniente, este enfoque crea un acoplamiento problemático entre nuestras operaciones de negocio y el logging específico del framework. El uso de `app.logger` de Flask requeriría hacer que el objeto de aplicación de Flask fuera accesible en toda nuestra base de código, lo cual es una grave violación de la Regla de Dependencia de Clean Architecture. Las capas internas necesitarían extenderse hacia la capa de Frameworks solo para registrar logs, creando exactamente el tipo de dependencia hacia el exterior que Clean Architecture pretende evitar.

En su lugar, Clean Architecture nos guía hacia un registro de logs independiente del framework que respeta los límites arquitectónicos. Considera cómo nuestro caso de uso de creación de tareas debería registrar las operaciones:

```python
# todo_app/application/use_cases/task_use_cases.py
import logging

logger = logging.getLogger(__name__)

@dataclass
class CreateTaskUseCase:
    task_repository: TaskRepository
    project_repository: ProjectRepository

    def execute(self, request: CreateTaskRequest) -> Result:
        try:
            logger.info(
                "Creating new task",
                extra={"context": {
                  "title": request.title, "project_id": request.project_id
                }},
            )
            # ... implementation continues ...
```

Este enfoque ofrece varios beneficios de Clean Architecture:

- Los casos de uso permanecen ajenos a los detalles de implementación del logging.
- Las sentencias de registro documentan las operaciones de negocio de forma natural.
- Podemos cambiar la infraestructura de logging sin modificar la lógica de negocio.
- El logging específico del framework permanece en los bordes del sistema, donde pertenece.

Implementemos este enfoque de logging limpio sistemáticamente, comenzando con la separación adecuada entre las preocupaciones de logging del framework y de la aplicación.

#### Implementación de patrones de logging estructurado

Como hemos visto, Clean Architecture requiere que las preocupaciones de infraestructura, incluidos los detalles de implementación del logging, permanezcan aisladas en las capas exteriores.

Para nuestra implementación, hemos elegido el logging estructurado en JSON. Esta es una práctica común que permite un procesamiento y análisis preciso de los logs. Cada entrada de log se convierte en un objeto JSON con campos consistentes, facilitando la búsqueda, filtrado y análisis programático de los datos de registro. Si bien demostraremos el formateo en JSON, los patrones que establecemos funcionarían igualmente bien con otros formatos de registro: podrías adaptar la implementación del formateador sin tocar ninguna línea de código en las capas internas.

Organizamos nuestra infraestructura de logging para mantener límites arquitectónicos limpios:

> **Figura 10.2:** Archivos de registro (*logging*) en la capa de Frameworks y Drivers.

Esta organización mantiene la configuración del logging donde corresponde: en la capa de Frameworks y Drivers. La separación entre los logs del framework (`access.log`) y los logs de la aplicación (`app.log`) demuestra cómo mantenemos límites claros incluso en la salida de nuestros registros.

Esta separación cumple dos objetivos clave de Clean Architecture:

- **Separación de responsabilidades**: Cada capa registra lo que mejor conoce. Flask maneja el registro de solicitudes HTTP en su formato estándar, mientras que nuestra aplicación captura las operaciones de negocio en JSON estructurado. Esta clara separación significa que cada tipo de log puede evolucionar de manera independiente, utilizando formatos y campos apropiados para su propósito.
- **Independencia del framework**: El registro de logs central de nuestra aplicación permanece completamente ajeno a Flask o a cualquier otro framework web. Podríamos cambiar a un framework diferente, o incluso agregar nuevas interfaces como una API REST, mientras que el registro de nuestras operaciones de negocio continúa sin cambios.

Necesitamos una forma de formatear los logs de nuestra aplicación que admita datos estructurados manteniéndose independiente de cualquier opinión del framework. Nuestro `JsonFormatter` asume esta responsabilidad:

```python
# todo_app/infrastructure/logging/config.py
class JsonFormatter(logging.Formatter):
    """Formats log records as JSON."""

    def __init__(self, app_context: str):
        super().__init__()
        self.app_context = app_context
        # Custom encoder handles datetime, UUID, sets, and exceptions
        self.encoder = JsonLogEncoder()

    def format(self, record: logging.LogRecord) -> str:
        """Format log record as JSON."""
        log_data = {
            "timestamp": datetime.now(timezone.utc),
            "level": record.levelname,
            "logger": record.name,
            "message": record.getMessage(),
            "app_context": self.app_context,
        }

        # `extra` in the log statement, places `context`
        # on the LogRecord so seek and extract
        context = {}
        for key, value in record.__dict__.items():
            if key == "context":
                context = value
                break

        if context:
            log_data["context"] = context

        return self.encoder.encode(log_data)
```

El formateador encapsula toda la lógica de formateo JSON en un único componente, demostrando el Principio de Responsabilidad Única (*Single Responsibility Principle*) en acción. Cada entrada de log incluye contexto esencial como la marca de tiempo (*timestamp*) y el nivel de log, manteniéndose completamente ajena a los frameworks web u otras preocupaciones externas.

Dado que el mecanismo de logging de Python adjunta directamente las claves del parámetro `extra` a la instancia de `LogRecord`, utilizamos un espacio de nombres (*namespace*) dedicado `context` para evitar colisiones con los atributos integrados de `LogRecord` (como `name`, `args`). Esta simple estrategia de nombres nos permite incluir datos estructurados de forma segura con cada mensaje de registro.

Con nuestro formateador manejando la estructura de los mensajes de log individuales, ahora necesitamos configurar cómo fluyen estos mensajes a través de nuestro sistema. Esta configuración determina qué logs van a dónde, manteniendo nuestra limpia separación entre el logging del framework y el de la aplicación. Para mayor claridad, utilizaremos `dictConfig` del módulo de logging de Python para establecer estas rutas, comenzando con nuestros formateadores:

```python
# todo_app/infrastructure/logging/config.py
def configure_logging(app_context: Literal["CLI", "WEB"]) -> None:
    """Configure application logging with sensible defaults."""
    log_dir = Path("logs")
    log_dir.mkdir(exist_ok=True)

    config = {
        "formatters": {
            "json": {"()": JsonFormatter, "app_context": app_context},
            "standard": {"format": "%(message)s"},
        },
        # ...
```

Aquí definimos dos formateadores: nuestro formateador JSON personalizado para los logs de la aplicación y un formato simple para los logs del framework. Esta separación permite que cada tipo de log mantenga su estructura apropiada.

A continuación, configuramos los manejadores (*handlers*) que dirigen los logs a sus destinos apropiados:

```python
        # ...
        "handlers": {
            "app_file": {
                "class": "logging.FileHandler",
                "filename": log_dir / "app.log",
                "formatter": "json",
            },
            "access_file": {
                "class": "logging.FileHandler",
                "filename": log_dir / "access.log",
                "formatter": "standard",
            },
        },
        # ...
```

Cada manejador conecta un destino de registro con su formateador correspondiente, manteniendo nuestra clara separación entre las preocupaciones del framework y de la aplicación.

Finalmente, conectamos todo mediante las configuraciones de los loggers:

```python
        # ...
        "loggers": {
            # Application logger
            "todo_app": {
                "handlers": ["app_file"],
                "level": "INFO",
            },
            # Flask's werkzeug logger
            "werkzeug": {
                "handlers": ["access_file"],
                "level": "INFO",
                "propagate": False,
            },
        },
    }
```

El logger `todo_app` captura todas las operaciones a nivel de aplicación a través de nuestro formateador JSON, escribiéndolas en `app.log`. Mientras tanto, el logger Werkzeug integrado de Flask permanece intacto, registrando solicitudes HTTP en formato estándar en `access.log`. Al mantener estos flujos de registro separados, conservamos límites limpios entre el framework y las preocupaciones de negocio.

Esta configuración se activa tempranamente en el inicio de nuestra aplicación:

```python
# web_main.py
def main():
    """Configure logging early"""
    configure_logging(app_context="WEB")

    # ...
```

Aquí vemos el archivo principal para la aplicación web; la CLI será idéntica excepto por `app_context="CLI"`.

Lo más importante es que esta configuración significa que cualquier código en nuestra aplicación puede simplemente usar el módulo estándar `logging` de Python sin conocer nada sobre el formateo JSON, los manejadores de archivos o cualquier otro detalle de implementación. Estas preocupaciones permanecen adecuadamente contenidas dentro de nuestra capa de Infraestructura.

Con nuestra infraestructura de logging en su lugar, veamos cómo la separación de responsabilidades de Clean Architecture se traduce en un beneficio práctico. Nuestro caso de uso de creación de tareas demuestra cómo las operaciones de negocio se pueden registrar con claridad sin ninguna conciencia de las particularidades del framework:

```python
import logging

logger = logging.getLogger(__name__)


@dataclass
class CreateTaskUseCase:
    task_repository: TaskRepository
    project_repository: ProjectRepository

    def execute(self, request: CreateTaskRequest) -> Result:
        try:
            logger.info(
                "Creating new task",
                extra={"title": request.title, "project_id": request.project_id},
            )
            
            # ... task creation logic ...
            
            logger.info(
                "Task created successfully",
                extra={"context": {
                    "task_id": str(task.id),
                    "project_id": str(project_id),
                    "priority": task.priority.name,
                }},
            )
            # ... remainder of method
```

Cuando ejecutamos nuestra aplicación, vemos esto en la consola:

Mientras que hemos optado por mostrar ambos flujos de log en la consola por comodidad durante el desarrollo, cada tipo de registro se separa adecuadamente en su archivo correspondiente.

Si observamos la sentencia de registro formateada de creación de una nueva tarea, vemos la inyección del atributo `context` en la declaración del log:

```json
{
  "timestamp": "2025-02-22T20:10:03.800373+00:00",
  "level": "INFO",
  "logger": "todo_app.application.use_cases.task_use_cases",
  "message": "Creating new task",
  "app_context": "WEB",
  "trace_id": "19d386aa-5537-45ac-9da6-3a0ce8717660",
  "context": {
    "title": "New Task",
    "project_id": "e587f1d5-5f6e-4da5-8d6b-155b39bbe8a9"
  }
}
```

A través de esta implementación, hemos visto cómo Clean Architecture nos guía hacia soluciones pragmáticas para preocupaciones comunes de infraestructura. Al aislar la configuración del logging en nuestra capa más externa, permitimos que cada parte de nuestro sistema registre información de manera apropiada manteniendo los límites arquitectónicos adecuados. Los logs del framework y las operaciones de negocio permanecen limpiamente separados, pero ambos contribuyen a una visión integral del comportamiento del sistema.

#### Construcción de observabilidad a través de los límites

A lo largo de este libro, hemos visto cómo los límites explícitos de Clean Architecture proporcionan beneficios cruciales, desde aislar la lógica de negocio y mantener la capacidad de prueba hasta permitir la flexibilidad de interfaces y la independencia de frameworks. Sin embargo, estos mismos límites que mantienen mantenible nuestro sistema pueden hacer que resulte desafiante rastrear las operaciones a medida que fluyen a través de nuestras capas.

Si bien el logging estructurado proporciona visibilidad sobre operaciones individuales, el seguimiento de solicitudes a través de estos límites arquitectónicos requiere infraestructura adicional. Extendamos nuestro sistema de gestión de tareas para implementar el rastreo a través de límites (*cross-boundary tracing*) manteniendo estas separaciones limpias.

Considera lo que sucede cuando un usuario crea una tarea a través de nuestra interfaz web, una operación que cruza múltiples límites arquitectónicos:

1. Una solicitud web llega a nuestro manejador de rutas de Flask.
2. La solicitud fluye a través de nuestro controlador de tareas.
3. El controlador invoca nuestro caso de uso.
4. El caso de uso se coordina con los repositorios.
5. Finalmente, el resultado fluye de regreso a través de estas capas.

Sin correlación entre estos eventos, la depuración y la monitorización se vuelven desafiantes. Nuestra solución es directa pero potente: generaremos un identificador único (*trace ID*) para cada solicitud e incluiremos este ID en cada sentencia de log relacionada con dicha solicitud. Esto nos permite seguir el recorrido de una solicitud a través de todas las capas de nuestro sistema, desde la solicitud web inicial hasta las operaciones de base de datos y de vuelta.

Para implementar este rastreo, necesitaremos:

- Crear `infrastructure/logging/trace.py` para gestionar la generación y almacenamiento de los identificadores de traza (*trace IDs*).
- Extender nuestra configuración de logging en `infrastructure/logging/config.py` para incluir los *trace IDs* en los formatos de log.
- Agregar un middleware de Flask en `infrastructure/web/middleware.py` para establecer los *trace IDs* en las solicitudes entrantes.

Debido a que hemos construido nuestra infraestructura de logging siguiendo los principios de Clean Architecture, no se requieren cambios en el código de la aplicación. Los *trace IDs* fluirán automáticamente a través de nuestras llamadas de logging existentes.

Con nuestro enfoque planificado, comencemos con la base: la gestión del identificador de traza en sí. Esta infraestructura, aunque reside completamente en nuestra capa exterior, permitirá visibilidad a través de todos los límites arquitectónicos:

```python
# todo_app/infrastructure/logging/trace.py

# Thread-safe context variable to hold trace ID
from contextvars import ContextVar
from typing import Optional
from uuid import uuid4


trace_id_var: ContextVar[Optional[str]] = ContextVar("trace_id", default=None)


def get_trace_id() -> str:
    """Get current trace ID or generate new one if not set."""
    current = trace_id_var.get()
    if current is None:
        current = str(uuid4())
        trace_id_var.set(current)
    return current


def set_trace_id(trace_id: Optional[str] = None) -> str:
    """Set trace ID for current context."""
    new_id = trace_id or str(uuid4())
    trace_id_var.set(new_id)
    return new_id
```

La función `set_trace_id` establece un identificador único para cada solicitud en nuestro sistema. Aunque acepta un parámetro opcional de ID existente (utilizado principalmente para pruebas o integraciones especializadas), en el funcionamiento normal cada solicitud recibe un nuevo UUID. Esto garantiza que cada operación en nuestro sistema se pueda rastrear de forma independiente, independientemente de si se originó desde nuestra CLI, la interfaz web u otros puntos de entrada.

##### ¿Por qué ContextVar?

Utilizamos `ContextVar` de Python porque proporciona almacenamiento seguro para subprocesos (*thread-safe*) que funciona a través de límites asíncronos (*async boundaries*). Si bien el mecanismo de implementación específico no es crucial para Clean Architecture, elegir las herramientas adecuadas ayuda a mantener límites limpios. Para más detalles sobre las variables de contexto, consulta la documentación de Python: [https://docs.python.org/3/library/contextvars.html](https://docs.python.org/3/library/contextvars.html).

Con la gestión del *trace ID* en su lugar, a continuación debemos asegurarnos de que nuestra configuración de logging incluya el *trace ID* en los formatos de registro:

```python
# todo_app/infrastructure/logging/config.py
def configure_logging(app_context: Literal["CLI", "WEB"]) -> None:
    config = {
        "formatters": {
            "json": {"()": JsonFormatter, "app_context": app_context},
            "standard": {
                "format": "%(asctime)s [%(trace_id)s] %(message)s",
                "datefmt": "%Y-%m-%d %H:%M:%S",
            },
        },
        # ... rest of configuration
    }
```

Nuestra configuración de logging garantiza que los *trace IDs* se incluyan con cada mensaje de log, independientemente del formato de registro. Para los logs del framework, agregamos *trace IDs* al formato estándar usando la sintaxis de patrones de logging integrada de Python (`%(trace_id)s`). Nuestro formateador JSON incluye automáticamente los *trace IDs* en la salida estructurada. Esta coherencia significa que podemos seguir las operaciones a través de todas las fuentes de registro, mientras que cada flujo de log mantiene su formato adecuado.

Por último, nuestro middleware web garantiza que cada solicitud reciba un *trace ID*:

```python
# todo_app/infrastructure/web/middleware.py
def trace_requests(flask_app):
    """Add trace ID to all requests."""

    @flask_app.before_request
    def before_request():
        trace_id = request.headers.get("X-Trace-ID") or None
        # pull trace id from globals
        g.trace_id = set_trace_id(trace_id)

    @flask_app.after_request
    def after_request(response):
        response.headers["X-Trace-ID"] = g.trace_id
        return response
```

Este middleware garantiza que cada solicitud web reciba un *trace ID* único. Aunque puede aceptar un ID existente a través del encabezado `X-Trace-ID` (útil para pruebas), normalmente genera un nuevo UUID para cada solicitud.

Para activar este rastreo, integramos el middleware al crear nuestra aplicación Flask:

```python
# todo_app/infrastructure/web/app.py
def create_web_app(app_container: Application) -> Flask:
    """Create and configure Flask application."""
    flask_app = Flask(__name__)
    flask_app.config["SECRET_KEY"] = "dev"  # Change this in production
    flask_app.config["APP_CONTAINER"] = app_container

    # Add trace ID middleware
    trace_requests(flask_app)

    # ...
```

Recordemos que `web_main.py` llama a `create_web_app`, y por tanto esta configuración garantiza que cada solicitud que fluya a través de nuestro sistema sea rastreada. Este identificador queda disponible a lo largo del procesamiento de la solicitud y se incluye en los encabezados de respuesta con fines de depuración. El *trace ID* conecta todas las entradas de log relacionadas con el procesamiento de esa solicitud específica, desde la recepción inicial hasta la respuesta final.

A cada solicitud que pasa por nuestro sistema se le asigna un *trace ID* único, lo que nos permite seguir esa operación específica a través de los límites arquitectónicos. Como se muestra arriba, el *trace ID* `abc-123-xyz` aparece tanto en los logs del framework como en los de la aplicación, conectando todos los eventos relacionados con esta solicitud individual de creación de tareas. Este rastreo nos permite comprender exactamente lo que sucedió durante cualquier solicitud determinada, desde el manejo HTTP inicial, pasando por las operaciones de negocio, hasta la respuesta final.

Nuestra implementación de logging y rastreo demuestra cómo los límites de Clean Architecture permiten una observabilidad integral del sistema sin comprometer los principios arquitectónicos. Sin embargo, implementar estos patrones es solo la mitad del desafío; también debemos asegurarnos de que estos límites permanezcan intactos a medida que nuestro sistema evoluciona. A continuación, exploraremos cómo verificar activamente nuestra integridad arquitectónica a través de verificaciones automáticas y funciones de idoneidad (*fitness functions*).

---

### Sección 10.4: Verificación de la integridad arquitectónica mediante funciones de idoneidad (fitness functions)

A medida que los sistemas evolucionan, mantener la integridad arquitectónica se vuelve cada vez más desafiante. Incluso los equipos comprometidos con los principios de Clean Architecture pueden introducir inadvertidamente cambios que comprometan los límites cuidadosamente elaborados de sus sistemas. Este riesgo ha llevado a los arquitectos a desarrollar funciones de idoneidad (*fitness functions*), que son pruebas automatizadas que verifican que los principios arquitectónicos estén correctamente implementados y detectan cualquier desviación de esos principios a lo largo del tiempo.

El concepto de funciones de idoneidad arquitectónicas, introducido por Neal Ford, Rebecca Parsons y Patrick Kua en su libro *Building Evolutionary Architectures*, proporciona un enfoque sistemático para mantener la integridad arquitectónica. Así como las pruebas unitarias verifican el comportamiento del código, las funciones de idoneidad verifican las características arquitectónicas. Al detectar violaciones en una etapa temprana del proceso de desarrollo (un enfoque conocido como *shift left* o desplazamiento a la izquierda), estas pruebas ayudan a los equipos a mantener los principios de Clean Architecture de manera automatizada.

Si bien existen marcos de validación arquitectónica integrales, Python nos permite implementar una verificación efectiva de una manera más simple y pragmática utilizando las capacidades integradas del lenguaje. A través de nuestro enfoque de verificación arquitectónica, nos centraremos en dos aspectos clave: garantizar que nuestra estructura de código fuente mantenga la organización por capas de Clean Architecture y detectar cualquier violación de la fundamental Regla de Dependencia, que exige que las dependencias solo apunten hacia adentro. Estas verificaciones complementarias ayudan a los equipos a mantener la integridad arquitectónica a medida que los sistemas evolucionan.

#### Verificación de la estructura de capas

Comencemos definiendo nuestra estructura arquitectónica esperada. Si bien la implementación específica de Clean Architecture de cada equipo puede variar ligeramente, el principio central de organización explícita de capas permanece constante. Podemos plasmar nuestra interpretación particular en una configuración simple:

```python
class ArchitectureConfig:
    """Defines Clean Architecture structure and rules."""

    # Ordered from innermost to outermost layer
    LAYER_HIERARCHY = [
        "domain",
        "application",
        "interfaces",
        "infrastructure",
    ]
```

Esta configuración actúa como nuestro contrato arquitectónico, definiendo cómo esperamos que se organicen los directorios de nuestra base de código. Tu equipo podría elegir nombres de capas diferentes o agregar reglas organizativas adicionales, pero el principio sigue siendo el mismo: Clean Architecture requiere capas explícitas y bien definidas con responsabilidades claras.

Con nuestra estructura definida, podemos implementar pruebas de verificación que garanticen que nuestra base de código mantenga esta organización:

```python
from pathlib import Path


def test_source_folders(self):
    """Verify todo_app contains only Clean Architecture layer folders."""
    src_path = Path("todo_app")
    folders = {f.name for f in src_path.iterdir() if f.is_dir()}

    # All layer folders must exist
    for layer in ArchitectureConfig.LAYER_HIERARCHY:
        self.assertIn(layer, folders, f"Missing {layer} layer folder")

    # No unexpected folders
    unexpected = folders - set(ArchitectureConfig.LAYER_HIERARCHY)
    self.assertEqual(
        unexpected,
        set(),
        f"Source should only contain Clean Architecture layers.\n"
        f"Unexpected folders found: {unexpected}",
    )
```

Esta simple verificación hace cumplir un principio fundamental de Clean Architecture: nuestro código fuente debe organizarse explícitamente en capas bien definidas. La clase `ArchitectureConfig` nos permite personalizar estas pruebas según tus preferencias particulares. Estamos examinando específicamente las carpetas de nivel superior dentro de `todo_app`, asegurando que coincidan con nuestra estructura arquitectónica esperada. Esto no se trata del contenido de estas carpetas (llegaremos a eso con la verificación de dependencias), sino más bien de verificar la base organizativa elemental de nuestra implementación de Clean Architecture.

Considera un escenario común: un equipo está agregando capacidades de notificación por correo electrónico al sistema de gestión de tareas. Un nuevo desarrollador, que aún no está familiarizado con Clean Architecture, crea una nueva carpeta `notifications` en el nivel raíz.

Esta elección organizativa aparentemente inocente representa el comienzo de la desviación arquitectónica (*architectural drift*). El código de notificaciones debería residir en la capa de Infraestructura, ya que es una preocupación externa. Al crear una nueva carpeta de nivel superior, hemos:

- Creado confusión sobre a dónde pertenece el código relacionado con las notificaciones.
- Comenzado a eludir la estratificación por capas explícita de Clean Architecture.
- Sentado un precedente para crear nuevas carpetas de nivel superior cuando los desarrolladores no están seguros de la ubicación adecuada.

Nuestra simple comprobación estructural detecta esto a tiempo (literalmente en cuestión de segundos si las pruebas se ejecutan en la máquina del desarrollador):

```bash
❯ pytest tests/architecture
========== test session starts ==================
tests/architecture/test_source_structure.py F
E   AssertionError: Items in the first set but not the second:
E   'notifications' : Source should only contain Clean Architecture layers.
E   Unexpected folders found: {'notifications'}
```

Esta advertencia impulsa al nuevo desarrollador a integrar adecuadamente el código de notificación en la capa de Infraestructura.

Estas sencillas comprobaciones estructurales detectan la desviación arquitectónica antes de que pueda comprometer la mantenibilidad del sistema. Sin embargo, una estructura adecuada es solo una parte de los requisitos de Clean Architecture. También debemos asegurarnos de que las dependencias entre estas capas fluyan en la dirección correcta. Examinemos cómo verificar la fundamental Regla de Dependencia de Clean Architecture.

#### Aplicación estricta de las reglas de dependencia

Con nuestra estructura de capas verificada, debemos asegurarnos de que estas capas interactúen correctamente de acuerdo con los principios de Clean Architecture. El más fundamental de ellos es la Regla de Dependencia (*Dependency Rule*), que establece que las dependencias solo deben apuntar hacia adentro, hacia las capas más centrales. Incluso una pequeña violación de esta regla puede comprometer la integridad arquitectónica que hemos construido con tanto cuidado.

Basándonos en nuestra verificación estructural, examinemos cómo detectar violaciones de la Regla de Dependencia. Esta regla es crucial para mantener una clara separación de responsabilidades, pero puede violarse de forma sutil durante el desarrollo.

Nuestra verificación de la Regla de Dependencia adopta un enfoque directo, examinando las sentencias `import` de Python para garantizar que solo fluyan hacia adentro a través de nuestras capas arquitectónicas. Si bien existen herramientas de análisis estático más sofisticadas (consulta la sección Lecturas complementarias), esta implementación directa detecta las violaciones más comunes:

```python
import ast
from pathlib import Path


def test_domain_layer_dependencies(self):
    """Verify domain layer has no outward dependencies."""
    domain_path = Path("todo_app/domain")
    violations = []

    for py_file in domain_path.rglob("*.py"):
        with open(py_file) as f:
            tree = ast.parse(f.read())

        for node in ast.walk(tree):
            if isinstance(node, ast.Import) or isinstance(node, ast.ImportFrom):
                module = node.names[0].name
                if module.startswith("todo_app."):
                    layer = module.split(".")[1]
                    if layer in ["infrastructure", "interfaces", "application"]:
                        violations.append(
                            f"{py_file.relative_to(domain_path)}: "
                            f"Domain layer cannot import from {layer} layer"
                        )
    self.assertEqual(violations, [], "\nDependency Rule Violations:\n" + "\n".join(violations))
```

Esta implementación de prueba aprovecha el módulo integrado `ast` de Python para analizar las sentencias de importación en el código de nuestra capa de Dominio. Funciona mediante:

1. Encontrar recursivamente todos los archivos de Python en la capa de Dominio.
2. Analizar sintácticamente cada archivo en un Árbol de Sintaxis Abstracta (*Abstract Syntax Tree* - AST).
3. Recorrer el AST para encontrar nodos `Import` e `ImportFrom`.
4. Comprobar cada importación para garantizar que no haga referencia a capas externas.

Si bien es posible realizar análisis estáticos más complejos, esta comprobación focalizada detecta eficazmente las violaciones de dependencias más críticas, que son aquellas que comprometerían la independencia de nuestra capa central de Dominio.

Considera un escenario del mundo real: un desarrollador está implementando notificaciones de finalización de tareas. Se percata de que `NotificationService` en la capa de Infraestructura ya tiene la lógica que necesita. En lugar de seguir los patrones de Clean Architecture, toma un atajo que viola nuestra fundamental Regla de Dependencia:

```python
# todo_app/domain/entities/task.py
# Dependency Rule Violation!
from todo_app.infrastructure.notifications.recorder import NotificationRecorder


class Task:

    def complete(self):
        self.status = TaskStatus.DONE
        self.completed_at = datetime.now()
        # Direct dependency on infrastructure –
        # violates Clean Architecture
        notification = NotificationRecorder()
        notification.notify_task_completed(self)
```

Este cambio puede parecer inocente porque cumple el objetivo inmediato. Sin embargo, crea exactamente el tipo de dependencia hacia el exterior que Clean Architecture prohíbe. Nuestra entidad de dominio ahora depende directamente de un componente de infraestructura, lo que significa que:

- La entidad `Task` ya no se puede probar sin `NotificationService`.
- No podemos cambiar las implementaciones de notificación sin modificar el código de dominio.
- Hemos creado un precedente para mezclar las preocupaciones de infraestructura con la lógica de dominio.

Nuestra comprobación de dependencias detecta esta violación de inmediato durante las pruebas:

```bash
❯ pytest tests/architecture
====================== test session starts ==========
...
E   'entities/task.py: Domain layer cannot import from infrastructure layer'
E   Dependency Rule Violations:
E   entities/task.py: Domain layer cannot import from infrastructure layer
====================== 2 passed in 0.01s ============
```

El mensaje de error identifica claramente:

- El archivo que contiene la violación.
- Qué regla arquitectónica se infringió.
- Cómo solucionarlo (la capa de Dominio no puede importar desde la infraestructura).

Estas verificaciones simples pero potentes ayudan a los equipos a mantener la alineación con los principios de Clean Architecture a medida que los sistemas evolucionan. Si bien nos hemos centrado en dos comprobaciones fundamentales (organización estructural y reglas de dependencia), los equipos pueden ampliar este enfoque para verificar otras características arquitectónicas:

- **Conformidad de interfaces**: Verificar que los adaptadores de interfaz implementen adecuadamente sus contratos declarados.
- **Implementaciones de repositorios**: Confirmar que las implementaciones de repositorios extiendan adecuadamente sus clases base abstractas.
- **Reglas específicas de capa**: Agregar reglas personalizadas sobre cómo cada capa debe estructurar y exponer sus componentes.

La clave es comenzar con comprobaciones focalizadas y de alto impacto que verifiquen tus límites arquitectónicos más cruciales. Luego puedes hacer evolucionar estas funciones de idoneidad junto con tu arquitectura, agregando verificaciones para nuevos patrones y restricciones a medida que tu sistema crezca.

Al detectar las violaciones estructurales y de dependencias a tiempo, evitamos la erosión gradual de los límites arquitectónicos que puede ocurrir durante el desarrollo rápido. Aunque estas comprobaciones no pueden reemplazar la comprensión arquitectónica, proporcionan retroalimentación inmediata y accionable cuando se violan las reglas arquitectónicas, ayudando así a los equipos a construir y preservar sistemas limpios y mantenibles.

---

### Sección 10.5: Resumen

En este capítulo, hemos explorado cómo los límites explícitos de Clean Architecture permiten una monitorización y verificación sistemáticas de nuestros sistemas. A través de nuestro sistema de gestión de tareas, hemos demostrado cómo implementar una observabilidad efectiva mientras mantenemos la integridad arquitectónica. Hemos visto cómo Clean Architecture transforma la monitorización de una preocupación transversal en una parte natural de la estructura de nuestro sistema.

Implementamos varios patrones clave de observabilidad que demuestran los beneficios de Clean Architecture:

- **Registro de logs independiente del framework** que respeta los límites arquitectónicos al tiempo que permite una visibilidad integral del sistema.
- **Rastreo de solicitudes a través de los límites** que mantiene una separación limpia entre las preocupaciones técnicas y las de negocio.
- **Verificación arquitectónica automatizada** que ayuda a los equipos a mantener los principios de Clean Architecture a medida que los sistemas evolucionan.

Lo más importante es que hemos visto cómo la cuidadosa atención de Clean Architecture a los límites hace que nuestros sistemas no solo sean mantenibles, sino también observables y verificables. Al organizar nuestra infraestructura de registro y monitorización de acuerdo con los principios de Clean Architecture, creamos sistemas que son más fáciles de entender, depurar y mantener a lo largo del tiempo.

En el [Capítulo 11](https://subscription.packtpub.com/book/programming/9781836642893/11), exploraremos cómo aplicar los principios de Clean Architecture a sistemas existentes, mostrando cómo estos mismos límites y patrones pueden guiar la transformación de bases de código heredadas (*legacy*) en arquitecturas limpias y mantenibles.

---

### Sección 10.6: Lecturas complementarias

- **Python Logging Cookbook** ([https://docs.python.org/3/howto/logging-cookbook.html](https://docs.python.org/3/howto/logging-cookbook.html)): Una colección de recetas de código relacionadas con el logging.
- **Building Evolutionary Architectures** ([https://www.oreilly.com/library/view/building-evolutionary-architectures/9781491986356/](https://www.oreilly.com/library/view/building-evolutionary-architectures/9781491986356/)): Excelente libro de arquitectura de software donde se acuñó por primera vez el término *Fitness Function* (Función de Idoneidad).
- **PyTestArch** ([https://github.com/zyskarch/pytestarch](https://github.com/zyskarch/pytestarch)): Framework de código abierto que te permite definir reglas arquitectónicas en código y ejecutarlas como pruebas.

# Parte 2: Implementación de Clean Architecture en Python

## Capítulo 5: La capa de Aplicación: Orquestación de casos de uso

En el [Capítulo 4](https://subscription.packtpub.com/book/programming/9781836642893/4), desarrollamos la capa de Dominio de nuestro sistema de gestión de tareas e implementamos entidades, objetos de valor y servicios de dominio que encapsulan nuestras reglas de negocio fundamentales. Si bien esto nos proporciona una base sólida, las reglas de negocio por sí solas no constituyen una aplicación utilizable. Necesitamos una forma de coordinar estos objetos de dominio para satisfacer las necesidades del usuario, tales como crear tareas, gestionar proyectos y manejar notificaciones. Aquí es donde entra en juego la capa de Aplicación.

La capa de Aplicación actúa como el director de orquesta en nuestra sinfonía de Clean Architecture. Coordina los objetos de dominio y los servicios externos para llevar a cabo casos de uso específicos, manteniendo al mismo tiempo un límite estricto entre nuestras reglas de negocio y el mundo exterior. Al implementar esta capa correctamente, creamos aplicaciones que no solo son funcionales, sino también mantenibles y adaptables al cambio.

En este capítulo, exploraremos cómo implementar una capa de Aplicación eficaz utilizando nuestro sistema de gestión de tareas como ejemplo. Veremos cómo crear casos de uso que orquesten objetos de dominio mientras preservan límites arquitectónicos limpios. Aprenderás a implementar modelos de solicitud y respuesta que definan con claridad los límites de los casos de uso, y cómo gestionar dependencias con servicios externos sin comprometer la integridad arquitectónica.

En este capítulo, vamos a cubrir los siguientes temas principales:

- **Comprensión del rol de la capa de Aplicación**
- **Implementación de interactores de casos de uso**
- **Definición de modelos de solicitud y respuesta (request and response)**
- **Mantenimiento de la separación respecto a servicios externos**

---

### Sección 5.1: Requisitos técnicos

Los ejemplos de código presentados en este capítulo y a lo largo del resto del libro se han probado con Python 3.13. Por motivos de brevedad, además de la ausencia de sentencias de registro (logging), algunos ejemplos de código en el capítulo están implementados solo parcialmente. Las versiones completas de todos los ejemplos se encuentran disponibles en el repositorio de GitHub complementario del libro en [https://github.com/PacktPublishing/Clean-Architecture-with-Python](https://github.com/PacktPublishing/Clean-Architecture-with-Python).

---

### Sección 5.2: Comprensión del rol de la capa de Aplicación

La capa de Aplicación sirve como una capa delgada que coordina nuestros objetos y servicios de dominio para llevar a cabo tareas significativas para el usuario. Mientras que nuestro modelo de dominio proporciona los bloques de construcción —tareas, proyectos, fechas límite—, es la capa de Aplicación la que ensambla estas piezas en funcionalidades útiles.

La capa de Aplicación cumple otra función crítica: el **ocultamiento de información** (**information hiding**). En el [Capítulo 4](https://subscription.packtpub.com/book/programming/9781836642893/4), vimos cómo las entidades de dominio ocultan su estado interno y sus detalles de implementación. La capa de Aplicación extiende este principio a través de los límites arquitectónicos, ocultando los detalles de infraestructura al dominio y las complejidades del dominio a las interfaces externas. Este ocultamiento deliberado de información es lo que hace que el esfuerzo adicional de crear puertos, adaptadores y modelos de solicitud/respuesta valga la pena. Al exponer únicamente lo necesario a través de interfaces cuidadosamente diseñadas, creamos un sistema donde los componentes pueden evolucionar de forma independiente y, al mismo tiempo, trabajar juntos sin fricciones.

#### Figura 5.1: Capa de Aplicación y gestión de tareas

En la Figura 5.1, ilustramos cómo encaja la capa de Aplicación dentro de las capas concéntricas de Clean Architecture. Actúa como mediadora entre la capa de Dominio —donde residen nuestras entidades centrales de negocio, como `Task` y `Project`— y las capas exteriores de nuestro sistema. Al encapsular casos de uso que orquestan entidades de dominio, la capa de Aplicación mantiene la **Regla de Dependencia** (**Dependency Rule**): las capas exteriores dependen hacia adentro, y las capas interiores permanecen inalteradas ante los cambios en las capas exteriores.

La capa de Aplicación tiene varias responsabilidades bien diferenciadas:

- **Orquestación de casos de uso**:
  - Coordinar objetos de dominio para cumplir las tareas del usuario.
  - Gestionar la secuencia de operaciones.
  - Asegurar que las reglas de negocio se apliquen adecuadamente.

- **Manejo de errores y validación**:
  - Validar la entrada antes de que llegue a los objetos de dominio.
  - Capturar y traducir errores de dominio.
  - Proporcionar respuestas de error consistentes.

- **Gestión de transacciones**:
  - Asegurar que las operaciones sean atómicas cuando sea necesario.
  - Mantener la consistencia de los datos.
  - Gestionar reversiones (*rollbacks*) ante fallos.

- **Traducción de límites**:
  - Convertir formatos de datos externos a formatos de dominio.
  - Transformar objetos de dominio para su presentación externa.
  - Gestionar la comunicación a través de los límites.

Estas responsabilidades trabajan en conjunto para crear una capa de orquestación robusta que mantiene límites limpios mientras garantiza un comportamiento confiable de la aplicación.

#### Manejo de errores con tipos de resultado

Antes de sumergirnos en nuestros patrones de implementación, es esencial comprender un concepto fundamental en nuestra capa de Aplicación: el uso del **tipo de resultado** (**result type**). Este patrón constituye la columna vertebral de nuestra estrategia de manejo de errores, proporcionando un manejo explícito del éxito y del fracaso en lugar de depender únicamente de excepciones. Este enfoque ofrece varios beneficios:

- Hace explícitas las rutas de éxito y fallo en las firmas de las funciones.
- Proporciona un manejo de errores consistente en toda la aplicación.
- Mantiene límites arquitectónicos limpios al traducir errores de dominio.
- Mejora la capacidad de prueba (*testability*) y la previsibilidad del manejo de errores.

En primer lugar, definimos una clase `Error` estandarizada para representar todos los errores a nivel de aplicación:

```python
class ErrorCode(Enum):
    NOT_FOUND = "NOT_FOUND"
    VALIDATION_ERROR = "VALIDATION_ERROR"
    # Add other error codes as needed


@dataclass(frozen=True)
class Error:
    """Standardized error information"""

    code: ErrorCode
    message: str
    details: Optional[dict[str, Any]] = None

    @classmethod
    def not_found(cls, entity: str, entity_id: str) -> Self:
        return cls(
            code=ErrorCode.NOT_FOUND,
            message=f"{entity} with id {entity_id} not found",
        )

    @classmethod
    def validation_error(cls, message: str) -> Self:
        return cls(code=ErrorCode.VALIDATION_ERROR, message=message)
```

A continuación, definimos una clase `Result` que encapsula ya sea un valor exitoso o un error:

```python
@dataclass(frozen=True)
class Result:
    """Represents success or failure of a use case execution"""

    value: Any = None
    error: Optional[Error] = None

    @property
    def is_success(self) -> bool:
        return self.error is None

    @classmethod
    def success(cls, value: Any) -> Self:
        return cls(value=value)

    @classmethod
    def failure(cls, error: Error) -> Self:
        return cls(error=error)
```

El uso de tipos de resultado permite una orquestación limpia de las operaciones de dominio, como se demuestra en este ejemplo de uso:

```python
try:
    project = find_project(project_id)
    task = create_task(task_details)
    project.add_task(task)
    notify_stakeholders(task)
    return Result.success(TaskResponse.from_entity(task))
except ProjectNotFoundError:
    return Result.failure(Error.not_found("Project", str(project_id)))
except ValidationError as e:
    return Result.failure(Error.validation_error(str(e)))
```

El ejemplo de uso anterior demuestra varias ventajas clave del patrón de resultado:

- **Rutas de error limpias**: Observa cómo los casos de error se manejan de manera uniforme a través de `Result.failure()`, proporcionando una interfaz consistente independientemente del tipo de error subyacente.
- **Traducción explícita de dominio**: La conversión de errores específicos del dominio (`ProjectNotFoundError`) a errores a nivel de aplicación ocurre limpiamente en el límite arquitectónico.
- **Contexto autocontenido**: El objeto `Result` empaqueta tanto el resultado como cualquier contexto de error, haciendo que el comportamiento de la función sea completamente claro a partir de su valor de retorno.
- **Claridad en las pruebas**: El ejemplo facilita probar tanto los casos de éxito como los de fallo verificando el estado del resultado en lugar de intentar capturar excepciones.

#### Límites del manejo de errores en Clean Architecture

Al implementar el manejo de errores en la capa de Aplicación, capturamos y transformamos explícitamente solo los errores esperados de dominio y de negocio en resultados. Por lo tanto, no tenemos una cláusula `except Exception:` emparejada con los errores esperados. Esta separación mantiene límites arquitectónicos limpios. Preocupaciones como el manejo global de errores permanecen en las capas exteriores.

#### Patrones de la capa de Aplicación

Para comprender cómo la capa de Aplicación gestiona sus responsabilidades, examinemos cómo fluyen los datos a través de nuestra arquitectura:

##### Figura 5.2: Flujo de solicitud y respuesta en Clean Architecture

El flujo mostrado en la Figura 5.2 demuestra varios patrones clave trabajando juntos. Una solicitud ingresa a través de la capa de Adaptadores de Interfaz (*Interface Adapters*) y es manejada por los objetos de transferencia de datos (**Data Transfer Objects - DTOs**) de nuestra capa de Aplicación, los cuales validan y transforman la entrada en formatos que nuestro dominio puede procesar. Los casos de uso luego orquestan las operaciones de dominio, trabajando con estas entradas validadas para interactuar con objetos de dominio y coordinarse con servicios externos a través de puertos. Los casos de uso devuelven resultados que encapsulan éxito (con un DTO de respuesta) o fallo (con un error), que la capa de Adaptadores de Interfaz puede luego mapear directamente a las respuestas HTTP apropiadas. No te preocupes por entender todos los componentes discretos de la Figura 5.2 en este momento; los cubriremos con más detalle a lo largo del capítulo.

Esta interacción coreografiada se basa en tres patrones fundamentales que trabajan juntos para mantener límites arquitectónicos limpios:

- **Interactores de casos de uso**: Sirven como los orquestadores principales, implementando operaciones de negocio específicas mientras gestionan transacciones y coordinan objetos de dominio. Aseguran que cada operación esté enfocada y que su ejecución sea consistente.
- **Límites de interfaz**: Establecen contratos claros entre nuestra capa de Aplicación y los servicios de los que depende.
- **Inversión de dependencias**: Permite una implementación flexible y pruebas sencillas a través de estos límites, asegurando que nuestra lógica central de negocio permanezca desacoplada de preocupaciones externas.

Inicialmente, nuestros casos de uso trabajarán con parámetros simples y devolverán estructuras de datos básicas. A medida que nuestra aplicación crezca, introduciremos patrones más sofisticados para manejar los datos que cruzan nuestros límites arquitectónicos. Esta evolución nos ayuda a mantener una separación limpia entre capas mientras conservamos nuestro código adaptable al cambio.

Estos patrones se alinean de forma natural con los principios SOLID que exploramos en el [Capítulo 2](https://subscription.packtpub.com/book/programming/9781836642893/2). Los casos de uso encarnan el **Principio de Responsabilidad Única** (**Single Responsibility Principle - SRP**) al enfocar cada operación en un objetivo específico. Las definiciones de interfaz respaldan la **segregación de interfaces** al definir contratos enfocados y específicos para el cliente.

#### Planificación para la evolución

Las aplicaciones rara vez permanecen estáticas; las exitosas crecen inevitablemente en alcance y complejidad. Lo que comienza como un simple sistema de gestión de tareas puede necesitar evolucionar para admitir múltiples equipos, integrarse con diversos servicios externos o manejar una automatización de flujos de trabajo compleja. Los patrones de la capa de Aplicación que hemos explorado permiten esta evolución con una fricción mínima.

Examinemos cómo nuestro sistema de gestión de tareas puede crecer a través de escenarios del mundo real:

- **Extensibilidad de casos de uso**:
  - Ampliar las notificaciones de tareas desde el correo electrónico para incluir Slack o plataformas de comunicación similares.
  - Componer casos de uso individuales, como asignar tarea y establecer fecha límite, en operaciones de mayor nivel como la planificación de un sprint (*sprint planning*).

- **Dependencias limpias**:
  - Comenzar con almacenamiento de archivos local para los adjuntos y luego agregar soporte para AWS S3 sin problemas a través de la misma interfaz.
  - Cambiar los motores de base de datos de SQLite a PostgreSQL sin modificar el código de los casos de uso.

- **Límites consistentes**:
  - Gestionar la transformación de datos en objetos de Solicitud (*Request*) a través de nuevas versiones de la API (v1 frente a v2) mientras se reutiliza el mismo código de caso de uso subyacente.
  - Implementar distintos transformadores de Respuesta (*Response*) para diferentes clientes (móvil, web, CLI) mientras se comparte una lógica de negocio central idéntica.

Esta base arquitectónica nos permite evolucionar nuestro sistema con confianza. Cuando el equipo de marketing solicite la integración con Salesforce, o cuando el cumplimiento normativo exija el registro de auditoría, estas capacidades podrán añadirse sin alterar la funcionalidad existente ni comprometer la integridad arquitectónica.

En la siguiente sección, exploraremos cómo implementar estos conceptos en Python, creando interactores de casos de uso robustos que defiendan los principios de Clean Architecture.

---

### Sección 5.3: Implementación de interactores de casos de uso

Habiendo explorado los fundamentos teóricos de la capa de Aplicación, ahora nos dirigimos a la implementación práctica. Los interactores de casos de uso son las clases concretas que implementan las reglas de negocio específicas de la aplicación. El término **interactores** enfatiza su rol de interactuar y coordinar varias partes del sistema. Mientras que la capa de Dominio define *cuáles* son las reglas de negocio, los interactores definen *cómo* y *cuándo* se aplican estas reglas en respuesta a necesidades específicas de la aplicación. En Python, podemos implementar estos interactores de una manera que sea tanto limpia como expresiva.

#### Estructuración de un caso de uso

Un interactor de caso de uso bien diseñado orquesta objetos de dominio mientras mantiene límites arquitectónicos limpios. Examinemos cómo se logra esto:

```python
@dataclass(frozen=True)
class CompleteTaskUseCase:
    """Use case for marking a task as complete and notifying stakeholders"""

    task_repository: TaskRepository

    def execute(
        self,
        task_id: UUID,
        completion_notes: Optional[str] = None,
    ) -> Result:
        ...
```

En primer lugar, al observar la estructura externa de un caso de uso, vemos algunos componentes clave. Las interfaces de dependencias se inyectan, y la clase dispone de un método público `execute` que devuelve un objeto `Result`.

A continuación, examinemos el método `execute`:

```python
def execute(
    self,
    task_id: UUID,
    completion_notes: Optional[str] = None,
) -> Result:
    try:
        # Input validation
        task = self.task_repository.get(task_id)
        task.complete(notes=completion_notes)
        self.task_repository.save(task)

        # Return simplified task data
        return Result.success(
            {
                "id": str(task.id),
                "status": "completed",
                "completion_date": task.completed_at.isoformat(),
            }
        )

    except TaskNotFoundError:
        return Result.failure(Error.not_found("Task", str(task_id)))
    except ValidationError as e:
        return Result.failure(Error.validation_error(str(e)))
```

Aquí podemos apreciar la orquestación de las reglas de negocio sobre el objeto de dominio `Task` para cumplir el objetivo discreto del caso de uso: completar la tarea.

Esta implementación encarna varios principios arquitectónicos fundamentales:

- **Encapsulación**: La clase de caso de uso proporciona un límite claro alrededor de una operación de negocio específica.
- **Definición de interfaces**: El método `execute` proporciona una interfaz limpia y enfocada que utiliza el tipo de resultado. El patrón de resultado asegura que tanto las rutas de éxito como las de fallo sean explícitas en nuestra interfaz, convirtiendo el manejo de errores en una preocupación de primer orden.
- **Manejo de errores**: Los errores de dominio se capturan y se traducen en errores a nivel de aplicación.
- **Inyección de dependencias**: Las dependencias se pasan a través del constructor, adhiriéndose al **Principio de Inversión de Dependencias** (**Dependency Inversion Principle - DIP**) introducido en el [Capítulo 2](https://subscription.packtpub.com/book/programming/9781836642893/2).

De estos principios, la inyección de dependencias merece especial atención, ya que hace posible gran parte de nuestra flexibilidad arquitectónica.

#### Inyección de dependencias

Anteriormente vimos cómo la inyección de dependencias ayuda a mantener límites arquitectónicos limpios en nuestros casos de uso. Profundicemos en esto examinando cómo estructurar nuestras interfaces para maximizar los beneficios de la inyección de dependencias, garantizando al mismo tiempo que nuestros casos de uso sigan siendo flexibles y comprobables mediante pruebas. En Python, podemos implementar esto elegantemente utilizando clases base abstractas (*Abstract Base Classes*):

```python
class TaskRepository(ABC):
    """Repository interface defined by the Application Layer"""

    @abstractmethod
    def get(self, task_id: UUID) -> Task:
        """Retrieve a task by its ID"""
        pass

    @abstractmethod
    def save(self, task: Task) -> None:
        """Save a task to the repository"""
        pass

    @abstractmethod
    def delete(self, task_id: UUID) -> None:
        """Delete a task from the repository"""
        pass


class NotificationService(ABC):
    """Service interface for sending notifications"""

    @abstractmethod
    def notify_task_assigned(self, task_id: UUID) -> None:
        """Notify when a task is assigned"""
        pass

    @abstractmethod
    def notify_task_completed(self, task: Task) -> None:
        """Notify when a task is completed"""
        pass
```

Al definir estas interfaces en la capa de Aplicación, fortalecemos nuestros límites arquitectónicos mientras proporcionamos contratos claros para que las capas exteriores los implementen. Este enfoque proporciona varios beneficios avanzados más allá de la inyección de dependencias básica:

- Las definiciones de interfaz expresan exactamente lo que la capa de Aplicación necesita, ni más ni menos.
- Los métodos abstractos documentan el comportamiento esperado mediante firmas de método claras y *docstrings*.
- La capa de Aplicación mantiene el control sobre sus dependencias mientras permanece independiente de sus implementaciones.
- Las implementaciones para pruebas pueden centrarse exactamente en lo que requiere cada caso de uso.

Una implementación concreta que se adhiera a este contrato podría adoptar una forma como esta:

```python
class MongoDbTaskRepository(TaskRepository):
    """MongoDB implementation of the TaskRepository interface"""

    def __init__(self, client: MongoClient):
        self.client = client
        self.db = client.task_management
        self.tasks = self.db.tasks

    def get(self, task_id: UUID) -> Task:
        """Retrieve a task by its ID"""
        document = self.tasks.find_one({"_id": str(task_id)})
        if not document:
            raise TaskNotFoundError(task_id)
        # ... remainder of method implementation

    # Other interface methods implemented ...
```

Este ejemplo demuestra cómo las capas exteriores pueden implementar las interfaces definidas por nuestra capa de Aplicación, manejando las particularidades de la persistencia de datos mientras se adhieren al contrato que nuestra lógica de negocio espera.

#### Manejo de operaciones complejas

Los casos de uso del mundo real a menudo involucran múltiples pasos y potenciales puntos de fallo. Examinemos cómo gestionar esta complejidad manteniendo los principios de Clean Architecture. Consideremos un escenario de finalización de proyecto que requiere la coordinación de múltiples tareas.

La clase `CompleteProjectUseCase` sigue nuestro patrón establecido:

```python
@dataclass(frozen=True)
class CompleteProjectUseCase:
    project_repository: ProjectRepository
    task_repository: TaskRepository
    notification_service: NotificationService

    def execute(
        self, project_id: UUID, completion_notes: Optional[str] = None
    ) -> Result:
        ...
```

Ahora, examinemos su método `execute`:

```python
def execute(
    self, project_id: UUID, completion_notes: Optional[str] = None
) -> Result:
    try:
        # Validate project exists
        project = self.project_repository.get(project_id)

        # Complete all outstanding tasks
        for task in project.incomplete_tasks:
            task.complete()
            self.task_repository.save(task)
            self.notification_service.notify_task_completed(task)

        # Complete the project itself
        project.mark_completed(notes=completion_notes)
        self.project_repository.save(project)

        return Result.success(
            {
                "id": str(project.id),
                "status": project.status,
                "completion_date": project.completed_at,
                "task_count": len(project.tasks),
                "completion_notes": project.completion_notes,
            }
        )

    except ProjectNotFoundError:
        return Result.failure(Error.not_found("Project", str(project_id)))
    except ValidationError as e:
        return Result.failure(Error.validation_error(str(e)))
```

Esta implementación demuestra varios patrones para gestionar la complejidad:

- **Operaciones coordinadas**: El caso de uso gestiona múltiples operaciones relacionadas como una única unidad lógica:
  - Completar todas las tareas pendientes.
  - Actualizar el estado del proyecto.
  - Notificar a las partes interesadas (*stakeholders*).

- **Gestión de errores**: El caso de uso proporciona un manejo exhaustivo de errores:
  - Los errores específicos de dominio se capturan y se traducen.
  - Se consideran los posibles fallos de cada operación. En un ejemplo más elaborado, se podría observar una reversión (*rollback*) del guardado de tareas si la actualización o guardado del proyecto fallara.
  - Las respuestas de error son consistentes e informativas.

- **Dependencias claras**: Los servicios requeridos se definen explícitamente:
  - Definición de repositorios para el acceso a datos.
  - Provisión de servicios de notificación para la comunicación externa.
  - Inyección de dependencias para flexibilidad y pruebas.

- **Validación de entrada**: Los parámetros se validan antes del procesamiento:
  - Se comprueba la existencia de los IDs requeridos.
  - Los parámetros opcionales se manejan apropiadamente.
  - Se hacen cumplir las reglas de dominio.

- **Integridad transaccional**: Los cambios tanto en las tareas como en los proyectos se manejan como una operación cohesiva:
  - El ejemplo de código podría extenderse para admitir transaccionalidad real simplemente capturando el estado inicial y luego revirtiendo si una de nuestras sentencias falla. Consulta el código de `CompleteProjectUseCase` en el repositorio de GitHub complementario del libro para ver un ejemplo de esto.

Al aplicar estos patrones de manera consistente en nuestra capa de Aplicación, creamos un sistema robusto que maneja operaciones complejas con elegancia mientras mantiene límites arquitectónicos limpios y una clara separación de responsabilidades.

---

### Sección 5.4: Definición de modelos de solicitud y respuesta (request and response)

En la sección anterior, nuestros casos de uso trabajaron directamente con tipos primitivos y diccionarios. Si bien este enfoque puede funcionar para casos simples, a medida que nuestra aplicación crece, necesitamos formas más estructuradas para manejar los datos que cruzan nuestros límites arquitectónicos. Los modelos de solicitud y respuesta sirven para este propósito, proporcionando DTOs especializados que manejan la transformación de datos entre las capas exteriores y el núcleo de nuestra aplicación. Partiendo de los principios de ocultamiento de información que presentamos anteriormente, estos modelos extienden dicho concepto a los límites arquitectónicos, protegiendo específicamente nuestra lógica de dominio de los detalles de formato externos y resguardando al mismo tiempo a las interfaces externas de las particularidades de implementación del dominio. Esta protección recíproca de límites es particularmente importante a medida que diferentes interfaces evolucionan a ritmos distintos.

#### Modelos de solicitud (Request models)

Los modelos de solicitud capturan y validan los datos entrantes antes de que lleguen a los casos de uso de nuestra capa de Aplicación. Proporcionan una estructura clara para los datos de entrada y realizan una validación preliminar:

```python
@dataclass(frozen=True)
class CompleteProjectRequest:
    """Data structure for project completion requests"""

    project_id: str  # From API (will be converted to UUID)
    completion_notes: Optional[str] = None

    def __post_init__(self) -> None:
        """Validate request data"""
        if not self.project_id.strip():
            raise ValidationError("Project ID is required")

        if self.completion_notes and len(self.completion_notes) > 1000:
            raise ValidationError(
                "Completion notes cannot exceed 1000 characters"
            )

    def to_execution_params(self) -> dict:
        """Convert validated request data to use case parameters"""
        return {
            "project_id": UUID(self.project_id),
            "completion_notes": self.completion_notes,
        }
```

Los modelos de solicitud cumplen múltiples propósitos arquitectónicos al establecer un límite claro entre las capas exteriores e interiores. A través de la validación de entrada y el método `to_execution_params`, aseguran que los casos de uso permanezcan enfocados puramente en la lógica de negocio. El paso de validación detecta datos mal formados tempranamente, mientras que `to_execution_params` transforma formatos amigables para la API (tales como identificadores en cadena de texto) en tipos de dominio adecuados (tales como `UUID`) que nuestra lógica de negocio espera.

Esta capacidad de transformación es particularmente poderosa porque:

- Mantiene los casos de uso limpios y enfocados, trabajando únicamente con tipos de dominio.
- Centraliza la lógica de conversión de datos en una ubicación única y predecible.
- Permite que los formatos de API evolucionen sin afectar la lógica de negocio central.
- Mejora la capacidad de prueba al proporcionar límites de formato bien definidos.

Para cuando los datos fluyen a través de un modelo de solicitud y llegan a nuestros casos de uso, ya han sido validados y transformados al formato exacto que espera nuestra lógica de dominio. Esto preserva la separación de responsabilidades de Clean Architecture, garantizando que los detalles de implementación de las capas exteriores (tales como cómo se formatean los IDs en las solicitudes HTTP) nunca se filtren en nuestras reglas de negocio centrales.

#### Modelos de respuesta (Response models)

Los modelos de respuesta manejan la transformación de objetos de dominio en estructuras adecuadas para el consumo externo. Mantienen nuestros límites arquitectónicos limpios al controlar explícitamente qué datos del dominio se exponen y cómo se formatean:

```python
@dataclass(frozen=True)
class CompleteProjectResponse:
    """Data structure for project completion responses"""

    id: str
    status: str
    completion_date: str
    task_count: int
    completion_notes: Optional[str]

    @classmethod
    def from_entity(
        cls, project: Project, user_service: UserService
    ) -> "CompleteProjectResponse":
        """Create response from domain entities"""
        return cls(
            id=str(project.id),
            status=project.status,
            completion_date=project.completed_at,
            task_count=len(project.tasks),
            completion_notes=project.completion_notes,
        )
```

Mientras que `to_execution_params` del modelo de solicitud transforma los datos entrantes para ajustarse a las expectativas del dominio, `from_entity` se encarga del viaje de salida al convertir los objetos de dominio en formatos adecuados para cruzar el límite hacia la capa de Adaptadores. Este patrón simétrico significa que nuestros casos de uso pueden trabajar puramente con objetos de dominio mientras que tanto la entrada como la salida se adaptan automáticamente a las necesidades externas.

El método `from_entity` cumple varios propósitos clave:

- Protege los objetos de dominio de quedar expuestos a las capas externas.
- Controla exactamente qué datos se exponen y en qué formato (por ejemplo, convirtiendo `UUID` nuevamente a cadenas de texto).
- Proporciona un punto de serialización consistente para todas las interfaces externas.
- Permite campos calculados o derivados (tales como `task_count`) sin modificar los objetos de dominio.
- Incluye datos calculados o agregados que no están presentes en la entidad base.
- Optimiza el rendimiento al omitir grandes cantidades de datos irrelevantes.
- Incluye metadatos específicos de la operación.

Revisemos una versión evolucionada de `CompleteProjectUseCase` para mostrar cómo los modelos de solicitud, la lógica de dominio y los modelos de respuesta trabajan en armonía:

```python
@dataclass(frozen=True)
class CompleteProjectUseCase:
    project_repository: ProjectRepository
    task_repository: TaskRepository
    notification_service: NotificationService

    # Using CompleteProjectRequest vs discreet parameters
    def execute(self, request: CompleteProjectRequest) -> Result:
        try:
            params = request.to_execution_params()
            project = self.project_repository.get(params["project_id"])
            project.mark_completed(notes=params["completion_notes"])

            # Complete all outstanding tasks
            # ... Truncated for brevity

            self.project_repository.save(project)

            # using CompleteProjectResponse vs handbuilt dict
            response = CompleteProjectResponse.from_entity(project)
            return Result.success(response)

        except ProjectNotFoundError:
            return Result.failure(
                Error.not_found("Project", str(params["project_id"]))
            )
        except ValidationError as e:
            return Result.failure(Error.validation_error(str(e)))
```

Este ejemplo demuestra cómo nuestro caso de uso permanece enfocado puramente en orquestar la lógica de dominio, mientras que los modelos de solicitud y respuesta se encargan de las transformaciones necesarias en nuestros límites arquitectónicos. El caso de uso recibe una solicitud ya validada, trabaja con los tipos de dominio adecuados durante toda su ejecución y devuelve un modelo de respuesta envuelto en un objeto `Result` que puede ser consumido por cualquier implementación de las capas exteriores.

En la capa de Adaptadores de Interfaz, estos modelos de respuesta pueden ser consumidos por una variedad de componentes, incluidos controladores que manejan solicitudes HTTP, procesadores de comandos de interfaz de línea de comandos (CLI) o manejadores de colas de mensajes. Cada adaptador puede transformar los datos de respuesta de manera apropiada para su mecanismo de transporte específico, convirtiéndolos a JSON sobre HTTP, salida de consola o cargas útiles (*payloads*) de mensajes según sea necesario.

---

### Sección 5.5: Mantenimiento de la separación respecto a servicios externos

Si bien los modelos de solicitud y respuesta gestionan la transformación de datos en la superficie de nuestra API, nuestra aplicación también debe interactuar con servicios externos tales como sistemas de correo electrónico, almacenamiento de archivos y APIs de terceros. La capa de Aplicación mantiene la separación respecto a estos servicios a través de **puertos** (**ports**): interfaces que definen exactamente qué capacidades requiere nuestra aplicación sin especificar detalles de implementación. En nuestro sistema de gestión de tareas, los servicios externos podrían incluir:

- Servicios de correo electrónico para el envío de notificaciones (tales como SendGrid o AWS SES).
- Sistemas de almacenamiento de archivos para adjuntos (tales como AWS S3 o Google Cloud Storage).
- Servicios de autenticación (tales como Auth0 u Okta).
- Servicios de integración de calendarios (tales como Google Calendar o Microsoft Outlook).
- Sistemas de mensajería externa (tales como Slack o Microsoft Teams).

Aunque tanto los modelos de solicitud/respuesta como los puertos sirven para mantener límites arquitectónicos limpios, abordan aspectos diferentes de la interacción del sistema con el mundo exterior. Los modelos de solicitud/respuesta manejan la transformación de datos en los límites de nuestra API, siguiendo interfaces consistentes en todos los casos de uso (por ejemplo, `from_entity` y `to_execution_params`) para garantizar un manejo uniforme de los datos.

Los puertos, en cambio, definen interfaces para los servicios de los que depende nuestra capa de Aplicación, donde cada puerto está diseñado específicamente para representar las capacidades de un servicio externo en particular. Este enfoque dual asegura que nuestra lógica central de negocio permanezca independiente tanto de los detalles de formato de datos como de las particularidades de implementación externa.

#### Límites de interfaz (Interface boundaries)

Los puertos permiten a la capa de Aplicación especificar exactamente qué capacidades necesita de los servicios externos sin estar acoplada a implementaciones específicas. Examinemos cómo funcionan juntos estos mecanismos de límite:

```python
# Port: Defines capability needed by Application Layer
class NotificationPort(ABC):

    @abstractmethod
    def notify_task_completed(self, task: Task) -> None:
        """Notify when a task is completed"""
        pass

    # other capabilities as needed
```

Esta interfaz ejemplifica el ocultamiento de información en los límites arquitectónicos. Revela únicamente las operaciones que nuestra capa de Aplicación necesita, mientras oculta todos los detalles de implementación: si las notificaciones se envían por correo electrónico, SMS u otro mecanismo permanece completamente oculto para nuestra lógica central de negocio.

Luego, en cada caso de uso, podemos aprovechar el puerto definido de esta manera:

```python
@dataclass
class SetTaskPriorityUseCase:
    task_repository: TaskRepository
    notification_service: NotificationPort  # Depends on capability interface

    def execute(self, request: SetTaskPriorityRequest) -> Result:
        try:
            params = request.to_execution_params()

            task = self.task_repository.get(params["task_id"])
            task.priority = params["priority"]

            self.task_repository.save(task)

            if task.priority == Priority.HIGH:
                self.notification_service.notify_task_high_priority(task)

            return Result.success(TaskResponse.from_entity(task))
        except ValidationError as e:
            return Result.failure(Error.validation_error(str(e)))
```

Este enfoque demuestra los roles diferenciados de nuestros mecanismos de límite:

- Los modelos de solicitud/respuesta gestionan la transformación de datos en los límites de la API.
- Los puertos definen las capacidades de servicio que nuestros casos de uso necesitan.
- La capa de Aplicación utiliza ambos para mantener una separación limpia mientras coordina el flujo general.

Recordarás que en nuestro ejemplo anterior en la sección *Manejo de operaciones complejas* hicimos referencia a un `NotificationService` concreto; aquí, hemos madurado nuestro diseño definiendo una interfaz abstracta o puerto (`NotificationPort`). Este cambio de una implementación a una interfaz se alinea mejor con la Regla de Dependencia y proporciona límites arquitectónicos más claros.

Al depender únicamente de la interfaz abstracta de capacidad en lugar de implementaciones concretas, nuestro caso de uso mantiene el ocultamiento de información en ambas direcciones: el caso de uso no sabe nada sobre los detalles de implementación de las notificaciones, mientras que los servicios de notificación no saben nada sobre los aspectos internos del caso de uso más allá de los parámetros proporcionados a través de la interfaz.

Ahora podemos explorar cómo gestionar eficazmente las dependencias externas que estos límites nos ayudan a controlar.

#### Soporte a requisitos de servicios en evolución

A medida que los sistemas evolucionan, necesitamos patrones que nos permitan agregar nuevas capacidades y adaptarnos a implementaciones de servicios cambiantes. Examinemos dos patrones clave para gestionar esta evolución.

##### Soporte a integración opcional

A medida que las aplicaciones crecen, a menudo queremos que ciertas integraciones de servicios sean opcionales o específicas de un entorno determinado. El patrón de servicios opcionales ayuda a gestionar esto:

```python
@dataclass(frozen=True)
class TaskManagementUseCase:
    task_repository: TaskRepository
    notification_service: NotificationPort
    _optional_services: dict[str, Any] = field(default_factory=dict)

    def register_service(self, name: str, service: Any) -> None:
        """Register an optional service"""
        self._optional_services[name] = service

    def complete_task(self, task_id: UUID) -> Result:
        try:
            task = self.task_repository.get(task_id)
            task.complete()
            self.task_repository.save(task)

            # Required notification
            self.notification_service.notify_task_completed(task)

            # Optional integrations
            if analytics := self._optional_services.get("analytics"):
                analytics.track_task_completion(task.id)
            if audit := self._optional_services.get("audit"):
                audit.log_task_completion(task.id)

            return Result.success(TaskResponse.from_entity(task))
        except ValidationError as e:
            return Result.failure(Error.validation_error(str(e)))
```

Este enfoque ofrece varias ventajas:

- Las operaciones centrales de negocio permanecen enfocadas y estables a través de las dependencias principales `task_repository` y `notification_service`.
- Se pueden añadir nuevas capacidades sin modificar el código existente mediante el diccionario flexible `_optional_services`.
- Los servicios opcionales pueden configurarse según las necesidades de despliegue a través del método `register_service`.
- Las pruebas siguen siendo sencillas, ya que las dependencias son explícitas en el constructor, con los servicios opcionales claramente separados de los requisitos centrales.

El uso de un diccionario para almacenar servicios opcionales combinado con la ejecución condicional (por ejemplo, `if analytics := self._optional_services.get('analytics'):`) proporciona un patrón limpio para manejar de manera fluida funcionalidades que pueden o no estar presentes en un despliegue determinado.

##### Adaptación a cambios en los servicios

Al integrarnos con servicios de terceros o gestionar actualizaciones de sistemas, a menudo necesitamos alternar entre diferentes interfaces. El patrón adaptador (*Adapter pattern*) nos ayuda a gestionar esto:

```python
class ModernNotificationService:
    """Third-party service with a different interface"""

    def send_notification(self, payload: dict) -> None:
        # Modern service implementation
        pass


class ModernNotificationAdapter(NotificationPort):
    """Adapts modern notification service to work with our interface"""

    def __init__(self, modern_service: ModernNotificationService):
        self._service = modern_service

    def notify_task_completed(self, task: Task) -> None:
        self._service.send_notification(
            {"type": "TASK_COMPLETED", "taskId": str(task.id)}
        )
```

El patrón adaptador es particularmente valioso en varios escenarios:

- **Integración con servicios de terceros**: `ModernNotificationService` se puede envolver sin modificar su propia interfaz.
- **Gestión de actualizaciones del sistema**: La capa de traducción del adaptador (`send_notification` a métodos de notificación específicos) aísla los cambios en las implementaciones de los servicios.
- **Soporte para múltiples implementaciones**: Diferentes servicios pueden adaptarse a la misma interfaz `NotificationPort`.
- **Transición entre versiones de servicios**: El mapeo de cargas útiles estructuradas en `notify_task_completed` permite la evolución del protocolo manteniendo la compatibilidad hacia atrás.

Al utilizar estos patrones en conjunto, podemos crear sistemas que manejen con elegancia tanto características opcionales como implementaciones de servicios cambiantes, manteniendo siempre límites arquitectónicos limpios.

---

### Sección 5.6: Resumen

En este capítulo, exploramos la capa de Aplicación de Clean Architecture, centrándonos en cómo orquesta los objetos de dominio y se coordina con los servicios externos para satisfacer las necesidades del usuario. Aprendimos cómo implementar casos de uso que preservan límites arquitectónicos limpios mientras proporcionan una funcionalidad significativa.

A través de nuestro ejemplo del sistema de gestión de tareas, descubrimos cómo crear interactores de casos de uso que coordinan objetos de dominio respetando la **Regla de Dependencia** introducida en el [Capítulo 1](https://subscription.packtpub.com/book/programming/9781836642893/1). Nos basamos en los principios SOLID del [Capítulo 2](https://subscription.packtpub.com/book/programming/9781836642893/2) y en los patrones orientados a tipos del [Capítulo 3](https://subscription.packtpub.com/book/programming/9781836642893/3) para crear implementaciones robustas y mantenibles. Nuestros casos de uso orquestan eficazmente los objetos y servicios de dominio que desarrollamos en el [Capítulo 4](https://subscription.packtpub.com/book/programming/9781836642893/4), demostrando cómo las capas de Clean Architecture funcionan juntas en armonía.

Implementamos varios patrones y conceptos clave:

- **Interactores de casos de uso** que orquestan las operaciones de dominio.
- **Modelos de solicitud y respuesta** que crean límites claros.
- **Patrones de manejo de errores** que mantienen la separación arquitectónica.
- **Definiciones de interfaz** que mantienen aisladas las preocupaciones externas.

Estas implementaciones demostraron cómo mantener la integridad de nuestra arquitectura mientras se atienden los requisitos del mundo real. Vimos cómo unos límites adecuados permiten que nuestra aplicación evolucione y se adapte a necesidades cambiantes sin comprometer su diseño fundamental.

En el [Capítulo 6](https://subscription.packtpub.com/book/programming/9781836642893/6), exploraremos cómo nuestros límites limpios permiten la creación de adaptadores eficaces que traducen entre nuestra capa de Aplicación y el mundo exterior. Veremos cómo los patrones que hemos establecido con modelos de solicitud/respuesta y puertos se extienden de forma natural a la implementación de controladores, pasarelas (*gateways*) y presentadores (*presenters*).

---

### Sección 5.7: Lecturas complementarias

Para profundizar en los temas tratados en este capítulo, consulta los siguientes recursos:

- *Building Microservices: Designing Fine-Grained Systems* de Sam Newman. Aunque está centrado en microservicios, los capítulos de este libro sobre límites de servicios, comunicación entre servicios y manejo de datos brindan valiosas ideas para crear límites bien definidos en las capas de aplicación, las cuales también se pueden aplicar a aplicaciones monolíticas.
- *Hexagonal Architecture* de Alistair Cockburn ([https://alistair.cockburn.us/hexagonal-architecture/](https://alistair.cockburn.us/hexagonal-architecture/)). Este artículo explica el patrón de puertos y adaptadores (o arquitectura hexagonal), el cual es altamente complementario con los principios de Clean Architecture. Proporciona una comprensión clara de la gestión de dependencias y la traducción de límites, aspectos centrales para la implementación de la capa de Aplicación.

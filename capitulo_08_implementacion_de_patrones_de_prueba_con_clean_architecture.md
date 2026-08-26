# Parte 2: Implementación de Clean Architecture en Python

## Capítulo 8: Implementación de patrones de prueba con Clean Architecture

En los capítulos anteriores, hemos construido un sistema de gestión de tareas implementando cuidadosamente cada una de las capas de **Clean Architecture**, desde entidades de dominio puras hasta interfaces independientes de frameworks. Para muchos desarrolladores, las pruebas pueden parecer abrumadoras: una carga necesaria que se vuelve cada vez más compleja a medida que los sistemas evolucionan. Clean Architecture ofrece una perspectiva diferente, proporcionando un enfoque estructurado que hace que las pruebas sean tanto manejables como significativas.

Ahora que hemos trabajado a través de todas las capas de Clean Architecture, demos un paso atrás y examinemos cómo este enfoque arquitectónico transforma nuestras prácticas de prueba. Al respetar los límites y las reglas de dependencia de Clean Architecture, creamos sistemas que son intrínsecamente comprobables mediante pruebas (*testable*). Las responsabilidades claras y las interfaces explícitas de cada capa nos guían no solo en qué probar, sino en cómo probar de manera efectiva.

En este capítulo, aprenderás cómo los límites explícitos de Clean Architecture permiten una cobertura de pruebas exhaustiva mediante pruebas unitarias y de integración enfocadas. A través de ejemplos prácticos, descubrirás cómo la separación de responsabilidades de Clean Architecture nos permite verificar minuciosamente el comportamiento del sistema manteniendo las pruebas fáciles de mantener. Veremos cómo unas interfaces bien definidas y las reglas de dependencia conducen de forma natural a conjuntos de pruebas (*test suites*) que sirven tanto como herramientas de verificación como barreras de contención (*guardrails*) arquitectónicas.

Al final de este capítulo, serás capaz de crear suites de prueba que sean enfocadas, mantenibles y eficaces para detectar problemas a tiempo, transformando las pruebas de ser una carga a convertirse en una herramienta poderosa para mantener la integridad arquitectónica. A lo largo del camino, examinaremos los siguientes temas principales:

- **Fundamentos de las pruebas en Clean Architecture**
- **Pruebas de componentes limpios: pruebas unitarias en la práctica**
- **Pruebas a través de los límites arquitectónicos**
- **Herramientas y patrones para el mantenimiento de pruebas**

---

### Sección 8.1: Requisitos técnicos

Los ejemplos de código presentados en este capítulo y a lo largo del resto del libro se prueban con Python 3.13. Por brevedad, la mayoría de los ejemplos de código en el capítulo solo están implementados parcialmente. Las versiones completas de todos los ejemplos se pueden encontrar en el repositorio de GitHub complementario del libro en [https://github.com/PacktPublishing/Clean-Architecture-with-Python](https://github.com/PacktPublishing/Clean-Architecture-with-Python).

---

### Sección 8.2: Fundamentos de las pruebas en Clean Architecture

Las capas cuidadosamente estructuradas y las dependencias explícitas en Clean Architecture no solo hacen que nuestros sistemas sean más mantenibles; transforman fundamentalmente la forma en que abordamos las pruebas. Muchos equipos, enfrentados a bases de código complejas y límites poco claros, recurren a las pruebas de extremo a extremo (*end-to-end*) mediante herramientas como Selenium o navegadores *headless*. Si bien estas pruebas pueden proporcionar confianza en que los flujos de trabajo críticos del usuario funcionan, a menudo son lentas, frágiles y proporcionan una retroalimentación deficiente cuando se producen fallos. Además, configurar pruebas unitarias y de integración exhaustivas en tales sistemas puede resultar abrumador. ¿Por dónde empezar cuando todo está estrechamente acoplado?

Clean Architecture ofrece una perspectiva diferente. En lugar de depender principalmente de pruebas de extremo a extremo, podemos generar confianza en nuestro sistema a través de pruebas enfocadas y mantenibles que respetan los límites arquitectónicos. En lugar de luchar contra dependencias complejas y configuraciones engorrosas, descubrimos que nuestros límites arquitectónicos brindan una guía natural para construir suites de pruebas eficaces.

Las pruebas son cruciales para mantener sistemas de software saludables. A través de las pruebas, verificamos que nuestro código funcione según lo previsto, detectamos regresiones tempranamente y nos aseguramos de que nuestros límites arquitectónicos permanezcan intactos. Los límites explícitos y las reglas de dependencia de Clean Architecture facilitan la redacción de pruebas enfocadas y mantenibles en todos los niveles de nuestro sistema.

**Figura 8.1: Pirámide de pruebas que representa la distribución ideal de los tipos de prueba**

La pirámide de pruebas representada en la Figura 8.1 demuestra la distribución ideal de los tipos de prueba en un sistema bien diseñado. La amplia base consta de pruebas unitarias rápidas que verifican componentes individuales de forma aislada, proporcionando una retroalimentación rápida durante el desarrollo. Subiendo por la pirámide, las pruebas de integración verifican las interacciones entre componentes mientras se mantienen razonablemente rápidas de ejecutar. En la cúspide, una pequeña cantidad de pruebas de extremo a extremo verifica los flujos de trabajo de usuario críticos, aunque estas pruebas normalmente se ejecutan más lentamente y proporcionan una retroalimentación menos precisa cuando ocurren fallos.

Este enfoque arquitectónico permite de forma natural una distribución óptima de las pruebas gracias a sus interfaces bien definidas y al aislamiento de los componentes. Nuestra lógica de negocio central, aislada en las capas de Dominio y Aplicación, se verifica fácilmente mediante pruebas unitarias enfocadas y sin dependencias externas. Los adaptadores de interfaz proporcionan límites claros para las pruebas de integración, permitiéndonos verificar las interacciones de los componentes sin tener que probar flujos de trabajo completos. Esta claridad arquitectónica significa que podemos generar confianza en nuestro sistema principalmente a través de pruebas rápidas y enfocadas. Si bien las pruebas de extremo a extremo a través de interfaces de usuario tienen su lugar, Clean Architecture nos permite generar una confianza sustancial en nuestro sistema únicamente a través de pruebas unitarias y de integración enfocadas.

A lo largo de este capítulo utilizaremos **pytest**, el framework de pruebas estándar de Python, para demostrar estos patrones de prueba. Al aprovechar los límites de Clean Architecture, veremos cómo el enfoque directo de pytest nos ayuda a construir una cobertura de prueba integral sin frameworks de prueba complejos ni herramientas de automatización del navegador. Si bien los beneficios de prueba de Clean Architecture se aplican independientemente de la elección de herramientas, el uso de un marco único y bien consolidado nos permite centrarnos en los principios arquitectónicos en lugar de en la sintaxis de las pruebas.

Clean Architecture requiere más configuración inicial que enfoques más simples, involucrando interfaces adicionales y separación de capas que podrían parecer innecesarias para aplicaciones pequeñas. Sin embargo, esta inversión inicial transforma las pruebas de un desafío técnico complejo a una verificación directa. Las alternativas estrechamente acopladas pueden parecer más rápidas al principio, pero pronto requieren coordinar bases de datos y servicios externos solo para probar una funcionalidad básica. La disciplina arquitectónica que hemos establecido crea sistemas que son intrínsecamente comprobables, lo que permite a los equipos generar confianza a través de pruebas unitarias enfocadas en lugar de pruebas de extremo a extremo lentas y frágiles. Los equipos pueden adoptar estos patrones de forma selectiva, pero comprender los beneficios para las pruebas ayuda a fundamentar estas decisiones arquitectónicas.

#### Las pruebas como retroalimentación arquitectónica

Las pruebas no son más que clientes de nuestro código. Si encontramos que nuestras pruebas son difíciles de escribir o requieren una configuración compleja, esto a menudo indica que nuestro código de producción necesita mejoras. Del mismo modo que la Regla de Dependencia guía la organización de nuestro código de producción, de manera similar informa un diseño de pruebas eficaz. Cuando las pruebas se vuelven incómodas o frágiles, esto frecuentemente indica que hemos violado los límites arquitectónicos o mezclado responsabilidades que deberían permanecer separadas.

Este bucle de retroalimentación arquitectónica es uno de los beneficios de prueba más valiosos de Clean Architecture. Los límites e interfaces explícitos se alinean de forma natural con varios enfoques de prueba, incluido el desarrollo guiado por pruebas (**Test-Driven Development - TDD**). Ya sea que escribas las pruebas primero o después de la implementación, las capas de Clean Architecture nos guían hacia mejores diseños: si escribir una prueba resulta incómodo, a menudo revela la necesidad de un límite arquitectónico. Si la configuración de la prueba se vuelve compleja, sugiere que hemos acoplado responsabilidades que deberían permanecer separadas. Estas señales sirven como advertencias tempranas, ayudándonos a identificar y corregir violaciones arquitectónicas antes de que queden profundamente arraigadas en nuestra base de código.

Para los equipos que dudan en adoptar pruebas unitarias exhaustivas debido a la complejidad de la configuración o a la falta de claridad en los límites, Clean Architecture proporciona un camino claro hacia adelante. Cada capa define interfaces y dependencias explícitas, proporcionando una guía clara sobre qué debe probarse y cómo mantener el aislamiento. A lo largo del resto de este capítulo, demostraremos estos beneficios implementando pruebas enfocadas para cada capa arquitectónica de nuestro sistema de gestión de tareas, mostrando cómo los límites de Clean Architecture nos guían naturalmente hacia suites de pruebas mantenibles.

#### De la complejidad en las pruebas a límites claros

Muchos desarrolladores tienen dificultades para probar bases de código que carecen de límites arquitectónicos claros. En sistemas donde la lógica de negocio, la persistencia y las responsabilidades de presentación están fuertemente acopladas, incluso las pruebas simples se convierten en desafíos técnicos complejos. Consideremos una entidad de tarea que se conecta directamente a bases de datos y envía notificaciones en su creación. Probar sus propiedades básicas requiere configurar y gestionar estas dependencias externas. Este acoplamiento de responsabilidades hace que las pruebas sean lentas, frágiles y difíciles de mantener. Los equipos responden con frecuencia minimizando las pruebas unitarias y de integración en favor de pruebas de extremo a extremo, las cuales, aunque valiosas, no pueden proporcionar la retroalimentación rápida necesaria durante el desarrollo.

Clean Architecture transforma este panorama al establecer límites claros entre componentes. En lugar de pruebas que deban coordinar múltiples responsabilidades entrelazadas, podemos enfocarnos en responsabilidades específicas:

- Las entidades de dominio y las reglas de negocio pueden probarse de forma aislada.
- La orquestación de casos de uso se puede verificar a través de interfaces explícitas.
- Las responsabilidades de infraestructura permanecen claramente separadas en los límites del sistema.

La estructura en capas mejora los flujos de trabajo de desarrollo en la práctica. Cada límite arquitectónico proporciona una guía natural para:

- Aislar errores en componentes o interacciones específicas.
- Agregar pruebas enfocadas que capturen casos extremos (*edge cases*).
- Construir una cobertura exhaustiva de forma incremental.

Esta claridad mejora drásticamente los flujos de trabajo de desarrollo. Cuando se reportan errores, esta organización por capas nos guía directamente al ámbito de prueba adecuado. Los problemas de lógica de dominio pueden reproducirse en pruebas unitarias, mientras que los problemas de integración tienen límites claros para examinar. Esta organización natural significa que nuestra cobertura de pruebas mejora de manera orgánica a medida que mantenemos y depuramos nuestro sistema. Cada problema resuelto conduce a pruebas enfocadas que verifican comportamientos específicos, construyendo gradualmente una suite de pruebas integral que detecta casos límite antes de que lleguen a producción.

En las siguientes secciones, exploraremos implementaciones concretas de estos patrones de prueba en nuestro sistema de gestión de tareas. Verás cómo los límites de Clean Architecture hacen que cada tipo de prueba sea más enfocado y mantenible, comenzando con pruebas unitarias de nuestra capa de Dominio y avanzando a través de pruebas de integración de nuestras interfaces externas.

---

### Sección 8.3: Pruebas de componentes limpios: pruebas unitarias en la práctica

Veamos cómo Clean Architecture transforma las pruebas unitarias de la teoría a la práctica. Consideremos un objetivo de prueba simple: verificar que las nuevas tareas tengan como valor predeterminado una prioridad media. En una base de código no alineada con el paradigma de Clean Architecture, muchos desarrolladores se han encontrado con clases como esta, donde incluso la lógica de dominio simple se entrelaza con la infraestructura:

```python
class Task(Entity):
    """Anti-pattern: Domain entity with direct infrastructure dependencies."""

    def __init__(self, title: str, description: str):
        self.title = title
        self.description = description
        # Direct database dependency:
        self.db = Database()
        # Direct notification dependency:
        self.notifier = NotificationService()
        self.priority = Priority.MEDIUM
        # Save to database and notify on creation
        self.id = self.db.save_task(self.as_dict())
        self.notifier(f"Task {self.id} created")
```

Este código estrechamente acoplado nos obliga a una configuración compleja para probar una regla de negocio simple concerniente a nuestra entidad `Task`:

```python
def test_new_task_priority_antipattern():
    """An anti-pattern mixing infrastructure concerns with simple domain logic."""
    # Complex setup just to test a default value
    db_connection = create_database_connection()
    notification_service = create_notification_service()
    # Just creating a task hits the database and notification service
    task = Task(
        title="Test task",
        description="Test description"
    )
    # Even checking a simple property requires a database query
    saved_task = task.db.get_task(task.id)
    assert saved_task['priority'] == Priority.MEDIUM
```

Esta prueba, aunque funcional, exhibe varios problemas comunes. Requiere una configuración compleja que involucra bases de datos y servicios solo para verificar una regla de dominio simple. Cuando falla, la causa podría ser cualquiera:

- ¿Hubo un problema de conexión con la base de datos?
- ¿Falló la inicialización del servicio de notificaciones?
- ¿O hubo realmente un problema con nuestra lógica de asignación predeterminada de prioridad?

Este nivel de complejidad al probar incluso propiedades básicas resalta por qué muchos desarrolladores perciben las pruebas como algo engorroso y que a menudo no vale la pena el esfuerzo.

Los límites de Clean Architecture eliminan estos problemas al mantener nuestra lógica de dominio pura y enfocada. Para el código que sigue un enfoque de Clean Architecture, podemos probar esta misma regla de negocio con notable claridad:

```python
from dataclasses import dataclass
from uuid import UUID


@dataclass
class Task:
    """Clean Architecture: Pure domain entity."""

    title: str
    description: str
    project_id: UUID
    priority: Priority = Priority.MEDIUM


def test_new_task_priority():
    """Clean test focused purely on domain logic."""
    task = Task(
        title="Test task",
        description="Test description",
        project_id=UUID('12345678-1234-5678-1234-567812345678')
    )
    assert task.priority == Priority.MEDIUM
```

La diferencia es sorprendente. Al mantener nuestras entidades de dominio enfocadas en las reglas de negocio:

- Nuestra prueba verifica exactamente una cosa: las nuevas tareas tienen por defecto prioridad media.
- La configuración requiere únicamente los datos necesarios para nuestra prueba.
- Si la prueba falla, existe exactamente una causa posible.
- La prueba se ejecuta instantáneamente sin dependencias externas.

Esta clara separación de responsabilidades demuestra uno de los beneficios clave de prueba de Clean Architecture: la capacidad de verificar reglas de negocio con una configuración mínima y máxima claridad. Los límites de Clean Architecture crean una progresión natural para construir una cobertura de pruebas exhaustiva. A lo largo de esta sección, implementaremos pruebas enfocadas y mantenibles que verifican el comportamiento respetando estos límites arquitectónicos. Comenzaremos con el caso más simple de probar entidades de dominio y trabajaremos progresivamente hacia el exterior a través de nuestras capas arquitectónicas.

#### Pruebas de entidades de dominio

Antes de sumergirnos en pruebas específicas, establezcamos un patrón que nos servirá a lo largo de todo nuestro viaje de pruebas. El patrón **Arrange-Act-Assert (AAA)**, propuesto originalmente por Bill Wake ([https://xp123.com/3a-arrange-act-assert/](https://xp123.com/3a-arrange-act-assert/)), proporciona una estructura clara para organizar pruebas que se alinea de forma natural con los límites de Clean Architecture:

- **Arrange (Preparar)**: configurar las condiciones y los datos de prueba.
- **Act (Actuar)**: ejecutar el comportamiento que se está probando.
- **Assert (Afirmar / Verificar)**: verificar los resultados esperados.

Este patrón se vuelve particularmente elegante al probar entidades de dominio porque Clean Architecture aísla nuestra lógica de negocio central de las preocupaciones externas. Consideremos cómo probamos el comportamiento de finalización de nuestra entidad `Task`:

```python
from datetime import datetime, timedelta
from uuid import UUID


def test_task_completion_captures_completion_time():
    """Test that completing a task records the completion timestamp."""
    # Arrange
    task = Task(
        title="Test task",
        description="Test description",
        project_id=UUID('12345678-1234-5678-1234-567812345678'),
    )

    # Act
    task.complete()

    # Assert
    assert task.completed_at is not None
    assert (datetime.now() - task.completed_at) < timedelta(seconds=1)
```

Esta prueba demuestra la esencia de las pruebas de entidades de dominio en Clean Architecture. Todo lo que necesitamos hacer es:

1. Configurar un estado inicial (una nueva tarea con los atributos requeridos).
2. Realizar una acción (completar la tarea).
3. Verificar el estado final (se registró la fecha y hora de finalización).

La claridad de la prueba de dominio proviene de la separación de responsabilidades de Clean Architecture. No necesitamos:

- Configurar ni gestionar conexiones a bases de datos.
- Configurar servicios de notificación.
- Gestionar autenticación o autorización.
- Administrar el estado del sistema externo.

Estamos probando lógica de negocio pura: cuando se completa una tarea, debe registrarse cuándo ocurrió. Este enfoque hace que nuestras pruebas sean rápidas, confiables y legibles. Si la prueba falla, solo hay una causa posible: nuestra lógica de finalización no está funcionando correctamente.

Este enfoque en reglas de negocio puras es uno de los beneficios clave que Clean Architecture aporta a las pruebas. Al aislar nuestra lógica de dominio de las preocupaciones de infraestructura, podemos verificar el comportamiento con pruebas simples y enfocadas que sirven como documentación viva de nuestras reglas de negocio. A continuación, veremos cómo esta claridad de prueba continúa a medida que avanzamos hacia afuera desde la capa interna de Dominio.

#### Herramientas para dobles de prueba en Python

Antes de trabajar con nuestras pruebas de casos de uso, comprendamos cómo Python nos ayuda a crear dobles de prueba (*test doubles*), los cuales actúan como sustitutos de dependencias para el componente bajo prueba. Al probar código que tiene dependencias, a menudo necesitamos una forma de reemplazar las implementaciones reales (como bases de datos o servicios externos) con versiones simuladas que podamos controlar. La biblioteca `unittest.mock` de Python, que se integra perfectamente con pytest, proporciona herramientas poderosas para crear estos dobles de prueba:

```python
from unittest.mock import Mock

# Create a mock object that records calls and can return preset values
mock_repo = Mock()
# Configure the response we want
mock_repo.get.return_value = some_task
# Call will return some_task
mock_repo.get(123)
# Verify the call happened exactly once
mock_repo.get.assert_called_once()

# Mocks track all interaction details
# Shows what arguments were passed
print(mock_repo.get.call_args)
# Shows how many times it was called
print(mock_repo.get.call_count)
```

Estos *mocks* sirven para dos propósitos clave en las pruebas:

1. Nos permiten controlar el comportamiento de las dependencias (como garantizar que un repositorio siempre devuelva una tarea específica).
2. Nos permiten verificar cómo interactúa nuestro código con esas dependencias (como asegurarnos de haber llamado a `save()` exactamente una vez).

#### Pruebas de orquestación de casos de uso

A medida que nos desplazamos hacia el exterior desde la capa de Dominio, nos encontramos de forma natural con dependencias de otros componentes de nuestro sistema. Un caso de uso de finalización de tareas, por ejemplo, necesita tanto un repositorio para persistir los cambios como un servicio de notificación para alertar a los interesados. Sin embargo, el énfasis de Clean Architecture en la abstracción a través de interfaces transforma estas dependencias de posibles dolores de cabeza para las pruebas en detalles de implementación directos.

Así como estas abstracciones nos permiten cambiar la implementación de un repositorio de almacenamiento basado en archivos a SQLite sin modificar ningún código dependiente, nos permiten reemplazar implementaciones reales con dobles de prueba durante las pruebas. Nuestros casos de uso dependen de interfaces abstractas como `TaskRepository` y `NotificationPort`, no de implementaciones concretas. Esto significa que podemos proporcionar implementaciones simuladas (*mocks*) para pruebas sin modificar en absoluto el código de nuestro caso de uso. El caso de uso no sabe ni le importa si está trabajando con un repositorio SQLite real o con un doble de prueba.

Examinemos cómo utilizamos mocks para probar nuestro caso de uso de forma aislada:

```python
def test_successful_task_completion():
    """Test task completion using mock dependencies."""
    # Arrange
    task = Task(
        title="Test task",
        description="Test description",
        project_id=UUID('12345678-1234-5678-1234-567812345678'),
    )
    task_repo = Mock()
    task_repo.get.return_value = task
    notification_service = Mock()
    use_case = CompleteTaskUseCase(
        task_repository=task_repo,
        notification_service=notification_service
    )
    request = CompleteTaskRequest(task_id=str(task.id))
```

La fase *Arrange* demuestra el aislamiento adecuado de las pruebas unitarias. Simulamos tanto el repositorio como el servicio de notificaciones para garantizar que estamos probando la lógica de orquestación del caso de uso de forma aislada. Esta configuración garantiza que nuestra prueba no se verá afectada por problemas de bases de datos, problemas de red u otros factores externos.

El flujo de prueba verifica las responsabilidades de orquestación de nuestro caso de uso a través de distintas verificaciones de mocks:

```python
    # Act
    result = use_case.execute(request)

    # Assert
    assert result.is_success
    task_repo.save.assert_called_once_with(task)
    notification_service.notify_task_completed.assert_called_once_with(task)
```

Observa cómo las aserciones de la prueba se centran en la orquestación en lugar de en la lógica de negocio. Verificamos que nuestro caso de uso coordine la secuencia correcta de operaciones mientras dejamos los detalles de implementación de esas operaciones a nuestros dobles de prueba. Este patrón escala de forma natural a medida que nuestros casos de uso se vuelven más sofisticados. Ya sea coordinando múltiples repositorios, gestionando notificaciones o administrando transacciones, las interfaces explícitas de Clean Architecture nos permiten verificar flujos de trabajo complejos a través de pruebas enfocadas.

En la siguiente sección, veremos cómo las pruebas de adaptadores de interfaz introducen nuevos patrones para verificar transformaciones de datos en los límites de nuestro sistema.

#### Pruebas de adaptadores de interfaz

A medida que avanzamos hacia la capa de Adaptadores de Interfaz, nuestro enfoque de prueba cambia hacia la verificación de la traducción adecuada entre los formatos externos y el núcleo de nuestra aplicación. Los controladores y presentadores sirven como estos traductores y, al igual que con nuestras pruebas unitarias en capas anteriores, queremos simular cualquier elemento externo a esta capa. No queremos que las conexiones a bases de datos, los sistemas de archivos o incluso las implementaciones de casos de uso afecten a nuestras pruebas de la lógica de traducción. Las interfaces explícitas de Clean Architecture hacen que esto sea sencillo. Podemos simular nuestros casos de uso y centrarnos puramente en verificar que nuestros adaptadores transformen los datos adecuadamente a medida que cruzan los límites de nuestro sistema.

Examinemos cómo probamos la responsabilidad de un controlador de convertir identificadores externos de tipo string a los UUID que espera nuestro dominio. Cuando los clientes web o CLI llaman a nuestro sistema, normalmente proporcionan los ID como cadenas de texto (*strings*). Nuestro dominio, sin embargo, trabaja con UUID internamente. El controlador debe gestionar esta traducción:

```python
def test_controller_converts_string_id_to_uuid():
    """Test that controller properly converts string IDs to UUIDs for use cases."""
    # Arrange
    task_id = "123e4567-e89b-12d3-a456-426614174000"
    complete_use_case = Mock()
    complete_use_case.execute.return_value = Result.success(
        TaskResponse.from_entity(
            Task(
                title="Test Task",
                description="Test Description",
                project_id=UUID('12345678-1234-5678-1234-567812345678')
            )
        )
    )
    presenter = Mock(spec=TaskPresenter)
    controller = TaskController(
        complete_use_case=complete_use_case,
        presenter=presenter,
    )
```

La fase *Arrange* establece nuestro escenario de prueba. Proporcionamos un ID de tarea como string (tal como lo haría un cliente) y creamos un caso de uso simulado que está configurado para devolver un resultado exitoso. Al crear nuestro mock del presentador, usamos `spec=TaskPresenter` para crear un mock estricto que conoce la interfaz de nuestro presentador:

```python
# Without spec, any method can be called
loose_mock = Mock()
loose_mock.non_existent_method()  # Works, but could hide bugs

# With spec, mock enforces the interface
strict_mock = Mock(spec=TaskPresenter)
strict_mock.non_existent_method()  # Raises AttributeError
```

Esta seguridad de tipos adicional es particularmente valiosa en la capa de Adaptadores de Interfaz, donde mantener los límites de interfaz correctos es crucial. Al usar `spec`, nos aseguramos de que nuestras pruebas detecten no solo problemas de comportamiento, sino también violaciones de contrato.

Con nuestros dobles de prueba debidamente configurados para hacer cumplir los límites de interfaz, podemos verificar la lógica de traducción de nuestro controlador:

```python
    # Act
    controller.handle_complete(task_id=task_id)

    # Assert
    complete_use_case.execute.assert_called_once()
    called_request = complete_use_case.execute.call_args[0][0]
    assert isinstance(called_request.task_id, UUID)
```

Cuando llamamos a `handle_complete`, el controlador debe:

1. Tomar el ID de tarea en formato string del cliente.
2. Convertirlo a un UUID.
3. Crear una solicitud debidamente formateada para el caso de uso.
4. Pasar esta solicitud al método `execute` del caso de uso.

Nuestras aserciones verifican este flujo:

- Confirmando que el caso de uso se llamó exactamente una vez.
- Extrayendo la solicitud que se pasó al caso de uso.
- Verificando que el `task_id` en esa solicitud ahora sea un UUID, no un string.

Esta prueba garantiza que el controlador cumpla su responsabilidad central: traducir formatos de datos externos a los tipos que nuestro dominio espera. Si el controlador fallara en convertir el identificador de string a un UUID, la prueba fallaría al verificar el tipo de `called_request.task_id`.

De manera similar, podemos probar presentadores para garantizar que formateen los datos de dominio adecuadamente para su consumo externo. Centrémonos en una responsabilidad específica: formatear las fechas de finalización de tareas en cadenas legibles por humanos para la CLI. Esta transformación aparentemente simple es un ejemplo perfecto del rol de un adaptador de interfaz:

```python
def test_presenter_formats_completion_date():
    """Test that presenter formats dates according to interface requirements."""
    # Arrange
    completion_time = datetime(2024, 1, 15, 14, 30, tzinfo=timezone.utc)
    task = Task(
        title="Test Task",
        description="Test Description",
        project_id=UUID('12345678-1234-5678-1234-567812345678')
    )
    task.complete()
    # Override completion time for deterministic testing
    task.completed_at = completion_time
    task_response = TaskResponse.from_entity(task)
    presenter = CliTaskPresenter()
```

Esta prueba demuestra cómo el enfoque por capas de Clean Architecture simplifica las pruebas. Debido a que nuestras entidades de dominio no tienen dependencias externas, podemos crearlas y manipularlas fácilmente en nuestras pruebas. No tenemos que preocuparnos por cómo se estableció el tiempo de finalización en la práctica. Las reglas de negocio intrínsecas a la entidad `Task` evitarán estados ilegales (como establecer la hora de finalización en una tarea no completada). Este aislamiento hace que nuestras pruebas de presentadores sean sencillas y confiables.

```python
    # Act
    view_model = presenter.present_task(task_response)

    # Assert
    expected_format = "2024-01-15 14:30"
    assert view_model.completion_info is not None and expected_format in view_model.completion_info
```

Este flujo de prueba demuestra cómo los límites explícitos de Clean Architecture simplifican las pruebas de adaptadores de interfaz. Nos centramos puramente en verificar el formateo de datos sin entrelazar la persistencia, las reglas de negocio u otras preocupaciones que nuestras pruebas unitarias ya han verificado. Cada adaptador tiene una única y clara responsabilidad que podemos probar de forma aislada.

Si bien probar aspectos de formato individuales es valioso, nuestros presentadores a menudo necesitan gestionar múltiples aspectos de visualización simultáneamente. Veamos cómo la separación de responsabilidades de Clean Architecture nos ayuda a probar la creación integral del modelo de vista (*view model*) de una manera clara y metódica:

```python
def test_presenter_provides_complete_view_model():
    """Test presenter creates properly formatted view model with all display fields."""
    # Arrange
    task = Task(
        title="Important Task",
        description="Testing view model creation",
        project_id=UUID('12345678-1234-5678-1234-567812345678'),
        priority=Priority.HIGH
    )
    task.complete()  # Set status to DONE
    task_response = TaskResponse.from_entity(task)
    presenter = CliTaskPresenter()

    # Act
    view_model = presenter.present_task(task_response)

    # Assert
    assert view_model.title == "Important Task"
    assert view_model.status_display == "[DONE]"
    assert view_model.priority_display == "HIGH PRIORITY"
    assert isinstance(view_model.completion_info, str)
```

Esta prueba verifica cómo nuestro presentador transforma múltiples aspectos del estado del dominio en formatos aptos para su visualización. La separación de responsabilidades de Clean Architecture significa que podemos verificar toda nuestra lógica de presentación (indicadores de estado, formato de prioridad e información de finalización) sin entrelazar reglas de negocio ni preocupaciones de infraestructura.

Con estos patrones establecidos para probar capas individuales, ahora podemos explorar cómo Clean Architecture nos ayuda a probar interacciones a través de los límites arquitectónicos.

---

### Sección 8.4: Pruebas a través de los límites arquitectónicos

Dado que nuestras pruebas unitarias verifican minuciosamente las reglas de negocio y la lógica de orquestación a través de interfaces explícitas, nuestras pruebas de integración pueden ser altamente estratégicas. Donde nuestras pruebas unitarias usaban mocks para verificar el comportamiento de los componentes de forma aislada, estas pruebas de integración confirman que nuestras implementaciones concretas funcionan correctamente juntas. En lugar de probar exhaustivamente cada combinación de componentes, nos centramos en los cruces de límites clave, particularmente aquellos que involucran infraestructura como la persistencia o los servicios externos.

Consideremos cómo esto cambia nuestro enfoque de prueba. En nuestras pruebas unitarias, simulamos repositorios para verificar que los casos de uso coordinaran correctamente la creación de tareas y la asignación de proyectos. Ahora probaremos que nuestras implementaciones reales `FileTaskRepository` y `FileProjectRepository` mantengan estas relaciones al persistir en el disco.

Examinemos cómo probar nuestro límite de persistencia en el sistema de archivos, una de las áreas donde las pruebas de integración aportan valor más allá de la cobertura de nuestras pruebas unitarias:

```python
@pytest.fixture
def repository(tmp_path):  # tmp_path is a pytest builtin for temp dirs
    """Create repository using temporary directory."""
    return FileTaskRepository(data_dir=tmp_path)


def test_repo_handles_project_task_relationships(tmp_path):
    # Arrange
    task_repo = FileTaskRepository(tmp_path)
    project_repo = FileProjectRepository(tmp_path)
    project_repo.set_task_repository(task_repo)

    # Create project and tasks through the repository
    project = Project(name="Test Project", description="Testing relationships")
    project_repo.save(project)

    task = Task(
        title="Test Task",
        description="Testing relationships",
        project_id=project.id
    )
    task_repo.save(task)
```

Esta configuración de prueba demuestra un punto de integración clave donde estamos creando repositorios reales que se coordinan a través del almacenamiento en el sistema de archivos. Nuestras pruebas unitarias ya verificaron las reglas de negocio mediante mocks, por lo que esta prueba se centra puramente en verificar que nuestra capa de Infraestructura mantenga estas relaciones correctamente.

```python
    # Act - Load project with its tasks
    loaded_project = project_repo.get(project.id)

    # Assert
    assert len(loaded_project.tasks) == 1
    assert loaded_project.tasks[0].title == "Test Task"
```

La prueba verifica comportamientos que no pudimos capturar en nuestras pruebas unitarias:

- Los proyectos pueden cargar sus tareas asociadas desde el disco.
- Las relaciones tarea–proyecto sobreviven a la serialización.

Esta coordinación de repositorios se vuelve particularmente importante cuando se trata de garantías arquitectónicas que abarcan múltiples operaciones. Una de esas garantías es nuestro proyecto buzón de entrada (*inbox*), que es una decisión clave a nivel de infraestructura tomada en el [Capítulo 7](https://subscription.packtpub.com/book/programming/9781836642893/7) para garantizar que todas las tareas tengan un hogar organizativo.

Otro punto de integración crucial es verificar que nuestras implementaciones de `ProjectRepository` mantengan esta garantía del buzón de entrada (*inbox*). Mientras que nuestras pruebas unitarias verificaron las reglas de negocio en torno al uso de la bandeja de entrada (como evitar su eliminación o finalización), nuestras pruebas de integración deben verificar que la capa de Infraestructura mantenga adecuadamente la existencia de este proyecto especial:

```python
def test_repository_automatically_creates_inbox(tmp_path):
    """Test that project repository maintains inbox project across instantiations."""
    # Arrange - Create initial repository and verify Inbox exists
    initial_repo = FileProjectRepository(tmp_path)
    initial_inbox = initial_repo.get_inbox()
    assert initial_inbox.name == "INBOX"
    assert initial_inbox.project_type == ProjectType.INBOX

    # Act - Create new repository instance pointing to same directory
    new_repo = FileProjectRepository(tmp_path)

    # Assert - New instance maintains same Inbox
    persisted_inbox = new_repo.get_inbox()
    assert persisted_inbox.id == initial_inbox.id
    assert persisted_inbox.project_type == ProjectType.INBOX
```

Esta prueba verifica un comportamiento que nuestras pruebas unitarias no pudieron capturar porque utilizaban repositorios simulados. Nuestra implementación de repositorio concreto asume la responsabilidad de la inicialización y persistencia de la bandeja de entrada. Al crear dos instancias de repositorio independientes que apuntan al mismo directorio de datos, confirmamos que:

- El repositorio crea automáticamente la bandeja de entrada (*inbox*) en su primer uso.
- La naturaleza especial de la bandeja de entrada (su tipo y su ID) persiste correctamente.
- Las instancias de repositorio posteriores reconocen y mantienen este mismo buzón de entrada.

Esta prueba de integración enfocada verifica una garantía arquitectónica fundamental que habilita nuestros patrones de organización de tareas. En lugar de probar cada operación posible de la bandeja de entrada, verificamos el comportamiento central de la infraestructura que hace posibles estas operaciones.

Habiendo verificado nuestras implementaciones de repositorios y garantías de infraestructura, examinemos cómo Clean Architecture permite pruebas de integración enfocadas a nivel de casos de uso. Consideremos nuestro caso de uso de creación de tareas. Si bien nuestras pruebas unitarias verificaron su lógica de negocio utilizando repositorios simulados, debemos confirmar que funciona correctamente con la persistencia real. Los límites explícitos de Clean Architecture nos permiten hacer esto de manera estratégica, probando la persistencia real mientras continuamos simulando preocupaciones no relacionadas con la persistencia, tales como las notificaciones:

```python
def test_task_creation_with_persistence(tmp_path):
    """Test task creation use case with real persistence."""
    # Arrange
    task_repo = FileTaskRepository(tmp_path)
    project_repo = FileProjectRepository(tmp_path)
    project_repo.set_task_repository(task_repo)

    use_case = CreateTaskUseCase(
        task_repository=task_repo,
        project_repository=project_repo,
        notification_service=Mock()  # Still mock non-persistence concerns
    )
```

En esta configuración de prueba, utilizamos repositorios reales para verificar el comportamiento de persistencia mientras simulamos las notificaciones, ya que no son relevantes para esta prueba de integración.

```python
    # Act
    result = use_case.execute(CreateTaskRequest(
        title="Test Task",
        description="Integration test"
    ))

    # Assert - Task was persisted
    assert result.is_success
    created_task = task_repo.get(UUID(result.value.id))
    assert created_task.project_id == project_repo.get_inbox().id
```

Esta prueba verifica que nuestro caso de uso orquesta correctamente la creación de tareas con persistencia real:

- La tarea se guarda correctamente en el disco.
- La tarea se asigna al buzón de entrada (*Inbox*) como se esperaba.
- Podemos recuperar la tarea persistida a través del repositorio.

Al mantener las notificaciones simuladas, conservamos el enfoque de la prueba mientras verificamos el comportamiento crítico de persistencia. Este enfoque estratégico para las pruebas de integración, que implica probar implementaciones reales de límites específicos mientras se simulan otros, demuestra cómo Clean Architecture nos ayuda a crear una cobertura de pruebas exhaustiva sin una complejidad innecesaria.

Estas pruebas de integración demuestran cómo los límites explícitos de Clean Architecture permiten realizar pruebas enfocadas y efectivas sobre responsabilidades que involucran múltiples componentes. En lugar de depender de pruebas de extremo a extremo que tocan todos los componentes del sistema, podemos probar estratégicamente límites específicos verificando la coordinación de repositorios, las garantías a nivel de infraestructura y la persistencia de casos de uso mientras se simulan las preocupaciones accesorias.

Al implementar pruebas de integración en tus propios sistemas basados en Clean Architecture:

- Deja que los límites arquitectónicos guíen lo que necesita pruebas de integración.
- Prueba implementaciones reales únicamente para el límite que se está verificando.
- Confía en la cobertura de tus pruebas unitarias sobre las reglas de negocio.
- Mantén cada prueba enfocada en una preocupación de integración específica.

En la siguiente sección, exploraremos patrones de prueba que ayudan a mantener la claridad de las pruebas a medida que los sistemas se vuelven más complejos.

---

### Sección 8.5: Herramientas y patrones para el mantenimiento de pruebas

Si bien los límites de Clean Architecture nos ayudan a escribir pruebas enfocadas, mantener una suite de pruebas completa presenta sus propios desafíos. A medida que nuestro sistema de gestión de tareas crece, también lo hacen nuestras pruebas. Las nuevas reglas de negocio requieren casos de prueba adicionales, los cambios en la infraestructura necesitan una verificación actualizada y modificaciones simples pueden afectar a múltiples archivos de prueba. Sin una organización cuidadosa, corremos el riesgo de pasar más tiempo gestionando pruebas que mejorando nuestro sistema.

Cuando una prueba falla, necesitamos entender rápidamente qué límite arquitectónico fue violado. Cuando las reglas de negocio cambian, deberíamos poder actualizar las pruebas sistemáticamente en lugar de tener que buscar en múltiples archivos. Al agregar nuevos casos de prueba, queremos aprovechar la infraestructura de prueba existente en lugar de tener que duplicar el código de configuración.

El ecosistema de pruebas de Python, particularmente pytest, proporciona herramientas poderosas que se alinean de forma natural con los objetivos de Clean Architecture. Exploraremos cómo:

- Verificar múltiples escenarios manteniendo el código de prueba limpio y enfocado.
- Organizar los *fixtures* de prueba para respetar los límites arquitectónicos.
- Aprovechar herramientas de prueba que facilitan el mantenimiento.
- Detectar problemas sutiles que podrían violar nuestra integridad arquitectónica.

A través de ejemplos prácticos, veremos cómo estos patrones nos ayudan a mantener una cobertura de pruebas exhaustiva sin crear una carga de mantenimiento, permitiéndonos verificar más escenarios con menos código mientras mantenemos nuestras pruebas tan limpias como nuestra arquitectura.

#### Estructuración de archivos de prueba

Los límites explícitos de Clean Architecture proporcionan una organización natural para nuestros archivos de prueba. Ya sea que tu equipo elija organizar las pruebas por tipo (unitarias/integración) o las mantenga juntas, la estructura interna debe reflejar la arquitectura de tu aplicación. Una estructura de directorio de pruebas de ejemplo podría parecerse a la siguiente:

```text
tests/
    domain/
        entities/
            test_task.py
            test_project.py
        value_objects/
            test_deadline.py
    application/
        use_cases/
            test_task_use_cases.py
    # ... Remaining tests by layer
```

Esta organización refuerza las reglas de dependencia de Clean Architecture a través de los límites del sistema de archivos. Las pruebas en `tests/domain` no deberían necesitar importar nada de `application` o `interfaces`, mientras que una prueba en `tests/interfaces` puede trabajar con componentes de todas las capas, tal como lo hacen sus contrapartes de producción. Esta alineación estructural también proporciona una advertencia temprana sobre posibles violaciones arquitectónicas. Si nos encontramos deseando importar un repositorio en una prueba de entidad de dominio, la ruta de importación incómoda nos señala que probablemente estamos violando la Regla de Dependencia de Clean Architecture.

#### Pruebas parametrizadas para una cobertura exhaustiva

Al realizar pruebas a través de límites arquitectónicos, a menudo necesitamos verificar comportamientos similares bajo diferentes condiciones. Consideremos nuestro caso de uso de creación de tareas. Necesitamos probar la asignación de proyectos, el establecimiento de prioridades y la validación de fechas límite a través de múltiples combinaciones de entrada. Escribir métodos de prueba independientes para cada escenario conduce a código duplicado y a un mantenimiento más difícil. Cuando las reglas de negocio cambian, necesitamos actualizar múltiples pruebas en lugar de una única fuente de verdad.

El decorador `parametrize` de pytest transforma la forma en que manejamos estos escenarios. En lugar de duplicar el código de prueba, podemos definir variaciones de datos que ejercitan nuestros límites arquitectónicos:

```python
@pytest.mark.parametrize(
    "request_data,expected_behavior",
    [
        # Basic task creation - defaults to INBOX project
        (
            {
                "title": "Test Task",
                "description": "Basic creation"
            },
            {
                "project_type": ProjectType.INBOX,
                "priority": Priority.MEDIUM
            }
        ),
        # Explicit project assignment
        (
            {
                "title": "Project Task",
                "description": "With project",
                "project_id": "project-uuid"
            },
            {
                "project_type": ProjectType.REGULAR,
                "priority": Priority.MEDIUM
            }
        ),
        # High priority task
        # ... data for task
    ],
    ids=["basic-task", "project-task", "priority-task"]
)
```

Luego, en el método de prueba que sigue al decorador `parametrize` anterior, la prueba se ejecutará una vez para cada elemento en la lista de parámetros:

```python
def test_task_creation_scenarios(request_data, expected_behavior):
    """Test task creation use case handles various input scenarios correctly."""
    # Arrange
    task_repo = Mock(spec=TaskRepository)
    project_repo = FileProjectRepository(tmp_path)  # Real project repo for INBOX

    use_case = CreateTaskUseCase(
        task_repository=task_repo,
        project_repository=project_repo
    )

    # Act
    result = use_case.execute(CreateTaskRequest(**request_data))

    # Assert
    assert result.is_success
    created_task = result.value
    if expected_behavior["project_type"] == ProjectType.INBOX:
        assert UUID(created_task.project_id) == (
            project_repo.get_inbox().id
        )
    assert created_task.priority == expected_behavior["priority"]
```

Esta prueba demuestra varios beneficios clave de las pruebas parametrizadas. El decorador inyecta `request_data` y `expected_behavior` de cada caso de prueba en nuestro método de prueba, donde `request_data` representa la entrada en el borde de nuestro sistema y `expected_behavior` define nuestras reglas de dominio esperadas. Esta separación nos permite definir nuestros escenarios de prueba declarativamente mientras mantenemos la lógica de verificación limpia y enfocada.

El parámetro `ids` hace que los fallos en las pruebas sean más significativos: en lugar de que falle `test_task_creation_scenarios[0]`, vemos que falló `test_task_creation_scenarios[basic-task]`, resaltando inmediatamente qué escenario requiere atención.

Al usar pruebas parametrizadas, es una buena práctica agrupar escenarios relacionados y proporcionar identificadores de escenarios claros. Este enfoque mantiene nuestra lógica de prueba enfocada mientras varían los datos de prueba, lo que nos ayuda a mantener una cobertura completa sin sacrificar la claridad de las pruebas.

Habiendo organizado nuestros escenarios de prueba, exploremos cómo el sistema de fixtures de pytest nos ayuda a gestionar las dependencias de prueba a través de los límites arquitectónicos.

#### Organización de fixtures de prueba

A lo largo de nuestros ejemplos de prueba, hemos utilizado fixtures de pytest para gestionar dependencias de prueba, desde proporcionar entidades de tareas limpias hasta configurar repositorios simulados. Si bien estos fixtures individuales satisficieron nuestras necesidades de prueba inmediatas, a medida que los conjuntos de pruebas crecen, gestionar la configuración de pruebas a través de límites arquitectónicos se vuelve cada vez más complejo. Cada capa tiene sus propias necesidades de configuración: las pruebas de dominio requieren instancias de entidades limpias, las pruebas de casos de uso necesitan repositorios y servicios debidamente configurados, y las pruebas de interfaz necesitan datos de solicitud formateados.

El sistema de fixtures de pytest, particularmente en combinación con sus archivos `conftest.py`, nos ayuda a escalar este patrón de fixtures a lo largo de nuestra jerarquía de pruebas manteniendo los límites de Clean Architecture. Al colocar los fixtures en el directorio de pruebas correspondiente, nos aseguramos de que cada prueba tenga acceso exactamente a lo que necesita sin dependencias excesivas:

```python
# tests/conftest.py - Root fixtures available to all tests
@pytest.fixture
def sample_task_data():
    """Provide basic task attributes for testing."""
    return {
        "title": "Test Task",
        "description": "Sample task for testing",
        "project_id": UUID('12345678-1234-5678-1234-567812345678'),
    }


# tests/domain/conftest.py - Domain layer fixtures
@pytest.fixture
def domain_task(sample_task_data):
    """Provide a clean Task entity for domain tests."""
    return Task(**sample_task_data)


# tests/application/conftest.py - Application layer fixtures
@pytest.fixture
def mock_task_repository(domain_task):
    """Provide a pre-configured mock repository."""
    repo = Mock(spec=TaskRepository)
    repo.get.return_value = domain_task
    return repo
```

Esta organización aplica de forma natural la Regla de Dependencia de Clean Architecture a través de nuestra estructura de pruebas. Una prueba que necesita tanto entidades de dominio como repositorios debe residir en la capa de Aplicación o superior, ya que depende de los fixtures de ambas capas. De manera similar, una prueba que solo utiliza entidades de dominio puede tener la seguridad de que no depende accidentalmente de preocupaciones de infraestructura.

Los propios fixtures respetan nuestros límites arquitectónicos:

```python
# tests/interfaces/conftest.py - Interface layer fixtures
@pytest.fixture
def task_controller(mock_task_repository, mock_notification_port):
    """Provide a properly configured TaskController."""
    return TaskController(
        create_use_case=CreateTaskUseCase(
            task_repository=mock_task_repository,
            project_repository=Mock(spec=ProjectRepository),
            notification_service=mock_notification_port
        ),
        presenter=Mock(spec=TaskPresenter)
    )


@pytest.fixture
def task_request_json():
    """Provide sample request data as it would come from clients."""
    return {
        "title": "Test Task",
        "description": "Testing task creation",
        "priority": "HIGH"
    }
```

Al utilizar fixtures a través de los límites arquitectónicos, estructúralos de modo que coincidan con la inyección de dependencias de producción. Por ejemplo, para verificar que nuestro controlador transforma adecuadamente las solicitudes externas en operaciones de casos de uso:

```python
def test_controller_handles_task_creation(
    task_controller,
    task_request_json,
    mock_task_repository
):
    """Test task creation through controller layer."""
    result = task_controller.handle_create(**task_request_json)
    assert result.is_success
    mock_task_repository.save.assert_called_once()
```

Este enfoque basado en fixtures rinde frutos de varias maneras prácticas:

- **Las pruebas se centran en el comportamiento en lugar de en la configuración**: Nuestra prueba verifica la responsabilidad del controlador sin que el código de configuración abarrote el método de prueba.
- **Las configuraciones de prueba comunes son reutilizables**: El mismo fixture `task_controller` puede admitir múltiples escenarios de prueba de controladores.
- **Las dependencias son explícitas**: Los parámetros de la prueba muestran claramente con qué componentes estamos trabajando.
- **Los cambios en la inicialización de los componentes solo necesitan actualizarse en el fixture**, no en cada prueba individual.

A continuación, examinemos cómo se combinan estos patrones con herramientas de prueba para detectar violaciones arquitectónicas sutiles.

#### Herramientas y técnicas de prueba

Incluso con pruebas y fixtures bien organizados, ciertos escenarios de prueba presentan desafíos únicos. Algunas pruebas pueden pasar de forma aislada pero fallar debido a dependencias temporales o de estado ocultas, mientras que otras pueden enmascarar violaciones arquitectónicas que solo salen a la superficie bajo condiciones específicas. Exploremos algunas herramientas prácticas que ayudan a mantener la confiabilidad de las pruebas respetando nuestros límites arquitectónicos. Desde el control del tiempo en nuestras pruebas hasta la exposición de dependencias de estado ocultas y la gestión de la ejecución de suites de prueba a escala, estas herramientas nos ayudan a detectar sutiles violaciones arquitectónicas antes de que queden profundamente integradas en nuestro sistema.

##### Gestión del tiempo en las pruebas

Probar los cálculos de fechas límite (*deadlines*) o las notificaciones basadas en el tiempo requiere un manejo cuidadoso del tiempo. En nuestro sistema de gestión de tareas, tenemos varias funciones sensibles al tiempo: las tareas pueden vencerse, los plazos desencadenan notificaciones cuando se aproximan y las tareas completadas registran su hora de finalización. Probar estas funciones sin controlar el tiempo se vuelve problemático. Imaginemos probar que una tarea vence después de su fecha límite: tendríamos que esperar a que transcurra el tiempo real (haciendo que las pruebas sean lentas y poco confiables) o manipular la hora del sistema (lo que podría afectar a otras pruebas). Peor aún, las pruebas basadas en el tiempo podrían pasar o fallar según el momento del día en que se ejecuten.

La biblioteca `freezegun` resuelve estos problemas permitiéndonos controlar el tiempo en nuestras pruebas sin modificar nuestra lógica de dominio. Primero, instala la biblioteca:

```bash
pip install freezegun
```

La biblioteca `freezegun` proporciona un gestor de contexto que nos permite establecer un punto específico en el tiempo para el código que se ejecuta dentro de su ámbito. Cualquier código dentro del bloque `with freeze_time():` verá el tiempo como congelado en ese momento, mientras que el código exterior continúa con el tiempo normal. Esto nos permite crear escenarios de prueba precisos mientras nuestras entidades de dominio continúan trabajando con objetos `datetime` reales:

```python
from freezegun import freeze_time


def test_task_deadline_approaching():
    """Test deadline notifications respect time boundaries."""
    # Arrange
    with freeze_time("2024-01-14 12:00:00"):
        task = Task(
            title="Time-sensitive task",
            description="Testing deadlines",
            project_id=UUID('12345678-1234-5678-1234-567812345678'),
            due_date=Deadline(datetime(
                2024, 1, 15, 12, 0, tzinfo=timezone.utc
            ))
        )
    notification_service = Mock(spec=NotificationPort)
    use_case = CheckDeadlinesUseCase(
        task_repository=Mock(spec=TaskRepository),
        notification_service=notification_service,
        warning_threshold=timedelta(days=1)
    )
```

En esta disposición de prueba, congelamos el tiempo al mediodía del 14 de enero para crear nuestra tarea con una fecha de vencimiento 24 horas más tarde. Esto nos proporciona un estado inicial preciso para probar los cálculos de fechas límite. Nuestras entidades de dominio continúan trabajando con objetos `datetime` estándar, preservando la separación de responsabilidades de Clean Architecture. Solo se ve afectada la percepción del tiempo actual:

```python
    # Act
    with freeze_time("2024-01-14 13:00:00"):
        result = use_case.execute()

    # Assert
    assert result.is_success
    notification_service.notify_task_deadline_approaching.assert_called_once()
```

Avanzar el tiempo una hora nos permite verificar que nuestro sistema de notificación de fechas límite identifique correctamente las tareas que vencen dentro del umbral de advertencia. La prueba se ejecuta instantáneamente mientras simula un escenario del mundo real que de otro modo tardaría horas en validarse. Nuestras entidades y casos de uso no se enteran de que están operando en un tiempo simulado, manteniendo límites arquitectónicos limpios y permitiendo al mismo tiempo pruebas exhaustivas del comportamiento dependiente del tiempo.

Este patrón mantiene la lógica dependiente del tiempo dentro de nuestro dominio al tiempo que la hace comprobable. Nuestras entidades y casos de uso trabajan con objetos `datetime` reales, pero nuestras pruebas pueden verificar su comportamiento en puntos específicos en el tiempo.

##### Exposición de dependencias de estado

Las pruebas que dependen de estados ocultos o del orden de ejecución pueden enmascarar violaciones arquitectónicas, particularmente en torno al estado global. En Clean Architecture, cada componente debe ser autónomo, con las dependencias pasadas explícitamente a través de interfaces. Sin embargo, el estado global sutil puede infiltrarse. Consideremos el servicio de notificaciones de nuestro sistema de gestión de tareas: podría mantener una cola interna de notificaciones pendientes que se traspase entre pruebas. Una prueba que verifique las notificaciones de tareas de alta prioridad podría pasar cuando se ejecuta sola, pero fallar cuando se ejecuta después de una prueba que llena esta cola. O nuestro repositorio de proyectos podría almacenar en caché los recuentos de tareas por razones de rendimiento, lo que lleva a pruebas que pasan o fallan según si otras pruebas han manipulado esa caché.

Estas dependencias de estado ocultas no solo hacen que las pruebas sean poco confiables, sino que a menudo indican violaciones arquitectónicas donde los componentes mantienen un estado que debería ser explícito en nuestras interfaces. Lo mejor es exponer estos problemas lo antes posible, por lo que se recomienda encarecidamente adoptar la práctica de ejecutar las pruebas en orden aleatorio. Con pytest esto se puede lograr instalando primero `pytest-random-order`:

```bash
pip install pytest-random-order
```

Luego configúralo para que se ejecute en cada prueba:

```ini
# pytest.ini
[pytest]
addopts = --random-order
```

Cuando las pruebas se ejecutan en orden aleatorio, las dependencias de estado ocultas salen a la superficie rápidamente a través de fallos en las pruebas. En el momento en que una prueba depende del estado global o del orden de ejecución, fallará de forma impredecible. Esta es una señal clara de que debemos investigar nuestros límites arquitectónicos. Cuando ocurre tal fallo, el plugin proporciona un valor semilla (*seed*) que te permite reproducir el orden exacto de ejecución de la prueba:

```bash
pytest --random-order-seed=123456
```

Luego puedes ejecutar las pruebas en el orden especificado por la semilla tantas veces como sea necesario para determinar la causa raíz del fallo.

##### Aceleración de la ejecución de pruebas

A medida que crece tu catálogo de pruebas, el tiempo de ejecución puede convertirse en una preocupación importante. Lo que comenzó como una suite de pruebas rápida ahora tarda minutos en ejecutarse. En nuestro sistema de gestión de tareas, hemos construido una cobertura completa en todas las capas, incluyendo entidades de dominio, casos de uso, adaptadores de interfaz e infraestructura. Ejecutar todas estas pruebas secuencialmente, especialmente aquellas que involucran operaciones del sistema de archivos o comportamientos basados en el tiempo, puede generar retrasos notables en el bucle de retroalimentación del desarrollo.

La ejecución rápida de pruebas es crucial para mantener la integridad arquitectónica. Las suites de pruebas de larga duración desincentivan la verificación frecuente durante el desarrollo, aumentando el riesgo de que se pasen por alto violaciones arquitectónicas. `pytest-xdist` proporciona herramientas para paralelizar la ejecución de pruebas manteniendo la integridad de las mismas. Primero, instala el plugin con pip:

```bash
pip install pytest-xdist
```

Configura la ejecución en paralelo en tu archivo `pytest.ini`:

```ini
# pytest.ini
[pytest]
addopts = --random-order -n auto  # Combine random order with parallel execution
```

Para cualquier escenario donde las pruebas no puedan ejecutarse en un solo grupo paralelizado (por ejemplo, pruebas que comparten un estado o recursos globales conocidos), `pytest-xdist` proporciona varias herramientas:

- Usa `@pytest.mark.serial` para marcar las pruebas que deben ejecutarse secuencialmente.
- Configura el alcance de los recursos con `@pytest.mark.resource_group('global-cache')` para garantizar que las pruebas que utilizan los mismos recursos se ejecuten juntas.

El indicador `-n auto` utiliza automáticamente los núcleos de CPU disponibles, aunque puedes especificar un número exacto como `-n 4` si lo deseas. Este enfoque nos permite mantener una ejecución rápida de las pruebas respetando las restricciones de nuestros límites arquitectónicos. Las pruebas críticas que verifican nuestros principios de Clean Architecture se ejecutan con la rapidez suficiente para formar parte de cada ciclo de desarrollo, ayudando a detectar violaciones arquitectónicas a tiempo.

---

### Sección 8.6: Resumen

En este capítulo, exploramos cómo los principios de Clean Architecture se traducen directamente en prácticas de prueba efectivas. Aprendimos cómo los límites arquitectónicos guían de forma natural nuestra estrategia de pruebas, dejando claro qué probar y cómo estructurar esas pruebas. A través de nuestro sistema de gestión de tareas, vimos cómo Clean Architecture permite pruebas enfocadas sin una gran dependencia de pruebas de extremo a extremo, manteniendo nuestro sistema adaptable y sostenible.

Implementamos varios patrones de prueba clave que demuestran los beneficios de Clean Architecture:

- **Pruebas unitarias** que aprovechan los límites naturales de Clean Architecture para una verificación enfocada.
- **Pruebas de integración** que verifican el comportamiento a través de capas arquitectónicas específicas.
- **Herramientas y patrones** para construir suites de pruebas mantenibles a escala.

Más importante aún, vimos cómo la cuidadosa atención de Clean Architecture a las dependencias y las interfaces hace que nuestras pruebas sean más enfocadas y fáciles de mantener. Al organizar nuestras pruebas para respetar los límites arquitectónicos, desde la estructura de archivos hasta los fixtures, creamos conjuntos de pruebas que crecen elegantemente con nuestros sistemas.

En el [Capítulo 9](https://subscription.packtpub.com/book/programming/9781836642893/9), exploraremos cómo aplicar los principios de Clean Architecture al diseño de interfaces web, mostrando cómo nuestra cuidadosa atención a los límites arquitectónicos nos permite agregar una interfaz web completa basada en Flask a nuestro sistema de gestión de tareas con cambios mínimos en nuestra aplicación central. Esta demostración práctica resaltará cómo la separación de responsabilidades de Clean Architecture nos permite mantener nuestra CLI existente mientras introducimos sin problemas nuevas interfaces de usuario.

---

### Sección 8.7: Lecturas complementarias

- **Software Testing Guide** ([https://martinfowler.com/testing/](https://martinfowler.com/testing/)). Recopila todos los artículos sobre pruebas del blog de Martin Fowler.
- **Just Say No to More End-to-End Tests** ([https://testing.googleblog.com/2015/04/just-say-no-to-more-end-to-end-tests.html](https://testing.googleblog.com/2015/04/just-say-no-to-more-end-to-end-tests.html)). Un artículo del equipo de pruebas de Google que argumenta que la dependencia excesiva de las pruebas de extremo a extremo puede generar mayor complejidad, fragilidad y retroalimentación tardía en el desarrollo de software, abogando en su lugar por un enfoque equilibrado que enfatice las pruebas unitarias y de integración.
- **Python Testing with pytest** ([https://pytest.org/](https://pytest.org/)). La documentación oficial de pytest, que proporciona información detallada sobre las herramientas de prueba que hemos utilizado a lo largo de este capítulo.
- **Test-Driven Development** ([https://www.oreilly.com/library/view/test-driven-development/0321146530/](https://www.oreilly.com/library/view/test-driven-development/0321146530/)). Una guía esencial de TDD por Kent Beck, uno de sus pioneros. Este libro proporciona una base sólida para comprender cómo TDD puede mejorar el diseño de tu software y cómo se alinea de forma natural con patrones arquitectónicos como los encontrados en Clean Architecture.

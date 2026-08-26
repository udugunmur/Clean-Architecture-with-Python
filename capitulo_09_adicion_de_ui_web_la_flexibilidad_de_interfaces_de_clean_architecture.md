# Parte 3: Aplicación de Clean Architecture en Python

## Capítulo 9: Adición de UI Web: La flexibilidad de interfaces de Clean Architecture

En capítulos anteriores, establecimos los patrones fundamentales de **Clean Architecture** a través de nuestro sistema de gestión de tareas. Construimos entidades de dominio, implementamos casos de uso y creamos una interfaz de línea de comandos (CLI) que demostró cómo los límites de Clean Architecture permiten una clara separación entre nuestra lógica de negocio central y las interfaces de usuario. Si bien la CLI proporciona una interfaz funcional, muchas aplicaciones requieren acceso basado en la web. Esto presenta una excelente oportunidad para mostrar cómo los principios de Clean Architecture permiten la evolución de las interfaces sin comprometer la integridad arquitectónica.

A través de nuestro sistema de gestión de tareas, demostraremos uno de los beneficios clave de Clean Architecture: la capacidad de agregar nuevas interfaces sin modificar el código existente. Debido a que nuestra lógica de dominio, casos de uso y controladores se construyeron con los límites arquitectónicos adecuados, agregar una interfaz web se convierte en un ejercicio puramente aditivo. No se requiere refactorizar ningún componente existente. Este mismo principio que simplifica la adición de una UI web también permite el mantenimiento a largo plazo de múltiples interfaces, ya que cada una puede evolucionar de manera independiente mientras comparte el mismo núcleo robusto.

Al final de este capítulo, comprenderás cómo implementar interfaces adicionales manteniendo los límites arquitectónicos. Podrás aplicar estos patrones a tus propios proyectos, asegurando que tus aplicaciones permanezcan adaptables a medida que evolucionen los requisitos de interfaz.

En este capítulo, vamos a cubrir los siguientes temas principales:

- **Comprensión de la flexibilidad de interfaces en Clean Architecture**
- **Patrones de presentación web en Clean Architecture**
- **Integración de Flask con Clean Architecture**

---

### Sección 9.1: Requisitos técnicos

Los ejemplos de código presentados en este capítulo y a lo largo del resto del libro se han probado con Python 3.13. Por razones de brevedad, la mayoría de los ejemplos de código en el capítulo están implementados solo de forma parcial. Las versiones completas de todos los ejemplos se pueden encontrar en el repositorio de GitHub que acompaña al libro en [https://github.com/PacktPublishing/Clean-Architecture-with-Python](https://github.com/PacktPublishing/Clean-Architecture-with-Python).

---

### Sección 9.2: Comprensión de la flexibilidad de interfaces en Clean Architecture

La CLI de nuestro sistema de gestión de tareas (implementada en el [Capítulo 7](https://subscription.packtpub.com/book/programming/9781836642893/7)) demuestra la cuidadosa separación de Clean Architecture entre la lógica de negocio central y las interfaces de usuario. Esta separación no fue simplemente una buena práctica: fue una preparación estratégica para exactamente lo que lograremos en este capítulo: agregar una interfaz de usuario completamente nueva preservando nuestra funcionalidad existente.

#### Comprensión de nuestra implementación web

Para implementar nuestra interfaz web, utilizaremos **Flask**, un framework web de Python ligero y flexible. El manejo explícito de solicitudes de Flask y su estructura de aplicación directa lo hacen ideal para demostrar los límites de Clean Architecture. Su núcleo mínimo y su extenso ecosistema de extensiones opcionales se alinean bien con la preferencia de Clean Architecture por dependencias explícitas. Si bien los patrones que exploraremos funcionarían igualmente bien con Django, FastAPI u otros frameworks web, la simplicidad de Flask ayuda a mantener nuestro enfoque en los principios arquitectónicos en lugar de en características específicas del framework.

A través de una interfaz basada en navegador, los usuarios ahora pueden gestionar sus proyectos y tareas con flujos de trabajo familiares mejorados por capacidades específicas de la web. Cuando un usuario visita la aplicación, se le presentan sus proyectos y las tareas asociadas en una vista jerárquica y limpia:

> **Figura 9.1:** Página de listado de la interfaz de usuario web mostrando proyectos y sus tareas asociadas.

La interfaz web mejora nuestras capacidades de gestión de tareas existentes mediante retroalimentación visual inmediata y navegación intuitiva. Los usuarios pueden crear nuevas tareas, actualizar su estado y organizarlas dentro de proyectos. La interfaz adapta nuestra lógica de negocio existente a las convenciones web, utilizando patrones estándar tales como envíos de formularios para la creación de tareas y mensajes flash para la retroalimentación al usuario.

Para implementar esta interfaz manteniendo nuestros límites arquitectónicos, nuestra implementación web está organizada en componentes diferenciados:

> **Figura 9.2:** Archivos asociados para la implementación de la UI web.

Esta estructura demuestra la separación de responsabilidades de Clean Architecture en acción. En nuestra capa de Adaptadores e Interfaces (`interfaces`), los presentadores web saben cómo formatear datos para su visualización web mediante la creación de cadenas compatibles con HTML y la estructuración de datos para plantillas, pero permanecen completamente ajenos a Flask o a cualquier framework web específico. Estos presentadores podrían funcionar igualmente bien con Django, FastAPI o cualquier otro framework web.

Esta separación contrasta notablemente con las aplicaciones construidas sin límites arquitectónicos claros. En una aplicación menos estructurada, una solicitud para agregar una interfaz web a menudo desencadena una cascada de cambios en toda la base de código. La lógica de negocio mezclada con las preocupaciones de presentación requiere una refactorización exhaustiva. Las consultas a la base de datos incrustadas en la lógica de visualización necesitan reestructuración. Incluso cambios aparentemente simples, como formatear fechas para su visualización en la web, pueden requerir modificaciones en múltiples componentes. En casos extremos, los equipos se encuentran esencialmente reescribiendo su aplicación para dar cabida a la nueva interfaz.

Nuestro sistema de gestión de tareas, por el contrario, trata la interfaz web como un cambio puramente aditivo. Ningún código existente necesita modificación: ni nuestras reglas de negocio, ni nuestros casos de uso, ni siquiera nuestra CLI. Esta capacidad de agregar características importantes sin perturbar la funcionalidad existente demuestra el valor práctico de Clean Architecture en sistemas en evolución.

El código específico del framework reside donde corresponde: en el directorio `infrastructure/web` dentro de nuestra capa de Frameworks y Drivers. Aquí, las preocupaciones específicas de Flask, como el manejo de rutas, la configuración de plantillas y la gestión de sesiones HTTP, permanecen aisladas en los bordes de nuestro sistema. Esta separación significa que podríamos cambiar de framework web sin tocar nuestros adaptadores de interfaz ni la lógica de negocio central.

#### Implementaciones paralelas de interfaces

Antes de profundizar en los detalles de nuestra implementación web, examinemos cómo coexisten nuestras interfaces CLI y web dentro de nuestro sistema de Clean Architecture. Aunque estas interfaces atienden a los usuarios a través de mecanismos muy diferentes (línea de comandos frente a HTTP), comparten los mismos componentes centrales y siguen patrones arquitectónicos idénticos.

> **Figura 9.3:** Comparación del flujo de solicitudes.

Este diagrama ilustra cómo nuestra arquitectura mantiene límites claros mientras soporta múltiples interfaces:

- **CLI**: transforma la entrada de la línea de comandos mediante el Manejador de Comandos de Click (*Click Command Handler*).
- **Interfaz web**: procesa solicitudes HTTP mediante el Manejador de Rutas de Flask (*Flask Route Handler*).
- **Núcleo compartido (*shared core*)**: contiene nuestro Controlador de Tareas (*Task Controller*), Casos de Uso (*Use Cases*) y Entidades (*Entities*).

Clean Architecture permite esta coexistencia a través de reglas de dependencia estrictas. Ambos manejadores de interfaz se conectan al mismo controlador de tareas, pero los componentes centrales permanecen completamente ajenos a cómo están siendo utilizados. Este aislamiento significa que nuestra lógica de negocio central puede enfocarse en las reglas de creación de tareas mientras cada interfaz gestiona sus preocupaciones específicas, ya sea analizar argumentos de línea de comandos o procesar envíos de formularios.

Para implementar esta separación, utilizamos un enfoque pragmático de inyección de dependencias a través de nuestro contenedor de Aplicación:

```python
# todo_app/infrastructure/configuration/container.py
@dataclass
class Application:
    """Container which wires together all components."""

    task_repository: TaskRepository
    project_repository: ProjectRepository
    notification_service: NotificationPort
    task_presenter: TaskPresenter
    project_presenter: ProjectPresenter
```

Observa cómo cada componente se declara utilizando interfaces abstractas (`TaskRepository`, `NotificationPort`, etc.). Esto permite que cada implementación de interfaz proporcione sus propias dependencias específicas mientras el núcleo de nuestra aplicación permanece ajeno a las implementaciones concretas que recibirá. La fábrica de la aplicación (*application factory*) demuestra cómo funciona esta flexibilidad en la práctica.

Nuestra fábrica de aplicación implementa el patrón de raíz de composición (*composition root*) de Clean Architecture, que actúa como el punto único donde componemos nuestro núcleo independiente de la interfaz con las implementaciones específicas de la interfaz. La fábrica demuestra dos principios arquitectónicos clave:

```python
# todo_app/infrastructure/configuration/container.py
def create_application(
    notification_service: NotificationPort,
    task_presenter: TaskPresenter,
    project_presenter: ProjectPresenter,
) -> "Application":
    """Factory function for the Application container."""
    task_repository, project_repository = create_repositories()

    return Application(
        task_repository=task_repository,
        project_repository=project_repository,
        notification_service=notification_service,
        task_presenter=task_presenter,
        project_presenter=project_presenter,
    )
```

En primer lugar, la fábrica demuestra el Principio de Inversión de Dependencias (DIP) de Clean Architecture en acción: los componentes específicos de la interfaz (presentadores) se pasan como parámetros, mientras que la infraestructura central (repositorios) se construye internamente. Esta separación significa que las implementaciones de interfaz pueden proporcionar sus propios presentadores mientras la fábrica garantiza que todo se conecte correctamente a nuestro núcleo de negocio compartido.

En segundo lugar, la fábrica sirve como la raíz de composición que funciona como el punto único donde las interfaces abstractas se encuentran con sus implementaciones concretas.

Nuestra aplicación CLI demuestra esta adaptabilidad a diferentes interfaces. En el límite de la aplicación, conectamos nuestro núcleo compartido con componentes específicos de CLI:

```python
# cli_main.py
def main() -> int:
    """Main entry point for the CLI application."""
    app = create_application(
        notification_service=NotificationRecorder(),
        task_presenter=CliTaskPresenter(),
        project_presenter=CliProjectPresenter(),
    )
    cli = ClickCli(app)
    return cli.run()
```

Observa cómo `main()` configura una instancia de aplicación específica de CLI proporcionando implementaciones específicas de interfaz (`CliTaskPresenter`, `CliProjectPresenter`) a nuestro contenedor de aplicación genérico. La clase `ClickCli` luego envuelve esta aplicación central, gestionando la traducción entre las interacciones de línea de comandos y las operaciones independientes de interfaz de nuestra aplicación. Este patrón de envolver código específico de interfaz alrededor de nuestra aplicación central es una práctica fundamental de Clean Architecture que veremos reflejada en nuestra implementación web.

Al configurar nuestra aplicación de esta manera, hemos establecido un patrón claro de cómo las nuevas interfaces se conectan a nuestra aplicación central. Para agregar nuestra interfaz web, necesitaremos implementar componentes análogos que cumplan los mismos roles pero para las preocupaciones específicas de la web:

- **Capa de presentación**: implementar `WebTaskPresenter` para plantillas HTML.
- **Manejo de solicitudes**: procesar envíos de formularios y parámetros de URL.
- **Estado de sesión**: gestionar la persistencia entre solicitudes.
- **Retroalimentación del usuario**: implementar la presentación de errores específica para la web.

La idea clave es que todas las preocupaciones específicas de la interfaz permanecen en los bordes de nuestro sistema. Cada interfaz gestiona sus propios requisitos únicos, tales como la gestión de sesiones web o el análisis sintáctico de argumentos CLI, mientras nuestra lógica de negocio central permanece enfocada y limpia.

En la siguiente sección, exploraremos patrones de presentación específicos para interfaces web, viendo cómo estos mismos principios que mantuvieron limpia nuestra implementación CLI pueden guiarnos en la creación de componentes específicos de la web que sean mantenibles.

#### Violaciones comunes de los límites de interfaz

La eficacia de Clean Architecture depende de mantener límites claros entre capas. Una violación común ocurre cuando los desarrolladores permiten que el formateo específico de la interfaz se filtre en los controladores, creando dependencias problemáticas que fluyen en la dirección incorrecta. Considera este antipatrón:

```python
# Anti-pattern: Interface-specific logic in controller
def handle_create(self, request_data: dict) -> dict:
    """DON'T: Mixing CLI formatting in controller."""
    try:
        result = self.create_use_case.execute(request_data)
        if result.is_success:
            # Wrong: CLI-specific formatting doesn't belong here
            return {"message": click.style(f"Created task: {result.value.title}", fg="green")}
    except ValueError as e:
        # Wrong: CLI-specific error formatting
        return {"error": click.style(str(e), fg="red")}
```

Esta implementación viola la Regla de Dependencia de Clean Architecture de una manera sutil pero importante. El controlador, que reside en nuestra capa de Adaptadores de Interfaz, hace referencia directa a `click` (un framework que debería estar restringido a nuestra capa más externa). Esto genera un acoplamiento problemático, ya que nuestro controlador ahora depende tanto de la capa de Aplicación (hacia adentro) como de la capa de Frameworks (hacia afuera), rompiendo la regla fundamental de Clean Architecture de que las dependencias solo deben apuntar hacia adentro. Más allá de la violación arquitectónica, este acoplamiento tiene consecuencias prácticas: no podríamos reutilizar este controlador para nuestra interfaz web, e incluso actualizar a una versión más reciente de Click requeriría cambios en nuestra capa de Adaptadores de Interfaz.

En su lugar, nuestro sistema de gestión de tareas delega correctamente todas las responsabilidades de formateo a presentadores específicos de la interfaz. Observa cómo nuestro controlador depende únicamente de la interfaz abstracta `Presenter`. No tiene conocimiento de si está trabajando con CLI, web o cualquier otra implementación concreta de presentador:

```python
# Correct: Interface-agnostic controller
def handle_create(self, title: str, description: str) -> OperationResult:
    """DO: Keep controllers interface-agnostic."""
    try:
        request = CreateTaskRequest(title=title, description=description)
        result = self.create_use_case.execute(request)
        if result.is_success:
            view_model = self.presenter.present_task(result.value)
            return OperationResult.succeed(view_model)

        error_vm = self.presenter.present_error(result.error.message, str(result.error.code.name))
        return OperationResult.fail(error_vm.message, error_vm.code)
    except ValueError as e:
        error_vm = self.presenter.present_error(str(e), "VALIDATION_ERROR")
        return OperationResult.fail(error_vm.message, error_vm.code)
```

Esta implementación corregida demuestra varios principios de Clean Architecture:

- El controlador acepta tipos simples (`str`) en lugar de estructuras específicas del framework.
- El manejo de errores produce instancias de `OperationResult` independientes del framework.
- Todo el formateo se delega a la interfaz abstracta del presentador.
- El controlador permanece enfocado en coordinar entre los casos de uso y la presentación.

Este enfoque produce beneficios prácticos significativos. Con nuestra implementación limpia, los cambios en los frameworks solo afectan a la capa más externa. Podríamos reemplazar Click con otro framework de CLI simplemente implementando nuevos adaptadores sin tocar nuestros controladores, casos de uso o lógica de dominio. El mismo controlador procesa las solicitudes de manera idéntica, independientemente de si se originan en nuestra CLI, en la interfaz web o en cualquier interfaz futura que podamos agregar.

La capa de Adaptadores de Interfaz actúa como un límite protector, transformando datos entre nuestro núcleo de dominio y las interfaces externas. Este límite arquitectónico nos permite agregar una interfaz web sin perturbar los componentes existentes. Nuestras entidades de dominio se enfocan exclusivamente en las reglas de negocio, mientras que las preocupaciones específicas de la interfaz permanecen debidamente aisladas en los bordes del sistema.

Una vez establecido cómo los límites de Clean Architecture permiten la flexibilidad de interfaces, examinemos los patrones de presentación específicos necesarios para las interfaces web y cómo mantienen estos mismos principios arquitectónicos.

---

### Sección 9.3: Patrones de presentación web en Clean Architecture

Habiendo establecido cómo Clean Architecture permite la flexibilidad de interfaces, ahora nos enfocamos en los patrones específicos necesarios para la presentación web. Mientras que nuestra CLI formateaba los datos directamente para la salida por consola, las interfaces web deben gestionar requisitos de presentación más complejos: formatear datos para plantillas HTML, gestionar el estado a través de múltiples solicitudes y proporcionar retroalimentación al usuario mediante validación de formularios y mensajes flash (banners temporales de notificación que aparecen en la parte superior de la página después de una acción, como el mensaje verde de éxito mostrado en la Figura 9.1). Esta sección explora estos desafíos específicos de la web y muestra cómo los límites de Clean Architecture guían nuestras decisiones de implementación.

Examinaremos cómo los presentadores específicos de la web formatean los datos del dominio para su visualización en HTML, garantizando que nuestras plantillas reciban información estructurada adecuadamente. Veremos cómo la gestión del estado a través de solicitudes puede respetar los límites de Clean Architecture, y cómo el manejo de formularios puede mantener la separación entre las preocupaciones de la web y las reglas de negocio. A través de estos patrones, demostraremos que las interfaces web, a pesar de su complejidad, pueden integrarse limpiamente con nuestra arquitectura existente.

#### Implementación de presentadores específicos para la web

Para tender un puente entre nuestra lógica de dominio y los requisitos de visualización web, necesitamos presentadores que comprendan las convenciones web. Para entender cómo debe funcionar nuestro presentador web, examinemos primero nuestro presentador CLI del [Capítulo 7](https://subscription.packtpub.com/book/programming/9781836642893/7). Observa cómo encapsula todas las decisiones de formateo específicas de CLI (estado entre corchetes, prioridades coloreadas) mientras mantiene una interfaz limpia a través de `TaskViewModel`. Este patrón establecido de transformar objetos de dominio en modelos de vista (*view models*) apropiados para la interfaz guiará nuestra implementación web:

```python
# CLI Presenter from Chapter 7
def present_task(self, task_response: TaskResponse) -> TaskViewModel:
    """Format task for CLI display."""
    return TaskViewModel(
        id=task_response.id,
        title=task_response.title,
        status_display=f"[{task_response.status.value}]",  # CLI-specific bracketed format
        priority_display=self._format_priority(task_response.priority),  # CLI-specific coloring
    )
```

Nuestro presentador web sigue el mismo patrón, pero adapta el formateo para la visualización en HTML:

```python
class WebTaskPresenter(TaskPresenter):

    def present_task(self, task_response: TaskResponse) -> TaskViewModel:
        """Format task for web display."""
        return TaskViewModel(
            id=task_response.id,
            title=task_response.title,
            description=task_response.description,
            status_display=task_response.status.value,
            priority_display=task_response.priority.name,
            due_date_display=self._format_due_date(task_response.due_date),
            project_display=task_response.project_id,
            completion_info=self._format_completion_info(
                task_response.completion_date, task_response.completion_notes
            ),
        )
```

Observa cómo la clase `WebTaskPresenter` proporciona campos adicionales y formateos específicos para las necesidades de visualización web: valores de estado compatibles con HTML, formateo de fechas para su visualización en el navegador e información de finalización estructurada para el renderizado en plantillas. Esta implementación demuestra cómo los presentadores de Clean Architecture actúan como una capa sistemática de traducción entre los conceptos de dominio y las necesidades de presentación:

- Traduce objetos de dominio a formatos apropiados para la interfaz mientras preserva su significado de negocio.
- Centraliza todas las decisiones de presentación en un componente único y comprobable mediante pruebas.
- Permite que cada interfaz adapte los datos de dominio según sus necesidades específicas.
- Mantiene una separación clara entre la lógica de dominio y las preocupaciones de visualización.

El presentador no solo formatea datos; actúa como el intérprete autorizado de cómo deben aparecer los conceptos de dominio en la interfaz. Considera nuestro método de formateo de fechas:

```python
from datetime import datetime, timezone
from typing import Optional


def _format_due_date(self, due_date: Optional[datetime]) -> str:
    """Format due date for web display."""
    if not due_date:
        return ""

    is_overdue = due_date < datetime.now(timezone.utc)
    date_str = due_date.strftime("%Y-%m-%d")
    return f"Overdue: {date_str}" if is_overdue else date_str
```

El método `_format_due_date` encapsula todas las decisiones de formateo relacionadas con fechas: manejo de zonas horarias, cadenas de formato de fecha y comprobaciones del estado de vencimiento. Al contener estas decisiones en el presentador, aseguramos que nuestras entidades de dominio permanezcan enfocadas en las reglas de negocio (cuándo vence una tarea) mientras que las preocupaciones de presentación (cómo mostrar esa fecha de vencimiento) se mantienen en la capa arquitectónica apropiada.

Esta capa de traducción permite que nuestras plantillas se mantengan simples sin dejar de ofrecer información rica y contextual:

```html
<span class="badge {% if 'overdue' in task.due_date_display %}bg-danger {% else %}bg-info {% endif %}">
    {{ task.due_date_display }}
</span>
```

La plantilla ejemplifica la separación de responsabilidades de Clean Architecture en acción: se enfoca puramente en la estructura HTML y las decisiones de estilo basadas en valores previamente formateados. Toda la lógica de negocio (comparaciones de `datetime`) y el formateo de datos permanecen en las capas arquitectónicas correspondientes. La plantilla simplemente adapta la salida del presentador para su visualización visual, utilizando comprobaciones de cadenas simples para aplicar las clases CSS apropiadas.

Al igual que en el [Capítulo 8](https://subscription.packtpub.com/book/programming/9781836642893/8), podemos verificar esta lógica de formateo mediante pruebas unitarias focalizadas. Esta prueba demuestra un beneficio clave de la separación de responsabilidades de Clean Architecture: podemos verificar nuestra lógica de presentación de forma aislada, sin ninguna dependencia del framework web. Al probar directamente contra el presentador, podemos asegurarnos de que nuestra lógica de formateo de fechas funcione correctamente sin configurar un entorno web completo. La prueba se centra puramente en la transformación de los datos de dominio al formato de presentación:

```python
def test_web_presenter_formats_overdue_date():
    """Test that presenter properly formats overdue dates."""
    # Arrange
    past_date = datetime.now(timezone.utc) - timedelta(days=1)
    task_response = TaskResponse(
        id="123",
        title="Test Task",
        description="Test Description",
        status=TaskStatus.TODO,
        priority=Priority.MEDIUM,
        project_id="456",
        due_date=past_date,
    )
    presenter = WebTaskPresenter()

    # Act
    view_model = presenter.present_task(task_response)

    # Assert
    assert "Overdue" in view_model.due_date_display
    assert past_date.strftime("%Y-%m-%d") in view_model.due_date_display
```

Esta prueba demuestra cómo la separación de Clean Architecture permite una verificación precisa de nuestra lógica de formateo web. Podemos probar escenarios complejos, como fechas vencidas, sin ninguna configuración de framework web. El mismo patrón se aplica a fechas futuras:

```python
def test_web_presenter_formats_future_date():
    """Test that presenter properly formats future dates."""
    # Arrange
    future_date = datetime.now(timezone.utc) + timedelta(days=1)
    task_response = TaskResponse(
        id="123",
        title="Test Task",
        description="Test Description",
        status=TaskStatus.TODO,
        priority=Priority.MEDIUM,
        project_id="456",
        due_date=future_date,
    )
    presenter = WebTaskPresenter()

    # Act
    view_model = presenter.present_task(task_response)

    # Assert
    assert "Overdue" not in view_model.due_date_display
    assert future_date.strftime("%Y-%m-%d") in view_model.due_date_display
```

Esta prueba complementaria asegura que nuestro presentador maneje las fechas futuras de manera apropiada, completando nuestra verificación de la lógica de formateo de fechas. Junto con la prueba anterior, hemos confirmado tanto la presencia como la ausencia del indicador `'Overdue'`, todo sin tocar ninguna línea de código del framework web.

Estas pruebas destacan los beneficios clave del patrón presentador en Clean Architecture. Nuestra lógica de formateo se puede verificar sin una configuración web compleja: no hay necesidad de clientes de prueba de Flask, bases de datos simuladas (*mock databases*) ni análisis sintáctico de HTML (*HTML parsing*). Los cambios en el formateo de fechas se pueden probar rápida y precisamente, mientras nuestras plantillas permanecen enfocadas exclusivamente en las preocupaciones de visualización.

Este patrón se extiende a todos los conceptos de dominio, desde el estado de la tarea hasta los niveles de prioridad, garantizando una traducción consistente de los objetos de negocio en formatos listos para su presentación. Cualquier plantilla de nuestro sistema puede mostrar fechas de vencimiento de tareas sin conocer cómo se formatean dichas fechas. Más importante aún, a medida que nuestra lógica de formateo evolucione con adiciones como soporte para zonas horarias o nuevos formatos de visualización, solo necesitaremos actualizar el presentador y sus pruebas. Nuestras plantillas, controladores y lógica de dominio permanecen inalterados.

#### Presentadores frente al formateo basado en plantillas

Los desarrolladores familiarizados con frameworks web modernos como React, Vue o patrones orientados a plantillas en Flask/Django podrían cuestionar nuestra separación de la lógica de formateo en presentadores. Muchas aplicaciones incrustan el formateo directamente en las plantillas:

```html
<!-- Common pattern in many web frameworks -->
<span class="badge {% if task.due_date < now() %}bg-danger{% else %}bg-info{% endif %}">
    {{ task.due_date.strftime("%Y-%m-%d") }}
    {% if task.due_date < now() %}(Overdue){% endif %}
</span>
```

Si bien este patrón está muy extendido, difumina el límite entre las decisiones de presentación y la estructura de visualización. En Clean Architecture, reconocemos el formateo como una responsabilidad de traducción que pertenece a la capa de Adaptadores de Interfaz, no a las plantillas en sí.

Incluso cuando se trabaja con frameworks orientados a plantillas, los principios de Clean Architecture aún pueden guiar las decisiones de implementación al:

- Reconocer dónde se están filtrando decisiones de negocio en las plantillas.
- Extraer la lógica de formateo en componentes dedicados.
- Tratar a las plantillas puramente como estructura de visualización.

El principio arquitectónico fundamental sigue siendo el mismo: mantener límites claros entre capas. Ya sea que se implemente a través de nuestro patrón explícito de presentador o mediante ayudantes (*helpers*) y componentes de plantillas, el objetivo es garantizar que los conceptos de dominio se traduzcan adecuadamente antes de que alcancen la capa de visualización más externa.

#### Gestión del estado específico de la web

Los datos de sesión y el estado de los formularios presentan desafíos únicos para mantener los límites de Clean Architecture. Examinemos cómo nuestro sistema gestiona estas preocupaciones específicas de la web manteniendo pura nuestra lógica de dominio central. Considera este antipatrón donde una entidad de dominio accede directamente a los datos de la sesión web:

```python
# Anti-pattern: Domain entity accessing web state
from datetime import datetime


class Task:
    def complete(self, web_app_contatiner):
        # Wrong: Task shouldn't know about web sessions
        self.completed_by = web_app_contatiner.user.id
        self.completed_at = datetime.now()
```

Esto demuestra cómo mezclar preocupaciones de la web en las entidades de dominio crea múltiples desafíos de mantenimiento:

- Las pruebas requieren simular (*mocking*) datos de sesión web incluso para la lógica de dominio básica.
- Agregar nuevas interfaces significa actualizar el código de las entidades en lugar de simplemente agregar adaptadores.
- Los errores en el manejo de sesiones pueden propagarse a través de toda la capa de Dominio.
- El comportamiento de las entidades pasa a depender de los detalles de implementación del framework web.

Nuestros manejadores de rutas de Flask actúan como el límite arquitectónico donde se gestionan las preocupaciones específicas de la web. Traducen conceptos HTTP a operaciones independientes del dominio mientras mantienen la gestión del estado web donde pertenece:

```python
# todo_app/infrastructure/web/routes.py
@bp.route("/")
def index():
    """List all projects with their tasks."""
    app = current_app.config["APP_CONTAINER"]
    show_completed = request.args.get("show_completed", "false").lower() == "true"

    result = app.project_controller.handle_list()
    if not result.is_success:
        error = project_presenter.present_error(result.error.message)
        flash(error.message, "error")
        return redirect(url_for("todo.index"))

    return render_template("index.html", projects=result.success, show_completed=show_completed)
```

Este manejador ejemplifica la gestión de límites de Clean Architecture en acción. En este borde externo de nuestro sistema, la ruta captura y procesa el estado específico de la web como la preferencia `show_completed`, traduciendo conceptos HTTP en operaciones independientes del dominio. En lugar de permitir que las entidades de dominio accedan directamente a los datos de la sesión, el manejador extrae únicamente la información necesaria antes de pasarla a nuestra lógica de negocio central. Las preocupaciones específicas de la web, tales como la retroalimentación al usuario mediante mensajes flash y el renderizado de plantillas, permanecen en esta capa externa, mientras nuestra lógica de dominio se mantiene enfocada puramente en sus responsabilidades centrales.

#### Manejo y validación de formularios

Los envíos de formularios en aplicaciones web presentan un desafío arquitectónico. Un antipatrón común consiste en dispersar la lógica de validación entre plantillas, controladores y entidades de dominio, lo que dificulta el mantenimiento y la evolución de las reglas de validación. Examinemos cómo Clean Architecture nos guía para manejar los formularios adecuadamente utilizando un formulario simple de creación de proyectos:

```python
# todo_app/infrastructure/web/routes.py
@bp.route("/projects/new", methods=["GET", "POST"])
def new_project():
    """Create a new project."""
    if request.method == "POST":
        name = request.form["name"]
        app = current_app.config["APP_CONTAINER"]
        result = app.project_controller.handle_create(name)

        if not result.is_success:
            error = project_presenter.present_error(result.error.message)
            flash(error.message, "error")
            return redirect(url_for("todo.index"))

        project = result.success
        flash(f'Project "{project.name}" created successfully', "success")
        return redirect(url_for("todo.index"))

    return render_template("project_form.html")
```

El manejador de rutas demuestra el flujo de validación de Clean Architecture:

- **La ruta extrae entradas específicas de la web**:
  - Parámetros de URL (`project_id`)
  - Campos de formulario (`request.form["title"]`, etc.)
  - Campos opcionales con valores predeterminados (`due_date`)
- **El controlador de tareas recibe tipos estándar de Python**:
  - Cadenas (`str`) para campos de texto
  - `None` para campos opcionales vacíos
  - El `project_id` de la URL
- **La validación de dominio ocurre a través de las capas establecidas**:
  - Reglas de negocio en entidades
  - Coordinación de casos de uso
  - Resultados devueltos a través de nuestro tipo `Result`
- **Respuestas específicas de la web**:
  - Redirecciones de éxito con mensajes flash
  - Manejo de errores a través de mensajes flash y redirecciones

##### Sincronización de la validación del lado del cliente y del dominio

Si bien nuestra validación de dominio proporciona la fuente definitiva de la verdad (*single source of truth*), las aplicaciones web modernas a menudo necesitan retroalimentación inmediata para el usuario. Flask proporciona mecanismos como **WTForms** que pueden reflejar las reglas de validación del dominio en la capa de vista, permitiendo una experiencia de usuario (UX) ágil sin duplicar la lógica de validación. La clave es asegurarse de que estas validaciones de la capa de vista sigan siendo envoltorios delgados (*thin wrappers*) alrededor de nuestras reglas de dominio centrales, en lugar de introducir una lógica de validación paralela.

Esta separación asegura que nuestras reglas de validación permanezcan con nuestra lógica de dominio donde pertenecen, mientras que la capa web se enfoca en recopilar entradas y presentar retroalimentación.

---

### Sección 9.4: Integración de Flask con Clean Architecture

Una vez establecidos nuestros patrones de presentación y el enfoque de gestión del estado, pasamos a la integración práctica de Flask en nuestro sistema de Clean Architecture. Basándonos en la estructura del contenedor de aplicación vista anteriormente en *Comprensión de la flexibilidad de interfaces en Clean Architecture*, nos centraremos en los aspectos específicos de Flask de nuestra interfaz web:

- Configurar el patrón de fábrica de aplicaciones (*application factory*) de Flask.
- Gestionar configuraciones y dependencias específicas de Flask.
- Conectar las rutas de Flask con nuestra lógica de aplicación central.

Así es como nuestra fábrica de aplicaciones de Flask se integra con nuestra arquitectura existente:

```python
# todo_app/infrastructure/web/app.py
def create_web_app(app_container: Application) -> Flask:
    """Create and configure Flask application."""
    flask_app = Flask(__name__)
    flask_app.config["SECRET_KEY"] = "dev"  # Change this in production
    flask_app.config["APP_CONTAINER"] = app_container  # Store container in config

    # Register blueprints
    from . import routes

    flask_app.register_blueprint(routes.bp)

    return flask_app
```

Examinemos los componentes clave de esta configuración. Como se muestra en la Figura 9.4, `web_main.py` actúa como el punto de entrada de nuestra aplicación, orquestando la creación y configuración tanto de nuestra lógica de negocio (Contenedor de Aplicación) como de nuestra interfaz web (Contenedor Web) a través de Flask. El Contenedor de Aplicación alberga nuestra lógica de negocio central, mientras que el Contenedor Web gestiona las preocupaciones específicas de Flask, como rutas y plantillas.

> **Figura 9.4:** Inicialización (*bootstrapping*) de la aplicación Flask mostrando las relaciones entre contenedores.

Esta estructura sigue los principios de Clean Architecture de varias maneras clave:

- Mantener el código específico de Flask aislado en el Contenedor Web.
- Mantener la independencia de nuestro Contenedor de Aplicación central respecto a las preocupaciones de la web.
- Permitir rutas de comunicación claras entre contenedores a través de interfaces bien definidas.

Con estos contenedores adecuadamente configurados y conectados, estamos listos para implementar nuestras rutas y plantillas. Estos componentes se basarán en los patrones de presentación que hemos establecido, mostrando cómo Clean Architecture nos permite crear una interfaz web completa manteniendo límites arquitectónicos claros.

#### Implementación de rutas y plantillas

Anteriormente en este capítulo examinamos las rutas desde la perspectiva del flujo de datos: cómo representan puntos de entrada en nuestro sistema y traducen solicitudes HTTP para nuestro dominio central. Ahora observemos más de cerca su implementación para comprender cómo mantienen los límites de Clean Architecture mientras ofrecen una funcionalidad web específica.

Así como nuestra implementación CLI traducía los argumentos de la línea de comandos en entradas para los casos de uso, nuestras rutas web traducen las solicitudes HTTP en operaciones que nuestra aplicación central puede entender. Si bien el mecanismo de entrega difiere (solicitudes HTTP en lugar de argumentos de línea de comandos), el patrón arquitectónico sigue siendo el mismo: la entrada externa fluye a través de nuestros adaptadores de interfaz antes de alcanzar el núcleo de nuestra aplicación.

Considera cómo manejaba nuestra CLI la creación de tareas:

```python
# todo_app/infrastructure/cli/click_cli_app.py
def _create_task(self):
    """CLI task creation."""
    title = click.prompt("Task title", type=str)
    description = click.prompt("Description", type=str)
    result = self.app.task_controller.handle_create(title=title, description=description)
```

Nuestra ruta web implementa el mismo patrón arquitectónico que nuestra CLI, aunque adaptado al ciclo de solicitud-respuesta de HTTP. De la misma manera en que el manejador de CLI transformaba los argumentos de línea de comandos en operaciones de dominio, este manejador de rutas actúa como un límite limpio entre los conceptos HTTP y nuestra lógica de dominio:

```python
@bp.route("/projects/<project_id>/tasks/new", methods=["GET", "POST"])
def new_task(project_id):
    """Create a new task in a project."""
    if request.method == "POST":
        app = current_app.config["APP_CONTAINER"]
        result = app.task_controller.handle_create(
            project_id=project_id,
            title=request.form["title"],
            description=request.form["description"],
            priority=request.form["priority"],
            due_date=request.form["due_date"] if request.form["due_date"] else None,
        )

        if not result.is_success:
            error = task_presenter.present_error(result.error.message)
            flash(error.message, "error")
            return redirect(url_for("todo.index"))

        task = result.success
        flash(f'Task "{task.title}" created successfully', "success")
        return redirect(url_for("todo.index"))

    return render_template("task_form.html", project_id=project_id)
```

Observa cómo ambas implementaciones:

- Recopilan entradas de formas específicas de la interfaz (solicitudes de CLI frente a datos de formulario).
- Transforman esa entrada en parámetros estándar para nuestro controlador.
- Manejan las respuestas de éxito y error de forma apropiada para su interfaz (salida de CLI frente a redirecciones HTTP).

Este patrón coherente demuestra cómo Clean Architecture permite múltiples interfaces mientras mantiene nuestra aplicación central enfocada en la lógica de negocio.

El manejo de rutas va más allá del simple procesamiento de formularios. El parámetro `project_id` proviene de la propia URL (`/projects/<project_id>/tasks/new`), mientras que los campos del formulario contienen los detalles de la tarea. Nuestras capas de Clean Architecture gestionan esto de forma natural:

- **La capa de rutas gestiona todos los aspectos específicos de la web**:
  - Extracción de parámetros de URL.
  - Recopilación de datos de formularios.
  - Mensajes flash para retroalimentación al usuario (mensajes temporales de la interfaz mostrados tras redirecciones).
  - Selección y renderizado de plantillas.
- **La capa de controladores gestiona**:
  - Combinar los datos de la URL y del formulario en una única operación.
  - Coordinar con los casos de uso correspondientes.
  - Devolver resultados que nuestra capa web pueda interpretar.

Las plantillas representan la capa más externa de nuestro sistema de Clean Architecture, sirviendo como el punto final de transformación entre nuestros conceptos de dominio y la interfaz de usuario. Mientras que nuestros presentadores gestionan la transformación lógica de los datos de dominio en modelos de vista, las plantillas se enfocan exclusivamente en la representación visual de esos datos:

```html
{% extends 'base.html' %}
{% block content %}
{% for project in projects %}
<div class="card mb-4">
    <div class="card-header">
        <h2 class="card-title h5 mb-0">{{ project.name }}</h2>
    </div>
    <!-- Template focuses purely on structure and display -->
</div>
{% endfor %}
{% endblock %}
```

Esta plantilla demuestra nuestra limpia separación de responsabilidades en acción. Trabaja exclusivamente con el `ProjectViewModel` proporcionado por nuestros presentadores. Observa cómo simplemente hace referencia a `project.name` sin ningún conocimiento de cómo se obtuvieron o procesaron esos datos. La plantilla no tiene conocimiento de los repositorios, casos de uso ni siquiera de la capa HTTP, sino que se centra únicamente en renderizar los modelos de vista proporcionados en un formato fácil de usar. Esto refleja cómo nuestros presentadores CLI formateaban datos para la salida por consola, gestionando cada interfaz únicamente sus requisitos de visualización específicos.

Esta separación significa que podemos rediseñar completamente nuestras plantillas, ya sea cambiando el diseño (*layout*), agregando nuevos componentes de interfaz de usuario o incluso cambiando de motor de plantillas, sin tocar nuestra lógica de aplicación central.

#### Ejecución de tu aplicación web Clean Architecture

Habiendo implementado los componentes de nuestra interfaz web, examinemos cómo inicializar nuestra aplicación de Clean Architecture. El script `web_main.py` actúa como nuestra raíz de composición: el punto único donde las interfaces abstractas se encuentran con sus implementaciones concretas. Este punto de entrada orquesta la creación y conexión de nuestros componentes mientras mantiene las reglas de dependencia de Clean Architecture:

```python
def main():
    """Create and run the Flask web application."""
    app_container = create_application(
        notification_service=create_notification_service(),
        task_presenter=WebTaskPresenter(),
        project_presenter=WebProjectPresenter(),
    )

    web_app = create_web_app(app_container)
    web_app.run(debug=True)


if __name__ == "__main__":
    main()
```

El Principio de Inversión de Dependencias permite la configuración en tiempo de ejecución de implementaciones concretas a través de variables de entorno. Así como nuestra aplicación CLI podía alternar componentes sin cambios de código, nuestra interfaz web mantiene esta flexibilidad:

```bash
# Repository Configuration
export TODO_REPOSITORY_TYPE="memory"  # or "file"
export TODO_DATA_DIR="repo_data"      # used with file repository
  
# Optional: Email Notification Configuration
export TODO_SENDGRID_API_KEY="your_api_key"
export TODO_NOTIFICATION_EMAIL="recipient@example.com"
```

Esta flexibilidad de configuración demuestra un beneficio clave de Clean Architecture: la capacidad de cambiar componentes fácilmente. Por ejemplo, cambiar `TODO_REPOSITORY_TYPE` de `"memory"` a `"file"` cambia toda nuestra implementación de almacenamiento sin requerir ningún cambio de código. El mismo patrón que nos permitió agregar una interfaz web también permite:

- Agregar nuevos motores (*backends*) de almacenamiento (como PostgreSQL o MongoDB).
- Implementar servicios de notificación adicionales.
- Crear nuevas interfaces (como una aplicación de escritorio o móvil).
- Admitir métodos de autenticación alternativos.

Cada una de estas mejoras se puede implementar y probar de forma aislada, para luego integrarse a través de nuestros limpios límites arquitectónicos. Esta capacidad permite a los equipos de desarrollo experimentar con nuevas características y tecnologías manteniendo la estabilidad del sistema. En lugar de despliegues de código arriesgados del tipo "gran impacto" (*big bang*), los equipos pueden hacer evolucionar gradualmente sus aplicaciones agregando y probando nuevos componentes dentro de los límites protectores de Clean Architecture.

Para iniciar la aplicación web, ejecuta el script principal:

```bash
$ python web_main.py
 * Serving Flask app 'todo_app.infrastructure.web.app'
 * Debug mode: on
 * Running on http://127.0.0.1:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 954-447-204
127.0.0.1 - - [05/Feb/2025 13:58:57] "GET / HTTP/1.1" 200 -
```

Visitar `http://127.0.0.1:5000` en tu navegador presenta una interfaz web que, aunque radicalmente diferente en forma de nuestra CLI, opera sobre exactamente los mismos componentes centrales. Donde nuestra CLI interpretaba argumentos de línea de comandos, nuestra interfaz web ahora procesa envíos de formularios y parámetros de URL. El mismo caso de uso de creación de tareas que anteriormente respondía a comandos de CLI ahora maneja solicitudes HTTP POST:

> **Figura 9.5:** Formulario de creación de tareas mostrando el manejo de entradas específicas de la web.

Esta dualidad muestra Clean Architecture en la práctica. Nuestra sencilla aplicación de línea de comandos ahora coexiste con una interfaz web completa, provista de formularios, actualizaciones dinámicas y retroalimentación visual. Ambas interfaces se ejecutan de forma independiente pero comparten los mismos componentes centrales. El idéntico caso de uso de creación de tareas que antes procesaba comandos CLI ahora gestiona sin problemas los envíos de formularios web. Nuestros repositorios mantienen datos coherentes independientemente de qué interfaz cree o actualice los registros. El manejo de errores se adapta de forma natural: con mensajes de error por línea de comandos para usuarios de CLI, y con mensajes flash y validación de formularios para usuarios web.

Estas no son simplemente dos aplicaciones separadas que casualmente usan código similar: son dos interfaces para exactamente el mismo núcleo de aplicación, cada una presentando sus capacidades de una manera que tiene sentido para su entorno. Un miembro del equipo podría crear una tarea a través de la CLI mientras otro la actualiza a través de la interfaz web, fluyendo ambas operaciones a través de los mismos casos de uso y repositorios, lo que demuestra el poder práctico de las reglas de límites de Clean Architecture.

---

### Sección 9.5: Resumen

Nuestro recorrido desde la CLI hasta la interfaz web destaca el poder de Clean Architecture para permitir la evolución del sistema sin comprometer la integridad arquitectónica. Esta capacidad se extiende más allá de las interfaces web hacia un principio más amplio: los límites arquitectónicos bien diseñados crean sistemas que pueden adaptarse a los cambiantes requisitos de interfaz mientras mantienen un núcleo estable.

Los patrones que hemos explorado proporcionan una plantilla para la evolución futura del sistema. Estos patrones abarcan desde presentadores específicos de interfaz hasta la gestión del estado en los límites del sistema. Ya sea que agregues interfaces móviles, endpoints de API o modelos de interacción completamente nuevos, estos mismos principios aseguran que nuestra lógica de negocio central permanezca enfocada y protegida.

Esta flexibilidad no se logra a expensas de la mantenibilidad. Al mantener nuestras entidades de dominio enfocadas en las reglas de negocio y nuestros casos de uso trabajando con conceptos de dominio puros, hemos creado un sistema donde cada capa puede evolucionar de forma independiente. Los nuevos requisitos de interfaz pueden satisfacerse mediante adaptadores adicionales, mientras que nuestra lógica de negocio central permanece estable e intacta.

En el [Capítulo 10](https://subscription.packtpub.com/book/programming/9781836642893/10), exploraremos cómo agregar registro de logs (*logging*) y monitorización a los sistemas de Clean Architecture, asegurando que nuestras aplicaciones sigan siendo observables y mantenibles en entornos de producción.

---

### Sección 9.6: Lecturas complementarias

Para obtener más información sobre los temas tratados en este capítulo, consulta los siguientes recursos:

- **Documentación de Flask** ([https://flask.palletsprojects.com/en/stable/](https://flask.palletsprojects.com/en/stable/)). Documentación completa para el framework Flask.
- **WTForms** ([https://wtforms.readthedocs.io/en/3.2.x/](https://wtforms.readthedocs.io/en/3.2.x/)). Biblioteca flexible de validación y renderizado de formularios para el desarrollo web en Python.

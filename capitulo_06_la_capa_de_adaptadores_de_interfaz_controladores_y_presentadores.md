# Parte 2: Implementación de Clean Architecture en Python

## Capítulo 6: La capa de Adaptadores de Interfaz: Controladores y Presentadores

En los Capítulos 4 y 5, construimos el núcleo de nuestro sistema de gestión de tareas: las entidades de dominio que representan nuestros conceptos de negocio y los casos de uso que los orquestan. Los modelos de solicitud/respuesta (*request/response*) de la capa de Aplicación manejan la traducción entre los casos de uso y los objetos de dominio, asegurando que nuestras reglas de negocio centrales permanezcan puras y enfocadas. Sin embargo, todavía existe una brecha entre estos casos de uso y el mundo exterior, tales como las interfaces web o las herramientas de línea de comandos. Aquí es donde entra en juego la capa de **Adaptadores de Interfaz** (*Interface Adapters*).

La capa de Adaptadores de Interfaz sirve como traductor entre el núcleo de nuestra aplicación y las cuestiones externas. Convierte los datos entre formatos convenientes para las agencias externas y aquellos esperados por nuestros casos de uso. A través de controladores y presentadores cuidadosamente diseñados, esta capa mantiene los límites arquitectónicos que conservan nuestras reglas de negocio centrales aisladas y mantenibles.

En este capítulo, exploraremos cómo implementar la capa de Adaptadores de Interfaz en Python, viendo cómo defiende la Regla de Dependencia de Clean Architecture. Aprenderemos cómo los controladores coordinan la entrada externa con nuestros casos de uso y cómo los presentadores transforman los datos del dominio para diversas necesidades de salida.

Al final de este capítulo, comprenderás cómo crear una capa flexible de Adaptadores de Interfaz que proteja tu lógica de negocio central mientras admite múltiples interfaces. Implementarás límites arquitectónicos limpios que harán que tu sistema sea más mantenible y adaptable al cambio.

En este capítulo, vamos a cubrir los siguientes temas principales:

- **Diseño de la capa de Adaptadores de Interfaz**
- **Implementación de controladores en Python**
- **Aplicación de límites a través de adaptadores de interfaz**
- **Construcción de presentadores para el formateo de datos**

---

### Sección 6.1: Requisitos técnicos

Los ejemplos de código presentados en este capítulo y a lo largo del resto del libro se han probado con Python 3.13. Por razones de brevedad, los ejemplos de código en el capítulo pueden estar implementados parcialmente. Las versiones completas de todos los ejemplos se pueden encontrar en el repositorio de GitHub complementario del libro en [https://github.com/PacktPublishing/Clean-Architecture-with-Python](https://github.com/PacktPublishing/Clean-Architecture-with-Python).

---

### Sección 6.2: Diseño de la capa de Adaptadores de Interfaz

En Clean Architecture, cada capa tiene un propósito específico en el mantenimiento de la separación de responsabilidades. Como hemos visto en capítulos anteriores, la capa de Dominio encapsula nuestras reglas de negocio centrales, mientras que la capa de Aplicación orquesta los casos de uso. Pero, ¿cómo conectamos estas capas puras centradas en el negocio con las necesidades prácticas de las interfaces de usuario, las bases de datos y los servicios externos? Este es el rol de la capa de Adaptadores de Interfaz.

> **Figura 6.1**: Capa de Adaptadores de Interfaz con sus componentes principales.

En la siguiente sección nos sumergiremos en los detalles del rol de la capa de Adaptadores de Interfaz y veremos ejemplos de esta capa en nuestra aplicación de gestión de tareas.

#### El rol de la capa de Adaptadores de Interfaz en Clean Architecture

La capa de Adaptadores de Interfaz actúa como un conjunto de traductores entre el núcleo de nuestra aplicación y los detalles externos, tales como un framework web o una interfaz de línea de comandos. Esta capa es crucial porque nos permite mantener límites arquitectónicos limpios a la vez que habilita una interacción práctica con las cuestiones externas. Al situarse entre la capa de Aplicación y las interfaces externas, asegura que:

- Nuestra lógica de negocio central permanezca pura y enfocada.
- Las cuestiones externas no puedan filtrarse hacia las capas internas.
- Los cambios en las interfaces externas no afecten a nuestra lógica central.
- Múltiples interfaces puedan interactuar con nuestro sistema de manera consistente.

El principio clave que rige esta capa es la **Regla de Dependencia** (**Dependency Rule**): las dependencias deben apuntar hacia adentro, hacia las reglas de negocio centrales. La capa de Adaptadores de Interfaz aplica rígidamente esta regla asegurando que todas las traducciones mantengan los límites arquitectónicos adecuados.

#### Responsabilidades de la capa de Adaptadores de Interfaz

A medida que profundizamos en la capa de Adaptadores de Interfaz de Clean Architecture, resulta esencial comprender sus responsabilidades centrales. Del mismo modo que un traductor debe dominar ambos idiomas con los que trabaja, esta capa debe entender tanto el lenguaje preciso del núcleo de nuestra aplicación como los diversos dialectos de las interfaces externas. Estas responsabilidades forman dos flujos de datos distintos pero complementarios a través de nuestro sistema, requiriendo cada uno un manejo cuidadoso para mantener nuestros límites arquitectónicos.

> **Figura 6.2**: Flujo bidireccional de datos a través de la capa de Adaptadores de Interfaz.

En la Figura 6.2, vemos que la capa de Adaptadores de Interfaz gestiona el flujo bidireccional de datos entre el núcleo de nuestra aplicación y las cuestiones externas:

- **Flujo de datos entrante (*Inbound data flow*)**:
  - Conversión de solicitudes externas a formatos específicos de la aplicación.
  - Asegurar que los datos cumplan con los requisitos de la aplicación.
  - Coordinación con los casos de uso para ejecutar operaciones.
- **Flujo de datos saliente (*Outbound data flow*)**:
  - Transformación de los resultados de la aplicación para consumo externo.
  - Provisión de formatos de datos adecuados para la interfaz.
  - Mantenimiento de la separación entre la lógica central y las interfaces externas.

Estas responsabilidades forman la base para los componentes específicos que examinaremos a continuación.

#### Límites entre la capa de Adaptadores de Interfaz y la capa de Aplicación

Al trabajar por primera vez con Clean Architecture, es común preguntarse sobre la distinción entre la transformación de datos en la capa de Adaptadores de Interfaz frente a la capa de Aplicación. Después de todo, ambas capas parecen encargarse de la conversión de datos. Sin embargo, estas capas cumplen propósitos fundamentalmente diferentes en nuestra arquitectura, y comprender estas diferencias es crucial para mantener límites limpios en nuestro sistema.

Aunque tanto la capa de Adaptadores de Interfaz como la capa de Aplicación gestionan la transformación de datos, sirven a diferentes propósitos y mantienen diferentes límites:

- **Capa de Aplicación**:
  - Transforma entre entidades de dominio y formatos específicos de casos de uso.
  - Se enfoca en la coordinación de reglas de negocio.
  - Trabaja con tipos y estructuras específicas del dominio.
- **Capa de Adaptadores de Interfaz**:
  - Transforma entre formatos de casos de uso y necesidades de interfaces externas.
  - Se enfoca en la coordinación de interfaces externas.
  - Trabaja con formatos específicos de interfaz y tipos primitivos.

Esta clara separación asegura que nuestro sistema mantenga límites sólidos entre su lógica de negocio central y las interfaces externas.

#### Componentes clave y sus relaciones

Con una comprensión de las responsabilidades y límites de la capa de Adaptadores de Interfaz, ahora podemos examinar los componentes específicos que implementan estos conceptos. Estos componentes trabajan juntos como un equipo bien orquestado, desempeñando cada uno un papel específico en el mantenimiento de nuestros límites arquitectónicos a la vez que posibilitan una interacción práctica con el sistema. Aunque exploraremos implementaciones detalladas más adelante en este capítulo, comprender cómo colaboran estos componentes proporciona un contexto esencial para nuestro diseño de Clean Architecture.

La capa de Adaptadores de Interfaz implementa sus responsabilidades a través de tres componentes clave:

- **Controladores (*Controllers*)**: manejan el flujo entrante, sirviendo como puntos de entrada para solicitudes externas a nuestro sistema. Aseguran que los datos que ingresan al núcleo de nuestra aplicación cumplan con los requisitos del sistema, protegiendo al mismo tiempo a nuestros casos de uso de cuestiones externas.
- **Presentadores (*Presenters*)**: gestionan el flujo saliente, transformando los resultados de los casos de uso en formatos adecuados para el consumo externo. La capa de Adaptadores de Interfaz define las interfaces de los presentadores, estableciendo el contrato que deben seguir tanto los casos de uso como las implementaciones concretas de presentadores.
- **Modelos de vista (*View models*)**: sirven como portadores de datos entre presentadores y vistas, conteniendo únicamente tipos primitivos y estructuras de datos simples. Esta simplicidad asegura que las vistas puedan consumir fácilmente los datos formateados manteniendo límites arquitectónicos limpios.

Estos componentes interactúan en un flujo cuidadosamente orquestado que siempre respeta la Regla de Dependencia:

1. Las solicitudes externas fluyen a través de los controladores.
2. Los controladores coordinan con los casos de uso.
3. Los casos de uso devuelven resultados a través de interfaces definidas.
4. Los presentadores formatean los resultados en modelos de vista (*view models*).
5. Las vistas consumen los datos formateados.

Esta interacción cuidadosamente orquestada asegura que nuestro sistema mantenga límites limpios sin dejar de ser práctico y mantenible.

#### Principios de diseño de interfaces

Al diseñar interfaces en la capa de Adaptadores de Interfaz, debemos equilibrar los límites arquitectónicos limpios con las preocupaciones prácticas de implementación. Como vimos en el [Capítulo 5](https://subscription.packtpub.com/book/programming/9781836642893/5) con los modelos de solicitud/respuesta, un diseño cuidadoso de interfaces permite un flujo fluido de datos al tiempo que mantiene una separación adecuada entre capas. Los principios que guían el diseño de interfaces en esta capa nos ayudan a lograr este equilibrio a la vez que nos adherimos a los principios fundamentales de Clean Architecture.

Tres principios clave moldean nuestro diseño de interfaces:

- **La Regla de Dependencia (*Dependency Rule*)** tiene prioridad en todas las decisiones de diseño. Todas las dependencias deben apuntar hacia adentro, hacia los casos de uso y las entidades. Esto significa que nuestros adaptadores de interfaz dependen de interfaces de aplicación (como el `CreateTaskUseCase` que vimos en el [Capítulo 5](https://subscription.packtpub.com/book/programming/9781836642893/5)), pero la aplicación nunca depende de nuestros adaptadores. Esta regla asegura que los cambios en las interfaces externas no puedan afectar a nuestra lógica de negocio central.
- **El Principio de Responsabilidad Única (*Single Responsibility Principle - SRP*)** guía los límites de los componentes. Cada adaptador maneja un tipo específico de transformación: los controladores gestionan la validación y conversión de entrada, mientras que los presentadores administran el formateo de salida. Esta separación hace que nuestro sistema sea más fácil de mantener y modificar. Por ejemplo, un `TaskController` se enfoca únicamente en validar y convertir entradas relacionadas con tareas, mientras que un `TaskPresenter` maneja solo el formateo de datos de tareas para su visualización.
- **El Principio de Segregación de Interfaces (*Interface Segregation Principle - ISP*)** asegura que nuestras interfaces permanezcan enfocadas y cohesivas. En lugar de crear interfaces grandes y monolíticas, diseñamos interfaces pequeñas y específicas para un propósito que atienden distintas necesidades de los clientes. Por ejemplo, en lugar de una única interfaz grande `TaskOperations`, podríamos tener interfaces separadas para la creación, compleción y consulta de tareas. Esta granularidad proporciona flexibilidad y hace que nuestro sistema sea más adaptable al cambio.

Al seguir estos principios, creamos interfaces que acortan eficazmente la brecha entre nuestra lógica de negocio central limpia y enfocada y las necesidades prácticas de las interfaces externas. A medida que exploremos implementaciones específicas en las siguientes secciones, veremos cómo estos principios guían nuestras decisiones de diseño y conducen a un código más mantenible.

---

### Sección 6.3: Implementación de controladores en Python

Habiendo establecido los fundamentos teóricos de la capa de Adaptadores de Interfaz, nos centramos ahora en la implementación práctica utilizando Python. Las características del lenguaje Python ofrecen varios mecanismos elegantes para implementar los patrones de controlador de Clean Architecture. A través de data classes, clases base abstractas (ABCs) y sugerencias de tipo (*type hints*), podemos crear límites de interfaz claros y mantenibles mientras mantenemos nuestro código pythónico.

Aunque Clean Architecture proporciona un conjunto de principios y patrones, no prescribe un enfoque de implementación rígido. A medida que avancemos, recuerda que esto representa una implementación posible de los principios de Clean Architecture; la clave reside en mantener límites limpios y la separación de responsabilidades, independientemente de los detalles específicos de implementación.

#### Responsabilidades y patrones de los controladores

Como vimos en nuestro examen de los componentes de la capa de Adaptadores de Interfaz, los controladores en Clean Architecture tienen un conjunto enfocado de responsabilidades: aceptan entradas de fuentes externas, validan y transforman esas entradas en el formato que esperan nuestros casos de uso, coordinan la ejecución de los casos de uso y manejan los resultados adecuadamente.

Examinemos una implementación concreta que demuestra estos principios:

```python
from dataclasses import dataclass


@dataclass
class TaskController:
    create_use_case: CreateTaskUseCase
    # ... additional use cases as needed
    presenter: TaskPresenter

    def handle_create(
        self, title: str, description: str
    ) -> OperationResult[TaskViewModel]:
        try:
            request = CreateTaskRequest(title=title, description=description)
            result = self.create_use_case.execute(request)

            if result.is_success:
                view_model = self.presenter.present_task(result.value)
                return OperationResult.succeed(view_model)

            error_vm = self.presenter.present_error(
                result.error.message, str(result.error.code.name)
            )
            return OperationResult.fail(error_vm.message, error_vm.code)

        except ValueError as e:
            error_vm = self.presenter.present_error(str(e), "VALIDATION_ERROR")
            return OperationResult.fail(error_vm.message, error_vm.code)
```

Este controlador demuestra varios principios clave de Clean Architecture. En primer lugar, observa cómo depende únicamente de dependencias inyectadas: tanto el caso de uso como el presentador se construyen en otro lugar y se introducen en el controlador mediante inyección a través del constructor.

Para comprender por qué este patrón de inyección de dependencias es tan importante, considera este contraejemplo (*anti-example*):

```python
# Anti-example: Tightly coupled controller


class TightlyCoupledTaskController:

    def __init__(self):

        # Direct instantiation creates tight coupling

        self.use_case = TaskUseCase(SqliteTaskRepository())

        self.presenter = CliTaskPresenter()

    def handle_create(self, title: str, description: str):

        # Implementation details...

        pass
```

Este contraejemplo o antiejemplo demuestra varios problemas:

- La instanciación directa de clases concretas crea un acoplamiento fuerte (*tight coupling*).
- El controlador sabe demasiado sobre los detalles de implementación.
- Las pruebas se vuelven difíciles, ya que las dependencias no se pueden sustituir.
- Los cambios en las implementaciones obligan a realizar cambios en el controlador.

Volviendo a nuestra implementación limpia, el método `handle_create` muestra las responsabilidades centrales del controlador en acción. Comienza aceptando tipos primitivos (cadenas de texto para `title` y `description`) desde el mundo exterior, manteniendo la interfaz simple e independiente de cualquier framework (*framework-agnostic*). Estas entradas luego se transforman en un objeto de solicitud (*request object*) adecuado, validando y formateando los datos antes de que lleguen a nuestro caso de uso.

Por brevedad, solo estamos mostrando la implementación de `handle_create`, pero en la práctica, este controlador tendría casos de uso adicionales inyectados (como `complete_use_case`, `set_priority_use_case`, etc.) y los correspondientes métodos manejadores implementados. Este patrón de inyección de dependencias e implementación de manejadores se mantiene consistente en todas las operaciones del controlador.

La estrategia de manejo de errores del controlador es especialmente destacable. Captura los errores de validación antes de que lleguen al caso de uso y maneja tanto los resultados exitosos como los fallidos de la ejecución del caso de uso. En todos los casos, utiliza el presentador para formatear las respuestas adecuadamente para el consumo externo, devolviéndolas envueltas en un `OperationResult` que hace explícitos los casos de éxito y de fallo. Este patrón se basa en el tipo de resultado que introdujimos en el [Capítulo 5](https://subscription.packtpub.com/book/programming/9781836642893/5), agregando soporte para modelos de vista para un formateo específico de la interfaz. Discutiremos el uso de `OperationResult` con más detalle en la sección *Construcción de presentadores para el formateo de datos*.

Esta clara separación de responsabilidades asegura que nuestra lógica de negocio permanezca ajena a cómo está siendo invocada, a la vez que proporciona una interfaz robusta y mantenible para clientes externos.

#### Trabajo con modelos de solicitud en controladores

Vimos `CreateTaskRequest` anteriormente en nuestro examen de `TaskController`, y en la cobertura de la capa de Aplicación en el [Capítulo 5](https://subscription.packtpub.com/book/programming/9781836642893/5). Ahora examinemos más de cerca cómo los controladores trabajan con estos modelos de solicitud para mantener límites limpios entre la entrada externa y nuestros casos de uso:

```python
from dataclasses import dataclass
from typing import Optional


@dataclass(frozen=True)
class CreateTaskRequest:
    """Request data for creating a new task."""

    title: str

    description: str

    due_date: Optional[str] = None

    priority: Optional[str] = None

    def to_execution_params(self) -> dict:
        """Convert request data to use case parameters."""

        params = {
            "title": self.title.strip(),
            "description": self.description.strip(),
        }

        if self.priority:

            params["priority"] = Priority[self.priority.upper()]

        return params
```

Aunque la capa de Aplicación define estos modelos de solicitud, los controladores son responsables de su correcta instanciación y uso. El controlador asegura que la validación de entrada ocurra antes de la ejecución del caso de uso:

```python
# In TaskController

try:

    request = CreateTaskRequest(title=title, description=description)

    # Request is now validated and properly formatted

    result = self.create_use_case.execute(request)

except ValueError as e:

    # Handle validation errors before they reach use cases

    return OperationResult.fail(str(e), "VALIDATION_ERROR")
```

Esta separación asegura que nuestros casos de uso solo reciban datos debidamente validados y formateados, manteniendo límites arquitectónicos limpios al tiempo que proporciona un manejo robusto de la entrada.

#### Mantenimiento de la independencia del controlador

La efectividad de nuestra capa de Adaptadores de Interfaz depende en gran medida de mantener un aislamiento adecuado entre nuestros controladores y tanto las cuestiones externas como las internas.

Veamos más de cerca cómo nuestro `TaskController` logra esta independencia:

```python
from dataclasses import dataclass


@dataclass
class TaskController:
    # Application layer interface
    create_use_case: CreateTaskUseCase
    # Interface layer abstraction
    presenter: TaskPresenter
```

Esta simple estructura de dependencias demuestra varios principios clave. En primer lugar, el controlador depende únicamente de abstracciones; no sabe nada sobre implementaciones concretas ni del caso de uso ni del presentador.

Tomémonos un momento para aclarar a qué nos referimos con abstracciones en Python. Como veremos pronto, `TaskPresenter` sigue un patrón clásico de interfaz utilizando la clase `ABC` de Python, estableciendo un contrato formal de interfaz. Para casos de uso como `CreateTaskUseCase`, aprovechamos el tipado de pato (*duck typing*) de Python: dado que cada caso de uso solo necesita un método `execute` con parámetros y tipos de retorno definidos, cualquier clase que proporcione este método cumple con el contrato de interfaz sin requerir la formalidad de una `ABC`.

Esta flexibilidad para definir interfaces es una de las fortalezas de Python. Podemos elegir interfaces formales `ABC` cuando necesitamos imponer contratos complejos o confiar en el *duck typing* para interfaces más simples. Es la elección del desarrollador según el estilo que prefiera. Ambos enfoques mantienen los principios de dependencia de Clean Architecture mientras se mantienen idiomáticos en Python.

Haciendo un inventario mental, observa lo que falta en nuestro controlador:

- No hay importaciones de frameworks web ni decoradores.
- No hay consideraciones de bases de datos o almacenamiento.
- No hay instanciación directa de dependencias.
- No hay conocimiento de implementaciones concretas de vistas.

Este cuidadoso aislamiento significa que nuestro controlador puede ser utilizado por cualquier mecanismo de entrega, ya sea una API web, una interfaz de línea de comandos (CLI) o un consumidor de colas de mensajes. Considera lo que sucede cuando violamos este aislamiento:

```python
# Anti-example: Controller with framework coupling

from fastapi import FastAPI, Request, HTTPException
from fastapi.responses import JSONResponse


class WebTaskController:

    def __init__(self, app: FastAPI):

        # Controller now tightly coupled to FastAPI
        self.app = app
        # Direct instantiation too!
        self.create_use_case = CreateTaskUseCase()

    async def handle_create(self, request: Request):

        try:
            data = await request.json()
            result = self.create_use_case.execute(data)

            return JSONResponse(status_code=201, content={"task": result})

        except ValidationError as e:

            raise HTTPException(status_code=400, detail=str(e))
```

Este contraejemplo viola nuestros principios de aislamiento al:

- Importar y depender de un framework web específico.
- Manejar aspectos específicos de HTTP.
- Mezclar el manejo de errores del framework con la lógica de negocio.

La decisión sobre cómo exponer la funcionalidad de nuestro controlador pertenece a la capa de Frameworks. En el [Capítulo 7](https://subscription.packtpub.com/book/programming/9781836642893/7), veremos cómo crear adaptadores específicos de framework adecuados que envuelvan nuestra implementación limpia del controlador. Esto nos permite mantener límites arquitectónicos limpios mientras seguimos aprovechando todas las capacidades de frameworks como FastAPI, Click para línea de comandos o bibliotecas de colas de mensajes.

Las interfaces de las que depende nuestro controlador demuestran la cuidadosa atención de Clean Architecture a los límites: las interfaces de casos de uso definidas por la capa de Aplicación establecen nuestras dependencias hacia adentro, mientras que las interfaces de presentador definidas en nuestra capa de Adaptadores de Interfaz nos dan control sobre el flujo de datos hacia afuera. Esta cuidadosa disposición de interfaces asegura que mantengamos la Regla de Dependencia mientras mantenemos nuestro sistema flexible y adaptable.

---

### Sección 6.4: Aplicación de límites a través de adaptadores de interfaz

Si bien nuestro examen de los controladores demostró cómo manejar las solicitudes entrantes, los límites de interfaz de Clean Architecture requieren una atención cuidadosa al flujo de datos en ambas direcciones. En esta sección, exploraremos patrones para mantener límites limpios en todo nuestro sistema, enfocándonos particularmente en el manejo explícito de los casos de éxito y de fallo. Estos patrones complementan a nuestros controladores y presentadores, asegurando que toda comunicación a través de los límites permanezca clara y mantenible.

#### Patrones explícitos de éxito/fallo en los límites

En nuestros límites arquitectónicos, necesitamos formas claras y consistentes de manejar tanto las operaciones exitosas como los fallos. Las operaciones pueden fallar por muchas razones —entradas inválidas, violaciones de reglas de negocio o errores del sistema— y cada tipo de fallo puede necesitar un manejo diferente por parte de la interfaz externa. Del mismo modo, las operaciones exitosas necesitan proporcionar sus resultados en un formato adecuado para la interfaz que los solicitó. Hemos visto este mecanismo en acción en los ejemplos de controlador mostrados anteriormente:

```python
class TaskController:

    def handle_create(
        self,
        title: str,
        description: str
    ) -> OperationResult[TaskViewModel]:
```

El patrón `OperationResult` responde a estas necesidades proporcionando una forma estandarizada de manejar tanto los casos de éxito como los de fallo. Este patrón asegura que nuestros adaptadores de interfaz siempre comuniquen los resultados de forma explícita, haciendo imposible pasar por alto los casos de error y proporcionando una estructura clara para los escenarios de éxito:

```python
from dataclasses import dataclass


@dataclass
class OperationResult(Generic[T]):
    """Represents the outcome of controller operations."""

    _success: Optional[T] = None

    _error: Optional[ErrorViewModel] = None

    @classmethod
    def succeed(cls, value: T) -> "OperationResult[T]":
        """Create a successful result with the given view model."""

        return cls(_success=value)

    @classmethod
    def fail(cls, message: str, code: Optional[str] = None) -> "OperationResult[T]":
        """Create a failed result with error details."""

        return cls(_error=ErrorViewModel(message, code))
```

Observa cómo la clase se define como `OperationResult(Generic[T])`. Esto significa que nuestra clase puede trabajar con cualquier tipo `T`. Cuando instanciamos la clase, reemplazamos `T` con un tipo específico; por ejemplo, cuando escribimos `OperationResult[TaskViewModel]`, estamos diciendo: esta operación tendrá éxito con un `TaskViewModel` o fallará con un error (`ErrorViewModel`). Esta seguridad de tipos ayuda a detectar posibles errores de forma temprana, a la vez que clarifica la intención de nuestro código.

Este manejo explícito de los resultados proporciona una base para el cruce limpio de límites que veremos aplicado a lo largo de nuestros adaptadores de interfaz. A medida que pasemos a examinar los patrones de transformación de datos, veremos cómo esta claridad en el manejo de éxitos y fallos ayuda a mantener límites arquitectónicos limpios al tiempo que habilita una funcionalidad práctica.

Si observamos algo de código de aplicación (que reside en la capa de Frameworks), vemos cómo este `OperationResult` se puede utilizar para dirigir el flujo de la aplicación:

```python
# pseudo-code example of a CLI app working with a OperationResult 

result = app.task_controller.handle_create(title, description)   

if result.is_success: 

    task = result.success 

    print(f"{task.status_display} [{task.priority_display}] {task.title}") 

    return 0  

print(result.error.message, fg='red', err=True) 

    return 1
```

#### Flujos limpios de transformación de datos

A medida que los datos se mueven a través de nuestros límites arquitectónicos, experimentan varias transformaciones. Comprender estos flujos de transformación nos ayuda a mantener límites limpios a la vez que aseguramos que nuestro sistema siga siendo mantenible:

```python
# Example transformation flow in TaskController


def handle_create(self, title: str, description: str) -> OperationResult[TaskViewModel]:

    try:

        # 1. External input to request model

        request = CreateTaskRequest(title=title, description=description)

        # 2. Request model to domain operations

        result = self.use_case.execute(request)

        if result.is_success:

            # 3. Domain result to view model

            view_model = self.presenter.present_task(result.value)

            return OperationResult.succeed(view_model)

        # 4. Error handling and formatting

        error_vm = self.presenter.present_error(result.error.message, str(result.error.code.name))

        return OperationResult.fail(error_vm.message, error_vm.code)

    except ValueError as e:

        # 5. Validation error handling

        error_vm = self.presenter.present_error(str(e), "VALIDATION_ERROR")

        return OperationResult.fail(error_vm.message, error_vm.code)
```

Este ejemplo muestra una cadena completa de transformación:

1. Validación y conversión de entradas externas.
2. Ejecución del caso de uso con tipos de dominio.
3. Transformación del caso de éxito a modelo de vista (*view model*).
4. Manejo y formateo de casos de error.
5. Manejo de errores de validación.

Cada paso en esta cadena mantiene límites limpios a la vez que asegura que los datos se muevan adecuadamente entre las capas.

#### Adaptadores de interfaz y límites arquitectónicos

Aunque nos hemos centrado en los controladores y presentadores como adaptadores de interfaz clave, no toda interacción entre capas requiere un adaptador. Comprender cuándo se necesitan adaptadores ayuda a mantener Clean Architecture sin complejidad innecesaria.

```python
# Defined in Application layer


from abc import ABC, abstractmethod
from uuid import UUID


class TaskRepository(ABC):

    @abstractmethod
    def get(self, task_id: UUID) -> Task:
        """Retrieve a task by its ID."""

        pass


# Implemented directly in Infrastructure layer


class SqliteTaskRepository(TaskRepository):

    def get(self, task_id: UUID) -> Task:

        # Direct implementation of interface

        pass
```

Aquí no se necesita ningún adaptador porque:

- La capa de Aplicación define la interfaz exacta requerida.
- La implementación puede cumplir directamente con esta interfaz.
- No se requiere ninguna conversión de formato de datos.
- La Regla de Dependencia se mantiene sin necesidad de adaptación.

Esto difiere de los controladores y presentadores, que deben manejar formatos y protocolos externos variables. La pregunta clave a la hora de decidir si se necesita un adaptador es: *¿Requiere esta interacción una conversión de formato entre capas?* Si la capa externa puede trabajar directamente con la interfaz definida por la capa interna, un adaptador en la capa de Interfaz puede no ser necesario.

Esta distinción nos ayuda a mantener los principios de Clean Architecture al tiempo que evitamos abstracciones innecesarias. Al comprender cuándo se necesitan adaptadores, podemos crear sistemas más mantenibles que respeten los límites arquitectónicos sin sobrecomplicar nuestro diseño.

---

### Sección 6.5: Construcción de presentadores para el formateo de datos

A lo largo de este capítulo, nos hemos referido a los presentadores como componentes clave de nuestra capa de Adaptadores de Interfaz. Ahora los examinaremos en detalle, viendo cómo mantienen límites arquitectónicos limpios mientras preparan los datos del dominio para el consumo externo.

Los presentadores complementan a nuestros controladores, manejando el flujo de datos saliente del mismo modo que los controladores gestionan las solicitudes entrantes. Al implementar el patrón *Humble Object* (objeto humilde), los presentadores nos ayudan a crear sistemas más testeables y mantenibles, al tiempo que mantienen nuestras vistas simples y enfocadas.

#### Comprensión del patrón Humble Object

El patrón *Humble Object* aborda un desafío común en Clean Architecture: cómo manejar la lógica de presentación, que a menudo se resiste a las pruebas unitarias, manteniendo al mismo tiempo límites arquitectónicos limpios.

El término *humble object* proviene de la estrategia de hacer que un componente sea lo más simple y desprovisto de lógica compleja como sea posible. En contextos de presentación, esto significa crear una vista extremadamente básica que no hace más que mostrar datos preformateados. La vista se vuelve «humilde» por diseño, conteniendo una inteligencia mínima.

Por ejemplo, una vista humilde podría ser:

- Una plantilla HTML simple que renderiza datos preformateados.
- Un componente de React que solo muestra las *props* pasadas.
- Una función de visualización CLI que imprime cadenas formateadas.

El patrón divide las responsabilidades entre dos componentes:

- Una **vista humilde** (*humble view*) que contiene una lógica mínima difícil de probar.
- Un **presentador** (*presenter*) que contiene toda la lógica de presentación en una forma fácilmente comprobable mediante pruebas (*testable*).

Considera cómo nuestro sistema de gestión de tareas podría mostrar información de tareas en una CLI:

```python
# The "humble" view - simple, minimal logic, hard to test


def display_task(task_vm: TaskViewModel):

    print(f"{task_vm.status_display} [{task_vm.priority_display}] {task_vm.title}")

    if task_vm.due_date_display:

        print(f"Due: {task_vm.due_date_display}")
```

Todas las decisiones de formateo —cómo mostrar el estado, los niveles de prioridad, las fechas— residen en nuestro presentador, no en el modelo de vista (`TaskViewModel`) en sí. Esta separación aporta varios beneficios:

- Las vistas permanecen simples y enfocadas en la visualización.
- La lógica de presentación se mantiene comprobable mediante pruebas.
- Las reglas de negocio permanecen aisladas de las preocupaciones de visualización.
- Múltiples interfaces pueden compartir la lógica de formateo.

Vale la pena señalar que el énfasis en los presentadores puede variar según tus necesidades específicas. Si estás construyendo una API en Python que sirve datos a un frontend de JavaScript, es posible que requieras una lógica de presentación mínima. Sin embargo, en aplicaciones Python de pila completa (*full-stack*) que utilizan frameworks como Django o Flask, unos presentadores robustos ayudan a mantener una separación limpia entre la lógica de negocio y las cuestiones de visualización. Comprender el patrón te permite tomar decisiones informadas según tus circunstancias.

#### Definición de interfaces de presentador

El éxito de Clean Architecture se basa en gran medida en interfaces bien definidas en los límites arquitectónicos. Para los presentadores, estas interfaces establecen contratos claros para transformar datos de dominio en formatos listos para la presentación:

```python
from abc import ABC, abstractmethod
from typing import Optional


class TaskPresenter(ABC):
    """Abstract base presenter for task-related output."""

    @abstractmethod
    def present_task(self, task_response: TaskResponse) -> TaskViewModel:
        """Convert task response to view model."""

        pass

    @abstractmethod
    def present_error(self, error_msg: str, code: Optional[str] = None) -> ErrorViewModel:
        """Format error message for display."""

        pass
```

Esta interfaz, definida en nuestra capa de Adaptadores de Interfaz, cumple varios propósitos clave:

- Establece un contrato claro para la presentación de tareas.
- Permite múltiples implementaciones de interfaz.
- Mantiene la Regla de Dependencia al mantener la lógica de dominio ajena a los detalles de presentación.
- Facilita las pruebas a través de una abstracción clara.

Observa cómo la interfaz utiliza tipos específicos del dominio (`TaskResponse`) como entrada, pero devuelve tipos específicos de la vista (`TaskViewModel`). Este cruce de límites es donde transformamos los conceptos del dominio en formatos amigables para la presentación.

#### Trabajo con modelos de vista (View Models)

Los modelos de vista (*view models*) sirven como portadores de datos entre presentadores y vistas, asegurando una separación limpia entre la lógica de presentación y las cuestiones de visualización. Encapsulan los datos formateados de modo que cualquier implementación de vista pueda consumirlos fácilmente:

```python
from dataclasses import dataclass
from typing import Optional


@dataclass(frozen=True)
class TaskViewModel:
    """View-specific representation of a task."""

    id: str
    title: str
    description: str
    status_display: str  # Pre-formatted for display
    priority_display: str  # Pre-formatted for display
    due_date_display: Optional[str]  # Pre-formatted for display
    project_display: Optional[str]  # Pre-formatted project context
    completion_info: Optional[str]  # Pre-formatted completion details
```

Varios principios clave guían nuestro diseño de modelos de vista:

- Usar únicamente tipos primitivos (cadenas de texto, números, booleanos).
- Preformatear todo el texto de visualización.
- No hacer suposiciones sobre el mecanismo de visualización.
- Permanecer inmutables (nótese el `frozen=True`).
- Incluir únicamente los datos necesarios para la visualización.

Esta simplicidad asegura que nuestras vistas permanezcan verdaderamente humildes: solo necesitan leer y mostrar estos valores preformateados, sin ningún conocimiento de los conceptos del dominio ni de las reglas de formateo.

#### Implementación de presentadores concretos

Con nuestras interfaces de presentador y modelos de vista definidos, podemos implementar presentadores concretos para necesidades específicas de interfaz. Estos presentadores concretos se implementan en la capa de Frameworks y Drivers, pero te damos un vistazo previo aquí para dar contexto. Examinemos una implementación de presentador específica para CLI:

```python
class CliTaskPresenter(TaskPresenter):
    """CLI-specific task presenter."""

    def present_task(self, task_response: TaskResponse) -> TaskViewModel:
        """Format task for CLI display."""

        return TaskViewModel(
            id=str(task_response.id),
            title=task_response.title,
            description=task_response.description,
            status_display=self._format_status(task_response.status),
            priority_display=self._format_priority(task_response.priority),
            due_date_display=self._format_due_date(task_response.due_date),
            project_display=self._format_project(task_response.project_id),
            completion_info=self._format_completion_info(
                task_response.completion_date, task_response.completion_notes
            ),
        )
```

El método `present_task` transforma nuestro `TaskResponse` específico del dominio en un `TaskViewModel` amigable para la vista. Para respaldar esta transformación, el presentador implementa varios métodos de formateo privados que manejan aspectos específicos de los datos:

```python
class CliTaskPresenter(TaskPresenter):
    # continuing from above
    def _format_due_date(self, due_date: Optional[datetime]) -> str:
        """Format due date, indicating if task is overdue."""

        if not due_date:

            return "No due date"

        is_overdue = due_date < datetime.now(timezone.utc)

        date_str = due_date.strftime("%Y-%m-%d")

        return f"OVERDUE - Due: {date_str}" if is_overdue else f"Due: {date_str}"

    def present_error(self, error_msg: str, code: Optional[str] = None) -> ErrorViewModel:
        """Format error message for CLI display."""

        return ErrorViewModel(message=error_msg, code=code)
```

Esta implementación demuestra varios principios clave de Clean Architecture:

- Toda la lógica de formateo reside en el presentador, no en las vistas.
- Los conceptos del dominio (como `TaskStatus`) se convierten en cadenas de texto para visualización.
- El manejo de errores se mantiene consistente con los casos de éxito.
- El formateo específico de la interfaz (CLI en este caso) se mantiene aislado.

Los métodos de formateo del presentador siguen siendo altamente comprobables mediante pruebas (*testable*): podemos verificar que las tareas vencidas se marquen adecuadamente, que las fechas se formateen correctamente y que los mensajes de error mantengan consistencia. Esta facilidad de prueba contrasta fuertemente con la comprobación directa de componentes de interfaz de usuario (UI), demostrando un beneficio clave del patrón *Humble Object*.

#### Flexibilidad de implementación

Si estás construyendo una API que sirve principalmente JSON a un frontend en JavaScript, es posible que necesites una lógica de presentación mínima. El patrón de presentador se vuelve más valioso cuando necesitas un formateo complejo o admitir múltiples tipos de interfaz.

En el [Capítulo 7](https://subscription.packtpub.com/book/programming/9781836642893/7), veremos cómo diferentes interfaces (CLI, web o APIs) pueden implementar sus propios presentadores mientras comparten esta arquitectura común. Esta flexibilidad demuestra cómo la cuidadosa atención de Clean Architecture a los límites permite la evolución del sistema sin comprometer la lógica de negocio central.

A través de nuestra exploración de controladores y presentadores, ahora hemos implementado una capa completa de Adaptadores de Interfaz para nuestro sistema de gestión de tareas. Tomémonos un momento para revisar nuestro progreso arquitectónico examinando la estructura que hemos construido a lo largo de los Capítulos 4 a 6:

> **Figura 6.3**: Estructura de carpetas con todas las capas en su lugar.

Esta estructura refleja las capas concéntricas de Clean Architecture. Nuestra capa de Dominio, establecida en el [Capítulo 4](https://subscription.packtpub.com/book/programming/9781836642893/4), permanece pura y enfocada en las reglas de negocio. La capa de Aplicación, agregada en el [Capítulo 5](https://subscription.packtpub.com/book/programming/9781836642893/5), orquesta estos objetos de dominio para cumplir con casos de uso específicos. Ahora, con nuestra capa de Adaptadores de Interfaz, hemos implementado los controladores y presentadores que traducen entre nuestra lógica de negocio central y las cuestiones externas, manteniendo límites limpios mientras posibilitan una interacción práctica con nuestro sistema. Consulta el repositorio complementario de GitHub ([https://github.com/PacktPublishing/Clean-Architecture-with-Python](https://github.com/PacktPublishing/Clean-Architecture-with-Python)) para ver un ejemplo de código más extenso de la aplicación de gestión de tareas utilizada a lo largo del libro.

---

### Sección 6.6: Resumen

En este capítulo, exploramos la capa de Adaptadores de Interfaz de Clean Architecture, implementando controladores y presentadores que mantienen límites limpios mientras permiten una interacción práctica con sistemas externos. Aprendimos cómo los controladores manejan las solicitudes entrantes, convirtiendo las entradas externas en formatos que nuestros casos de uso pueden procesar, mientras que los presentadores transforman los datos del dominio en formatos amigables para las vistas.

Utilizando nuestro sistema de gestión de tareas como ejemplo, vimos cómo implementar controladores que permanecen independientes de fuentes de entrada específicas y presentadores que separan la lógica de formateo de los detalles de implementación de la vista. Nos basamos en el patrón de resultados del [Capítulo 5](https://subscription.packtpub.com/book/programming/9781836642893/5), introduciendo `OperationResult` para el manejo explícito de éxitos y fallos en nuestros límites arquitectónicos. El patrón *Humble Object* nos mostró cómo mantener una separación limpia entre la lógica de presentación y las vistas, mejorando tanto la capacidad de prueba como la mantenibilidad.

En el [Capítulo 7](https://subscription.packtpub.com/book/programming/9781836642893/7), exploraremos cómo implementar interfaces específicas que consuman nuestros controladores y presentadores. Aprenderás a crear interfaces web y de línea de comandos que interactúen con nuestro sistema manteniendo los límites limpios que hemos establecido.

---

### Sección 6.7: Lecturas complementarias

- **Clean DDD Lessons: Presenters** ([https://medium.com/unil-ci-software-engineering/clean-ddd-lessons-presenters-6f092308b75e](https://medium.com/unil-ci-software-engineering/clean-ddd-lessons-presenters-6f092308b75e)): Una discusión sobre enfoques de presentadores en Clean Architecture.
- **Implementing Clean Architecture—Are Asp.Net Controllers “Clean”?** ([https://www.plainionist.net/Implementing-Clean-Architecture-AspNet/](https://www.plainionist.net/Implementing-Clean-Architecture-AspNet/)): Un artículo en profundidad que analiza los pros y los contras de múltiples enfoques para implementar vistas en Clean Architecture.

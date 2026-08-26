# Parte 1: Fundamentos de Clean Architecture en Python

## Capítulo 3: Python mejorado con tipos: Fortaleciendo Clean Architecture

En el [Capítulo 2](https://subscription.packtpub.com/book/programming/9781836642893/2), exploramos los principios SOLID y su aplicación en Python, estableciendo una base para un código mantenible y flexible. A partir de esto, ahora nos enfocamos en una característica poderosa de Python: el tipado mediante sugerencias de tipo (*type hinting*).

Si bien el tipado dinámico de Python ofrece flexibilidad, a veces puede dar lugar a errores inesperados en proyectos complejos. El tipado mediante sugerencias de tipo proporciona una solución, combinando los beneficios del tipado dinámico con la robustez de la comprobación estática de tipos.

Este capítulo explora cómo el *type hinting* mejora las implementaciones de Clean Architecture, haciendo que el código sea más autodocumentado y menos propenso a errores. Veremos cómo las sugerencias de tipo respaldan los principios SOLID, particularmente en la creación de interfaces claras y en el refuerzo del Principio de Inversión de Dependencias (*Dependency Inversion Principle*).

Comenzaremos con el papel de la conciencia de tipos en el entorno dinámico de Python, para luego profundizar en los aspectos prácticos del sistema de tipado de Python. Finalmente, exploraremos herramientas automatizadas de comprobación estática de tipos para la detección temprana de problemas.

Al finalizar el capítulo, comprenderás cómo utilizar eficazmente las sugerencias de tipo en proyectos de Python, escribiendo código que sea más robusto, mantenible y alineado con los principios de Clean Architecture. Este conocimiento será crucial a medida que avancemos hacia la construcción de sistemas complejos y escalables en capítulos posteriores.

En este capítulo, vamos a cubrir los siguientes temas principales:

- **Comprensión de la conciencia de tipos en el entorno dinámico de Python**
- **Aprovechamiento del sistema de tipos de Python**
- **Aprovechamiento de herramientas automatizadas de comprobación estática de tipos**

Comencemos nuestra exploración del *type hinting* en Python y su papel en el fortalecimiento de las implementaciones de Clean Architecture.

---

### Sección 3.1: Requisitos técnicos

Los ejemplos de código presentados en este capítulo y a lo largo del resto del libro se prueban con Python 3.13. Todos los ejemplos se pueden encontrar en el repositorio complementario de GitHub del libro en [https://github.com/PacktPublishing/Clean-Architecture-with-Python](https://github.com/PacktPublishing/Clean-Architecture-with-Python). Este capítulo hace referencia a Visual Studio Code (VS Code). VS Code se puede descargar desde [https://code.visualstudio.com/download](https://code.visualstudio.com/download).

---

### Sección 3.2: Comprensión de la conciencia de tipos en el entorno dinámico de Python

Para apreciar plenamente el sistema de tipos de Python, es importante distinguir entre los lenguajes de tipado dinámico como Python y los lenguajes de tipado estático como Java o C++. En los lenguajes de tipado estático, las variables tienen un tipo fijo que se determina en tiempo de compilación. Python, como lenguaje de tipado dinámico, permite que las variables cambien de tipo durante el tiempo de ejecución, lo que ofrece una gran flexibilidad pero también introduce desafíos potenciales. Este tipado dinámico es tanto una bendición como un desafío al implementar Clean Architecture. Si bien ofrece flexibilidad y un desarrollo rápido, también puede dar lugar a interfaces poco claras y dependencias ocultas, problemas que Clean Architecture busca abordar. En esta sección, exploraremos cómo la conciencia de tipos puede mejorar nuestras implementaciones de Clean Architecture sin sacrificar la naturaleza dinámica de Python.

#### Evolución del tipado en Python

El enfoque de Python respecto al tipado ha evolucionado significativamente con el tiempo. Aunque originalmente era un lenguaje puramente de tipado dinámico, Python introdujo el tipado estático opcional con la incorporación de la sintaxis de sugerencias de tipo (*type hinting*) en Python 3.5 (2015) a través de la **PEP 484**. Esta introducción estuvo motivada por la creciente complejidad de las aplicaciones en Python, particularmente en proyectos a gran escala donde los principios de Clean Architecture resultan más beneficiosos.

Esta estandarización de las sugerencias de tipo a través de la PEP 484 marcó un hito significativo en la evolución de Python, proporcionando un enfoque unificado para añadir información de tipos al código Python. Allanó el camino para una adopción más amplia de la comprobación estática de tipos en el ecosistema de Python y el desarrollo de diversas herramientas e IDEs que aprovechan esta información de *type hinting*.

El enfoque de Python hacia el *type hinting* forma parte de una tendencia más amplia en los lenguajes dinámicos. JavaScript, por ejemplo, ha sido testigo del auge de TypeScript, un superconjunto tipado de JavaScript que se compila a JavaScript puro. Aunque tanto Python como TypeScript tienen como objetivo aportar los beneficios del tipado estático a los lenguajes dinámicos, sus enfoques difieren:

- **Integración**: Las sugerencias de tipo de Python están integradas en el propio lenguaje, mientras que TypeScript es un lenguaje independiente que se compila a JavaScript.
- **Opcionalidad**: Las sugerencias de tipo de Python son completamente opcionales y pueden adoptarse de forma gradual, mientras que TypeScript impone la comprobación de tipos de manera más estricta.

El éxito de TypeScript en el ecosistema de JavaScript valida aún más el valor de añadir información de tipos a los lenguajes dinámicos. Tanto el *type hinting* de Python como TypeScript demuestran cómo la combinación de la flexibilidad del tipado dinámico con la solidez del tipado estático puede dar lugar a bases de código más mantenibles y escalables.

La evolución de las sugerencias de tipo en Python fue impulsada por varios factores importantes. Mejora significativamente la legibilidad del código y sirve como una forma de autodocumentación, facilitando que los desarrolladores comprendan el uso previsto de variables y funciones. Esta mayor claridad es particularmente valiosa para mantener la separación de responsabilidades (*Separation of Concerns*) de Clean Architecture. Las sugerencias de tipo también permiten un mejor soporte por parte de los entornos de desarrollo integrados (IDE) y de las herramientas, facilitando un autocompletado de código y una detección de errores más precisos. Este soporte mejorado de herramientas es crucial cuando se trabaja con arquitecturas complejas, ayudando a los desarrolladores a navegar entre diferentes capas y componentes de manera más eficiente.

Además, el *type hinting* hace que la refactorización y el mantenimiento de grandes bases de código sean considerablemente más sencillos. En el contexto de Clean Architecture, donde nos esforzamos por crear sistemas adaptables al cambio, este beneficio es particularmente significativo.

Las sugerencias de tipo actúan como una red de seguridad durante los esfuerzos de refactorización a gran escala, ayudando a garantizar que los cambios en una parte del sistema no rompan inadvertidamente las interfaces o expectativas en otra parte.

Quizás lo más importante para nuestras implementaciones de Clean Architecture es que las sugerencias de tipo nos permiten detectar ciertos tipos de errores de forma más temprana en el proceso de desarrollo. Al hacer explícitas nuestras intenciones a través de anotaciones de tipo, podemos identificar problemas potenciales en tiempo de diseño o durante el análisis estático, en lugar de encontrarlos en tiempo de ejecución. Esta detección temprana de errores se alinea perfectamente con el objetivo de Clean Architecture de crear sistemas robustos y mantenibles.

A medida que profundicemos en los detalles de las sugerencias de tipo en las siguientes secciones, ten en cuenta que estas características son herramientas para mejorar nuestras implementaciones de Clean Architecture en Python. Ofrecen una forma de hacer que nuestros límites arquitectónicos sean más explícitos y que nuestro código sea más autodocumentado, todo ello conservando la flexibilidad y expresividad que hacen de Python un lenguaje tan poderoso para el desarrollo de software.

#### Tipado dinámico frente a sugerencias de tipo

Para comprender la importancia de las sugerencias de tipo en Python, es crucial distinguir entre el tipado dinámico fundamental de Python y el papel de las sugerencias de tipo. Estos dos conceptos tienen propósitos diferentes y operan en distintas etapas del proceso de desarrollo.

##### Tipado dinámico

En un lenguaje de tipado dinámico como Python, las variables pueden contener valores de cualquier tipo, y estos tipos pueden cambiar durante el tiempo de ejecución. Esta flexibilidad es una característica fundamental de Python. Veamos un ejemplo:

```python
x = 5  # x is an integer
x = "hello"  # Now x is a string
```

Esta flexibilidad permite un desarrollo rápido y un código expresivo, pero puede dar lugar a errores en tiempo de ejecución si no se gestiona con cuidado. Consideremos el siguiente ejemplo:

```python
def add_numbers(a, b):
    return a + b


result = add_numbers(5, 3)  # Works fine, result is 8
result = add_numbers(
    5, "3"
)  # Raises TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

En este caso, la función `add_numbers` funciona según lo esperado cuando se le proporcionan dos enteros, pero genera un `TypeError` cuando se le pasa un entero y una cadena de texto. Este error solo ocurre en tiempo de ejecución, lo que puede resultar problemático si se encuentra en una parte crítica de tu aplicación o si no es detectado por tu proceso de pruebas.

##### Sugerencias de tipo (*Type hinting*)

Las sugerencias de tipo permiten a los desarrolladores anotar variables, parámetros de función y valores de retorno con sus tipos esperados. Con respecto a las sugerencias de tipo, revisemos nuestra función simple para sumar números:

```python
def add_numbers(a: int, b: int) -> int:
    return a + b


result = add_numbers(5, 3)  # Works fine, result is 8
result = add_numbers(5, "3")  # IDE or type checker would flag this as an error
```

Desglosemos la sintaxis de *type hinting* utilizada en esta función:

- `a: int` y `b: int`: Estas anotaciones indican que se espera que tanto `a` como `b` sean enteros. Los dos puntos (`:`) se utilizan para separar el nombre del parámetro de su tipo.
- `-> int`: Esta notación de flecha después de la lista de parámetros de la función especifica el tipo de retorno. En este caso, indica que se espera que `add_numbers` devuelva un entero.

Estas anotaciones de tipo proporcionan información clara sobre las entradas y salidas esperadas de la función, haciendo que el código sea más autodocumentado y fácil de entender.

Los puntos clave sobre las sugerencias de tipo incluyen lo siguiente:

- No afectan el comportamiento en tiempo de ejecución de Python. Python sigue siendo de tipado dinámico.
- Sirven como documentación, haciendo más claras las intenciones del código.
- Permiten que las herramientas de análisis estático detecten posibles errores relacionados con los tipos antes del tiempo de ejecución.
- Mejoran el soporte del IDE para el autocompletado de código y la refactorización.

Las sugerencias de tipo liberan el poder de las herramientas de análisis estático para detectar posibles errores antes del tiempo de ejecución. Si bien Python en sí proporciona la sintaxis para las sugerencias de tipo, no las impone en tiempo de ejecución. El intérprete de Python trata las sugerencias de tipo como metadatos decorativos. Son herramientas de terceros como `mypy` o `pyright` las que realizan la comprobación estática de tipos real. Estas herramientas analizan tu código sin ejecutarlo, utilizando las sugerencias de tipo para inferir y comprobar los tipos en toda tu base de código. Se pueden ejecutar como comandos independientes, integrarse en IDEs para obtener retroalimentación en tiempo real, o incorporarse en pipelines de integración continua, lo que permite la comprobación de tipos en diversas etapas del desarrollo.

En la sección *Aprovechamiento de herramientas automatizadas de comprobación estática de tipos* de este capítulo, profundizaremos en cómo usar estas herramientas para realizar comprobaciones estáticas de tipos en toda tu base de código en puntos clave del flujo de trabajo del desarrollador.

#### Conciencia de tipos en Clean Architecture

La introducción de sugerencias de tipo es particularmente relevante para Clean Architecture. En el capítulo anterior, discutimos la importancia de interfaces claras y de la inversión de dependencias. Las sugerencias de tipo pueden desempeñar un papel crucial en la consecución de estos objetivos, haciendo que nuestros límites arquitectónicos sean más explícitos y fáciles de mantener.

Consideremos cómo las sugerencias de tipo pueden mejorar el ejemplo de `Shape` que introdujimos en el [Capítulo 2](https://subscription.packtpub.com/book/programming/9781836642893/2), aquí con una utilización más completa de *type hints*:

```python
import math
from abc import ABC, abstractmethod


class Shape(ABC):
    @abstractmethod
    def area(self) -> float:
        pass


class Rectangle(Shape):
    def __init__(self, width: float, height: float) -> None:
        self.width = width
        self.height = height

    def area(self) -> float:
        return self.width * self.height


class Circle(Shape):
    def __init__(self, radius: float) -> None:

        self.radius = radius

    def area(self) -> float:
        return math.pi * self.radius**2


class AreaCalculator:
    def calculate_area(self, shape: Shape) -> float:

        return shape.area()
```

Analicemos más de cerca este ejemplo:

- El método `area` en la clase `Shape` está anotado para devolver un `float`, comunicando claramente el tipo de retorno esperado para todas las implementaciones de figuras.
- Las clases `Rectangle` y `Circle` especifican que sus constructores esperan parámetros de tipo `float` y devuelven `None`. Esta anotación `-> None` indica explícitamente que los constructores no devuelven ningún valor, lo cual es implícito en Python pero queda explícito a través del *type hinting*.
- Los métodos concretos `area` en `Rectangle` y `Circle` están anotados para devolver `float`, adhiriéndose al contrato definido en la clase base abstracta `Shape`.
- La clase `AreaCalculator` establece explícitamente que su método `calculate_area` espera un objeto `Shape` como argumento y devuelve un `float`.

Estas sugerencias de tipo hacen que las interfaces sean más explícitas, ayudando a mantener los límites de Clean Architecture entre componentes. Es importante destacar que estas sugerencias de tipo no imponen nada en tiempo de ejecución. Más bien, sirven como documentación y permiten que las herramientas de análisis estático detecten posibles errores de tipo antes de la ejecución.

Proporcionan varios beneficios en el contexto de Clean Architecture:

- **Interfaces claras**: Las sugerencias de tipo hacen explícitos los contratos entre diferentes capas de tu arquitectura. En nuestro ejemplo, queda claro que cualquier `Shape` debe tener un método `area` que devuelva un `float`.
- **Inversión de dependencias**: Ayudan a hacer cumplir la Regla de Dependencia al definir claramente las interfaces abstractas de las que dependen los módulos de nivel superior. `AreaCalculator` depende de la abstracción `Shape`, no de implementaciones concretas.
- **Facilidad de prueba (*Testability*)**: Las sugerencias de tipo facilitan la creación y el uso de objetos simulados (*mocks*) que se ajusten a las interfaces esperadas. Para las pruebas, podríamos crear fácilmente un *mock* de `Shape` que cumpla con los requisitos de interfaz documentados.
- **Mantenibilidad**: A medida que tu proyecto crece, las sugerencias de tipo sirven como documentación viva, facilitando que los desarrolladores comprendan y modifiquen el código. Proporcionan una visión inmediata de los tipos esperados para los parámetros de los métodos y los valores de retorno.

Al aprovechar las sugerencias de tipo de esta manera, creamos una implementación más robusta de Clean Architecture. Las interfaces explícitamente documentadas y las dependencias claras hacen que nuestro código sea más autodocumentado y ayudan a detectar problemas relacionados con los tipos de forma temprana a través del análisis estático. A medida que construimos sistemas más complejos, estos beneficios se acumulan, dando como resultado una base de código más fácil de entender, modificar y extender. En la siguiente sección, exploraremos algunos desafíos y consideraciones a tener en cuenta al integrar sugerencias de tipo en tus diseños de Clean Architecture.

#### Desafíos y consideraciones

Al aprovechar las sugerencias de tipo en tus proyectos de Python, es importante tener en cuenta varias consideraciones clave:

- No reemplazan la necesidad de realizar pruebas adecuadas ni del manejo de errores.
- Existe una curva de aprendizaje para los desarrolladores que son nuevos en los conceptos de tipado estático.
- La incorporación planificada en el flujo de trabajo de desarrollo del equipo y en el pipeline de integración y despliegue continuos (CI/CD) es esencial para una adopción exitosa.

A medida que profundicemos en el sistema de tipado de Python en las siguientes secciones y en el resto del libro, exploraremos cómo aprovechar estas características para crear implementaciones de Clean Architecture más robustas, mantenibles y autodocumentadas. Veremos cómo la conciencia de tipos puede ayudarnos a crear límites más claros entre las capas arquitectónicas, hacer que nuestras dependencias sean más explícitas y detectar problemas potenciales de forma más temprana en el proceso de desarrollo.

Recuerda que el objetivo no es convertir a Python en un lenguaje de tipado estático, sino utilizar la conciencia de tipos como una herramienta para mejorar nuestros diseños de Clean Architecture. Al final de este capítulo, tendrás una comprensión sólida de cómo equilibrar la naturaleza dinámica de Python con los beneficios de la conciencia de tipos en tus implementaciones de Clean Architecture.

---

### Sección 3.3: Aprovechamiento del sistema de tipos de Python

En el ámbito de Clean Architecture, el papel de un sistema de tipos robusto va mucho más allá de la simple prevención de errores. Sirve como una herramienta poderosa para expresar y hacer cumplir los límites arquitectónicos, respaldando principios clave como la abstracción, el polimorfismo y la inversión de dependencias. El sistema de tipado de Python, cuando se aprovecha eficazmente, se convierte en un activo invaluable para implementar estos conceptos cruciales.

A medida que comenzamos a considerar características más avanzadas del sistema de tipado de Python, veremos cómo pueden mejorar significativamente nuestras implementaciones de Clean Architecture. Estas características nos permiten crear interfaces más expresivas y precisas entre las diferentes capas de nuestra aplicación, lo que da lugar a un código que no solo es más robusto, sino también más autodocumentado y mantenible.

En esta sección, exploraremos una variedad de características de tipado, desde alias de tipos y tipos de unión hasta tipos literales y `TypedDict`. Luego veremos cómo se pueden aplicar para respaldar los principios SOLID en nuestros diseños de Clean Architecture. Al final de esta sección, tendrás una comprensión integral de cómo utilizar el sistema de tipos de Python para crear límites arquitectónicos más limpios y mantenibles.

Comenzaremos con una revisión de las sugerencias de tipo básicas, luego profundizaremos en características más avanzadas y, finalmente, veremos cómo estas características respaldan los principios SOLID en el contexto de Clean Architecture.

#### Sugerencias de tipo básicas: de tipos simples a contenedores

Ya hemos visto cómo usar sugerencias de tipo básicas para tipos simples. Recapitulemos rápidamente la sintaxis:

- Para enteros: `count: int`
- Para números de punto flotante: `price: float`
- Para cadenas de texto: `name: str`
- Para booleanos: `is_active: bool`
- Para anotaciones de funciones: seguir el patrón `def function_name(parameter: type) -> return_type:`

Ahora, veamos cómo podemos usar sugerencias de tipo con tipos de contenedor como listas y diccionarios:

```python
def process_order(items: list[str], quantities: list[int]) -> dict[str, int]:
    return {item: quantity for item, quantity in zip(items, quantities)}


# Usage
order = process_order(["apple", "banana", "orange"], [2, 3, 1])
print(order)
# Output: {'apple': 2, 'banana': 3, 'orange': 1}
```

Analicemos más de cerca este ejemplo:

- `list[str]` indica que `items` debe ser una lista de cadenas de texto.
- `list[int]` especifica que `quantities` debe ser una lista de enteros.
- `-> dict[str, int]` nos indica que la función devuelve un diccionario con claves de cadena y valores enteros.

Estas sugerencias de tipo proporcionan información clara sobre la estructura esperada de los datos de entrada y salida, lo cual es particularmente valioso en Clean Architecture, donde a menudo manejamos estructuras de datos complejas que pasan entre diferentes capas de la aplicación.

> [!NOTE]
> **¿Por qué a veces veo `list` y otras veces `List` en el código Python?**
>
> Es posible que observes que algunos códigos de Python utilizan `list` (en minúsculas) mientras que otros utilizan `List` (con mayúscula inicial) para las anotaciones de tipo. Esto se debe a que la compatibilidad con tipos genéricos integrados (*built-in generics*) se introdujo en Python 3.9. Antes de eso, era necesario importar el tipo sustituto `List` desde el paquete `typing`. Para código en Python 3.9+, puedes simplemente utilizar los nombres de colecciones integradas como `list` y `dict`.

En Clean Architecture, tales sugerencias de tipo son especialmente útiles al definir interfaces entre diferentes capas de la aplicación. Proporcionan un contrato claro para los datos que pasan entre la capa de Dominio, los casos de uso y las interfaces externas, ayudando a mantener límites limpios y a reducir el riesgo de inconsistencias en los datos.

A medida que avancemos, veremos cómo características de tipado más avanzadas pueden mejorar aún más nuestra capacidad para expresar relaciones y restricciones complejas, respaldando implementaciones robustas de Clean Architecture en Python.

#### Sequence: flexibilidad en tipos de colección

La sugerencia de tipo `Sequence` del módulo `typing` es una herramienta poderosa para expresar colecciones de una manera que se alinea bien con los principios SOLID, particularmente con el Principio de Sustitución de Liskov y el Principio de Abierto/Cerrado.

He aquí un ejemplo que demuestra su uso:

```python
from typing import Sequence


def calculate_total(items: Sequence[float]) -> float:
    return sum(items)


# Usage
print(calculate_total([1.0, 2.0, 3.0]))  # Works with list
print(calculate_total((4.0, 5.0, 6.0)))  # Also works with tuple
```

El uso de `Sequence` en lugar de un tipo específico como `list` o `tuple` ofrece varias ventajas:

- **Principio de Sustitución de Liskov**: `Sequence` permite que la función trabaje con cualquier tipo de secuencia (listas, tuplas y clases de secuencia personalizadas) sin romper el contrato.
- **Principio de Abierto/Cerrado**: La función `calculate_total` está abierta a la extensión (puede funcionar con nuevos tipos de secuencia) pero cerrada a la modificación (no necesitamos cambiar la función para admitir nuevos tipos).
- **Principio de Segregación de Interfaces**: Al usar `Sequence`, solo estamos exigiendo la interfaz mínima requerida (iteración sobre elementos), en lugar de comprometernos con un tipo de colección específico con métodos potencialmente innecesarios.

En Clean Architecture, la sugerencia de tipo `Sequence` resulta valiosa en varias capas. En la capa de Casos de Uso (*Use Case layer*), facilita el procesamiento de colecciones de entidades u objetos de valor (*Value Objects*). En la capa de Adaptadores de Interfaz (*Interface Adapters layer*), permite APIs flexibles que funcionan con varios tipos de colección. En la capa de Dominio (*Domain layer*), `Sequence` expresa la necesidad de una colección sin especificar su implementación concreta, manteniendo la separación de responsabilidades. Esta versatilidad hace de `Sequence` una herramienta poderosa para crear implementaciones adaptables y mantenibles de Clean Architecture en Python.

#### Tipos Union y Optional

En Clean Architecture, a menudo necesitamos manejar múltiples tipos posibles o valores opcionales, especialmente en los límites entre capas. Los tipos `Union` y los tipos `Optional` son perfectos para estos escenarios:

```python
from typing import Union, Optional


def process_input(data: Union[str, int]) -> str:
    return str(data)


def find_user(user_id: Optional[int] = None) -> Optional[str]:
    if user_id is None:
        return None
    # ... logic to find user ...
    return "User found"


# Usage
result1 = process_input("Hello")  # Works with str
result2 = process_input(42)  # Works with int
user = find_user()  # Optional parameter
```

Los tipos `Union` permiten que un valor sea uno de varios tipos posibles, mientras que `Optional` es una abreviatura de `Union[Some_Type, None]`. Estas construcciones son particularmente útiles en Clean Architecture para crear interfaces flexibles entre capas mientras se mantiene la seguridad de tipos.

Cabe señalar que en Python 3.10+, la sintaxis de unión se simplificó a un uso conciso y literal del carácter de tubería (`|`):

```python
def process_input(data: Union[str, int]) -> str:
```

La línea anterior se simplificaría a lo siguiente:

```python
def process_input(data: str | int) -> str:
```

#### Tipos Literal

Los tipos `Literal` nos permiten especificar los valores exactos que puede tomar una variable. Esto es especialmente útil en Clean Architecture para hacer cumplir valores específicos en los límites de las interfaces:

```python
from typing import Literal

LogLevel = Literal["DEBUG", "INFO", "WARNING", "ERROR"]


def set_log_level(level: LogLevel) -> None:
    print(f"Setting log level to {level}")


# Usage
set_log_level("DEBUG")  # Valid
set_log_level("CRITICAL")  # Type checker would flag this as an error
```

Los tipos `Literal` ayudan a crear interfaces más precisas, reduciendo la posibilidad de que datos no válidos se propaguen por el sistema. Esto se alinea perfectamente con el énfasis de Clean Architecture en límites y contratos claros entre capas.

#### Alias de tipos (*Type aliases*)

Los alias de tipos ayudan a simplificar anotaciones de tipo complejas, haciendo que nuestro código sea más legible y mantenible. Esto resulta particularmente útil en Clean Architecture cuando se trabaja con modelos de dominio complejos u objetos de transferencia de datos (*Data Transfer Objects* o DTOs).

Consideremos el siguiente ejemplo:

```python
# Type aliases
UserDict = dict[str, str]
UserList = list[UserDict]


def process_users(users: UserList) -> None:
    for user in users:
        print(f"Processing user: {user['name']}")


# Usage
users: UserList = [{"name": "Alice"}, {"name": "Bob"}]
process_users(users)
```

Analicemos más de cerca este código:

- `UserDict` es un alias de tipo para `dict[str, str]`, que representa un objeto de usuario con claves y valores de cadena de texto.
- `UserList` es un alias de tipo para `list[UserDict]`, que representa una lista de diccionarios de usuarios.

Los alias de tipos proporcionan nombres más legibles para tipos complejos, mejorando la claridad del código sin crear nuevos tipos. Nos permiten escribir código que es a la vez expresivo y alineado con los principios de Clean Architecture, promoviendo la separación de responsabilidades, la mantenibilidad y la claridad.

#### NewType

`NewType` crea tipos distintos, proporcionando una seguridad de tipos adicional. Esto es valioso en Clean Architecture para definir conceptos de dominio claros:

```python
from typing import NewType

UserId = NewType("UserId", int)
ProductId = NewType("ProductId", int)


def process_order(user_id: UserId, product_id: ProductId) -> None:
    print(f"Processing order for User {user_id} and Product {product_id}")


# Usage
user_id = UserId(1)
product_id = ProductId(1)  # Same underlying int, but distinct type
process_order(user_id, product_id)
# This would raise a type error:
# process_order(product_id, user_id)
```

`NewType` crea tipos diferenciados que son reconocidos por los comprobadores estáticos de tipos, evitando la mezcla accidental de valores conceptualmente diferentes pero con el mismo tipo subyacente. Esto ayuda a detectar posibles errores en las primeras etapas del proceso de desarrollo y mejora la seguridad de tipos general de tu implementación de Clean Architecture.

Tanto los alias de tipos como `NewType` se alinean bien con los principios de Clean Architecture al mejorar la legibilidad del código, garantizar la seguridad de tipos a través de los límites de las capas y definir claramente los conceptos del dominio. Esto conduce a implementaciones de Clean Architecture más expresivas, seguras en cuanto a tipos y mantenibles en Python.

#### El tipo Any

El tipo `Any` es una sugerencia de tipo especial que esencialmente le indica al comprobador de tipos que permita cualquier tipo. Se utiliza cuando deseas indicar que una variable puede ser de cualquier tipo, o cuando estás manejando código donde el tipo genuinamente no se conoce o podría variar ampliamente. Podemos ver su uso en este ejemplo general de registro (*logging*):

```python
from typing import Any


def log_data(data: Any) -> None:
    print(f"Logged: {data}")


# Usage
log_data("A string")
log_data(42)
log_data({"key": "value"})
```

En Clean Architecture, generalmente nuestro objetivo es ser lo más específicos posible con los tipos, especialmente en los límites de las capas. El tipo `Any` debe verse como un último recurso, indicando a menudo la necesidad de una refactorización o de una definición de tipo más específica. Es más apropiado cuando nos conectamos con sistemas externos donde el tipo es verdaderamente desconocido o altamente variable. Dentro de tu propio código, considera el uso de `Any` como una señal para refactorizar el código hacia el uso de tipos específicos en lugar de emplear el tipo comodín `Any`.

Estas características avanzadas de tipado proporcionan herramientas poderosas para implementar Clean Architecture en Python. Nos permiten crear interfaces más expresivas, precisas y autodocumentadas entre las diferentes capas de nuestra aplicación. A medida que avancemos, exploraremos cómo se pueden aplicar estas herramientas para respaldar los principios SOLID en nuestros diseños de Clean Architecture.

---

### Sección 3.4: Aprovechamiento de herramientas automatizadas de comprobación estática de tipos

A medida que hemos explorado el sistema de tipado de Python y sus beneficios para Clean Architecture, es crucial comprender cómo aplicar eficazmente estas sugerencias de tipo en la práctica. Python, al ser un lenguaje de tipado dinámico, no impone la comprobación de tipos en tiempo de ejecución. Aquí es donde entran en juego las herramientas automatizadas de comprobación estática de tipos, cerrando la brecha entre la naturaleza dinámica de Python y los beneficios del tipado estático. Este enfoque ofrece varios beneficios clave:

- **Detección temprana de errores**: Detecta problemas relacionados con los tipos antes del tiempo de ejecución, reduciendo la probabilidad de errores en producción.
- **Calidad de código mejorada**: Hace cumplir el uso consistente de tipos en todo tu proyecto, lo que conduce a un código más robusto y autodocumentado.
- **Refactorización mejorada**: Permite realizar cambios a gran escala en el código con mayor confianza, ya que el comprobador de tipos puede identificar muchos de los lugares que necesitan actualizarse.
- **Mejor soporte en el IDE**: Habilita herramientas más precisas de autocompletado, navegación y refactorización de código en tu entorno de desarrollo.

Estos beneficios son particularmente valiosos en las implementaciones de Clean Architecture, donde mantener límites claros entre capas y garantizar la corrección del flujo de datos es primordial.

En esta sección, nos centraremos en cómo aprovechar estas herramientas automatizadas para hacer cumplir la consistencia de tipos, detectar errores tempranamente y mejorar la experiencia general de desarrollo. Utilizaremos la interfaz de línea de comandos (CLI) de `mypy` y luego utilizaremos otra herramienta como extensión para el IDE VS Code.

#### La CLI de mypy

Mypy es un potente comprobador estático de tipos que se puede ejecutar directamente desde la línea de comandos. Esto facilita su integración en tu flujo de trabajo de desarrollo y en los pipelines de despliegue. Veamos cómo usar `mypy` e interpretar su salida.

Primero, necesitarás instalar `mypy`. Al ser un módulo de Python, puedes instalarlo fácilmente usando `pip`:

```bash
pip install mypy
```

Una vez instalado, puedes usar `mypy` para comprobar si tus archivos de Python contienen errores de tipo. Veamos un ejemplo sencillo. Supongamos que tienes un archivo de Python llamado `user_service.py` con el siguiente contenido:

```python
def get_user(user_id: int) -> dict:
    # Simulating user retrieval
    return {"id": user_id, "name": "John Doe", "email": "john@example.com"}


def send_email(user: dict, subject: str) -> None:
    print(f"Sending email to {user['email']} with subject: {subject}")


# Usage
user = get_user("123")
send_email(user, "Welcome!")
```

Para comprobar este archivo con `mypy`, ejecuta lo siguiente:

```bash
mypy user_service.py
```

Salida generada:

```text
user_service.py:11: error: Argument 1 to "get_user" has incompatible type "str"; expected "int"  [arg-type]
Found 1 error in 1 file (checked 1 source file)
```

Desglosemos lo que nos indica `mypy`:

- Identifica el archivo (`user_service.py`) y el número de línea donde ocurre el error.
- Describe el error: estamos pasando una cadena de texto (`"123"`) a `get_user`, pero la función espera un entero.
- Categoriza el error como un problema `[arg-type]`, indicando un problema con los tipos de argumentos.

Esta salida es increíblemente valiosa. Detecta una discrepancia de tipos que podría provocar errores en tiempo de ejecución, lo que nos permite corregirla antes de que el código siquiera se ejecute.

Podemos corregir el error cambiando `user = get_user("123")` por `user = get_user(123)` y luego volver a ejecutar `mypy`:

```bash
mypy user_service.py
```

Salida generada:

```text
Success: no issues found in 1 source file
```

Ahora, `mypy` no reporta ningún problema, confirmando que nuestras anotaciones de tipo son consistentes con cómo estamos utilizando las funciones.

#### Configuración de mypy

Si bien `mypy` funciona bien con su configuración por defecto, puedes personalizar su comportamiento utilizando un archivo de configuración. Esto es particularmente útil para proyectos grandes o cuando deseas adoptar la comprobación de tipos de forma gradual.

Crea un archivo llamado `mypy.ini` en la raíz de tu proyecto:

```ini
[mypy]
ignore_missing_imports = True
strict_optional = True
warn_redundant_casts = True
warn_unused_ignores = True
warn_return_any = True
warn_unreachable = True
```

Esta configuración realiza lo siguiente:

- **`ignore_missing_imports = True`**: Ignora las importaciones faltantes, lo cual es útil cuando se trabaja con bibliotecas de terceros que no cuentan con definiciones de tipos (*type stubs*).
- **`strict_optional = True`**: Habilita una comprobación estricta de tipos `Optional`.
- **`warn_redundant_casts = True`**: Advierte sobre conversiones de tipo (*type casts*) redundantes.
- **`warn_unused_ignores = True`**: Advierte sobre comentarios `# type: ignore` que ya no son necesarios.
- **`warn_return_any = True`**: Advierte cuando una función devuelve `Any` de forma implícita.
- **`warn_unreachable = True`**: Te alerta sobre código inalcanzable.

Con esta configuración, `mypy` proporcionará una comprobación más exhaustiva, ayudándote a detectar una gama más amplia de problemas potenciales en tu implementación de Clean Architecture.

Al ejecutar `mypy` regularmente como parte de tu proceso de desarrollo, puedes detectar problemas relacionados con los tipos de forma temprana, asegurando que las capas de Clean Architecture interactúen correctamente y mantengan los límites previstos.

Las opciones de configuración para `mypy` son amplias y se pueden adaptar para satisfacer las necesidades específicas de tu proyecto. Para obtener una lista completa de las opciones disponibles y sus descripciones, consulta la documentación oficial de `mypy` en [https://mypy.readthedocs.io/en/stable/config_file.html](https://mypy.readthedocs.io/en/stable/config_file.html).

#### Mypy en los pipelines de despliegue

Integrar `mypy` en tu pipeline de despliegue es un paso crucial para garantizar la consistencia de tipos en todo tu proyecto, especialmente en el contexto de Clean Architecture, donde mantener límites claros entre capas es fundamental.

Aunque los detalles específicos de implementación pueden variar según la herramienta de CI/CD que elijas, el concepto fundamental sigue siendo el mismo: ejecutar `mypy` como parte de tus comprobaciones automatizadas antes de desplegar el código. Dado que `mypy` opera a través de una CLI sencilla, incorporarlo en la mayoría de los pipelines de despliegue es un proceso directo.

Por ejemplo, podrías ejecutar comprobaciones de `mypy` en las siguientes instancias:

- Tras cada `push` de commits
- Como parte de la validación de *pull requests*
- Antes de fusionar (*merge*) en la rama principal
- Antes de desplegar en entornos de *staging* o producción

Este enfoque ayuda a detectar problemas relacionados con los tipos en etapas tempranas del proceso de desarrollo, reduciendo la probabilidad de que los errores de tipo lleguen a producción.

Como ejemplo, he aquí un flujo de trabajo simple de GitHub Actions que ejecuta `mypy` seguido de las pruebas unitarias:

```yaml
name: Python Type Check and Test
on: [ push, pull_request ]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.13'
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install mypy pytest
      - name: Run mypy
        run: mypy .
      - name: Run tests
        run: pytest
```

Este flujo de trabajo realiza lo siguiente:

1. Se activa en eventos de `push` o `pull_request`.
2. Configura un entorno de Python (Python 3.13).
3. Instala las dependencias necesarias (incluyendo `mypy` y `pytest`).
4. Ejecuta `mypy` en todo el proyecto (`mypy .`).
5. Ejecuta las pruebas unitarias del proyecto (`pytest`).

Al incluir `mypy` en tu pipeline de despliegue, aseguras que todo cambio en el código sea verificado respecto a sus tipos antes de ser integrado, ayudando a mantener la integridad de tu implementación de Clean Architecture.

Recuerda que, si bien este ejemplo utiliza GitHub Actions, el principio se aplica a cualquier herramienta de CI/CD. La clave radica en ejecutar `mypy` como parte de tus comprobaciones automatizadas, aprovechando su CLI para integrarlo fluidamente en tus procesos de despliegue existentes.

#### Aprovechamiento de las sugerencias de tipo en IDEs para una mejor experiencia de desarrollo

Si bien disponer de un pipeline de despliegue con comprobación de tipos es esencial para mantener la calidad del código, el enfoque más eficaz implica detectar problemas de tipo en tiempo real a medida que se escribe el código. Esta retroalimentación inmediata permite a los desarrolladores abordar las inconsistencias de tipo al instante, reduciendo el tiempo y el esfuerzo dedicados a solucionar problemas más adelante en el proceso de desarrollo.

Los IDEs modernos han adoptado este enfoque, aprovechando las sugerencias de tipo para ofrecer una experiencia de programación mejorada con retroalimentación inmediata de comprobación de tipos. Si bien esta funcionalidad está disponible en la mayoría de los IDEs populares para Python, nos centraremos en VS Code como nuestro ejemplo principal debido a su uso generalizado y a su sólido soporte para Python.

En VS Code, la extensión **Pylance** se ha convertido en la herramienta preferida para la comprobación de tipos en Python. Pylance, que utiliza `pyright` como motor de comprobación de tipos, se integra perfectamente en VS Code, ofreciendo comprobación de tipos en tiempo real junto con otras características avanzadas que mejoran significativamente la experiencia de desarrollo en Python.

Con Pylance instalado en VS Code, los desarrolladores reciben señales visuales instantáneas sobre cualquier problema de tipo:

> *Figura 3.1: VS Code con la extensión Pylance instalada.*

En la Figura 3.1, observamos que el uso de una cadena de texto donde se espera un entero aparece decorado en el editor del IDE con una explicación precisa de cuál es el problema.

Esta retroalimentación en tiempo real crea una poderosa sinergia con las sugerencias de tipo que hemos incorporado en nuestra implementación de Clean Architecture. Permite a los desarrolladores mantener una estricta consistencia de tipos a través de los límites arquitectónicos mientras programan, en lugar de depender únicamente de comprobaciones posteriores al desarrollo.

Puedes instalar la extensión Pylance desde el Marketplace de VS Code ([https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance)), además de leer más sobre sus características y configuración.

#### Características adicionales de comprobación de tipos

Si bien la retroalimentación en tiempo real y las comprobaciones en el pipeline de despliegue son cruciales, existen características adicionales que pueden mejorar tu flujo de trabajo de comprobación de tipos.

##### La pestaña Problems en VS Code

VS Code ofrece una pestaña **Problems** que agrupa todos los problemas en tu código, incluidos los errores de tipo detectados por Pylance. Esta pestaña proporciona una visión general completa de las inconsistencias de tipos en todo tu proyecto.

> *Figura 3.2: Pestaña Problems de VS Code.*

En la Figura 3.2, vemos la agregación de las comprobaciones de tipos que vimos en línea anteriormente. Los desarrolladores pueden usar esta pestaña como una comprobación final antes de confirmar (*commit*) el código, asegurando que no se pase por alto ningún problema de tipo.

##### Hooks de pre-commit de Git

Git admite *hooks* de pre-commit, lo que te permite ejecutar comprobaciones automáticamente antes de cada confirmación de cambios. Puedes configurar estos *hooks* para que ejecuten `mypy` y pruebas unitarias, evitando *commits* que introduzcan errores de tipo o rompan funcionalidades existentes.

Para obtener más información sobre la configuración de Git hooks, consulta la documentación oficial de Git: [https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

Al incorporar estas funciones adicionales en tu flujo de trabajo, creas múltiples capas de comprobación de tipos en tu proceso de desarrollo. Este enfoque integral ayuda a mantener la integridad de tu implementación de Clean Architecture, detectando inconsistencias de tipos en cada etapa, desde la escritura del código hasta la confirmación de cambios.

#### Estrategia de adopción gradual

La introducción de la comprobación estática de tipos en proyectos de Python a veces puede enfrentar resistencia, particularmente por parte de desarrolladores acostumbrados a la naturaleza puramente dinámica de Python. Para asegurar una transición fluida, es crucial trabajar de manera colaborativa con tu equipo, comunicando claramente la justificación y los beneficios del *type hinting*.

He aquí una estrategia para una adopción gradual:

1. **Reunión de equipo**: Realizar una reunión de equipo para discutir y formular un plan para incorporar la comprobación de tipos.
2. **Política para código nuevo**: Implementar una política que requiera sugerencias de tipo para todo código nuevo.
3. **Minimizar la disrupción inicial**: Minimizar el impacto inicial configurando `mypy` para ignorar módulos o paquetes específicos. Esto se puede hacer en el archivo de configuración de `mypy`:

```ini
[mypy.unwanted_module]
ignore_errors = True

[mypy.some_package.*]
ignore_errors = True
```

4. **Tareas de mantenimiento programadas**: Crear tareas de mantenimiento programadas para agregar progresivamente sugerencias de tipo al código existente, priorizando las rutas críticas.

Al emplear estas herramientas y estrategias, puedes mejorar sustancialmente la robustez y mantenibilidad de tus implementaciones de Clean Architecture. El enfoque más eficaz combina comprobaciones en varias etapas: retroalimentación en tiempo real en el IDE, *hooks* de pre-commit y validación en el pipeline de despliegue. Esta estrategia de múltiples capas garantiza la detección temprana de errores, mejora la navegación por el código y mantiene una comprobación de tipos consistente durante todo el ciclo de vida del desarrollo. En última instancia, este enfoque integral conduce a aplicaciones Python más confiables, mantenibles y escalables, aprovechando al máximo el poder del sistema de tipos de Python en tus proyectos de Clean Architecture.

---

### Sección 3.5: Resumen

En este capítulo, exploramos la conciencia de tipos en el entorno dinámico de Python y su papel en el fortalecimiento de las implementaciones de Clean Architecture. Aprendimos a aprovechar el sistema de tipos y las sugerencias de tipo de Python para crear un código más robusto y autodocumentado, y descubrimos el valor de las herramientas automatizadas de comprobación estática de tipos para detectar errores tempranamente.

Adquiriste habilidades para implementar sugerencias de tipo en funciones, clases y variables, mejorando la claridad y confiabilidad del código. También aprendiste a configurar y usar herramientas de comprobación estática de tipos como `mypy` para verificar la consistencia de tipos en tus proyectos. Estas habilidades son fundamentales para crear implementaciones mantenibles y escalables de Clean Architecture en Python, mejorando la calidad del código y la alineación con los principios de Clean Architecture.

En el próximo capítulo, *Domain-Driven Design: Creando la lógica de negocio central*, nos basaremos en este Python mejorado con tipos y en los principios SOLID del [Capítulo 2](https://subscription.packtpub.com/book/programming/9781836642893/2). Exploraremos la capa de Dominio de Clean Architecture, aprendiendo a modelar e implementar la lógica de negocio central de forma independiente de las preocupaciones externas. Utilizando una aplicación de gestión de tareas personales como ejemplo, aplicaremos técnicas de conciencia de tipos y principios SOLID para crear un modelo de dominio robusto y bien estructurado, sentando las bases para una arquitectura verdaderamente limpia y mantenible.

---

### Sección 3.6: Lecturas complementarias

- **Python Type Checking (Guide)**: [https://realpython.com/python-type-checking/](https://realpython.com/python-type-checking/)
- **Type Hints Cheat Sheet**: [https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)
- **Continuous Integration with Python: An Introduction**: [https://realpython.com/python-continuous-integration/](https://realpython.com/python-continuous-integration/)

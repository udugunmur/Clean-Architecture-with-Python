# Parte 3: Aplicación de Clean Architecture en Python

## Capítulo 11: De código heredado (Legacy) a Clean: Refactorización en Python para la mantenibilidad

Mientras que los capítulos anteriores demostraron los principios de Clean Architecture a través del desarrollo desde cero (*greenfield*), los sistemas del mundo real a menudo presentan un desafío diferente. Las aplicaciones existentes, construidas bajo la presión del tiempo o antes de que se establecieran las mejores prácticas arquitectónicas, violan con frecuencia los principios fundamentales de Clean Architecture. Su lógica de dominio se entrelaza con los frameworks, las reglas de negocio se mezclan con las preocupaciones de infraestructura y las dependencias fluyen en todas direcciones. Sin embargo, estos sistemas a menudo satisfacen necesidades de negocio críticas y no pueden reemplazarse simplemente.

A través de nuestra exploración de la transformación hacia Clean Architecture, descubriremos cómo hacer evolucionar sistemáticamente los sistemas heredados (*legacy*) mientras mantenemos su valor de negocio. Veremos cómo los límites explícitos y las reglas de dependencia de Clean Architecture proporcionan una guía clara para mejorar los sistemas existentes, incluso bajo restricciones del mundo real. Aprenderás a identificar violaciones arquitectónicas, establecer límites limpios de manera incremental y mantener la estabilidad del sistema durante la transformación.

Al final de este capítulo, comprenderás cómo aplicar los principios de Clean Architecture a sistemas heredados mediante una implementación por etapas. Serás capaz de evaluar los sistemas existentes a través del prisma de Clean Architecture e implementar transformaciones delimitadas que respeten las restricciones de negocio mientras mantienen la estabilidad del sistema.

En este capítulo, vamos a cubrir los siguientes temas principales:

- **Evaluación y planificación de la transformación arquitectónica**
- **Implementación progresiva de Clean Architecture**

---

### Sección 11.1: Requisitos técnicos

Los ejemplos de código presentados en este capítulo y a lo largo del resto del libro se han probado con Python 3.13. Por brevedad, la mayoría de los ejemplos de código del capítulo solo están parcialmente implementados. Las versiones completas de todos los ejemplos pueden encontrarse en el repositorio de GitHub que acompaña al libro en [https://github.com/PacktPublishing/Clean-Architecture-with-Python](https://github.com/PacktPublishing/Clean-Architecture-with-Python).

---

### Sección 11.2: Evaluación y planificación de la transformación arquitectónica

Mejorar la mantenibilidad y reducir el riesgo en aplicaciones complejas requiere un enfoque sistemático para la evolución arquitectónica. Las aplicaciones con dependencias enredadas y responsabilidades difusas consumen un esfuerzo de mantenimiento desproporcionado. La adición de funcionalidades que debería llevar días se prolonga durante semanas; las correcciones de errores provocan fallos persistentes e inesperados; y la incorporación de nuevos desarrolladores se vuelve dolorosamente lenta. Estos síntomas no solo reflejan problemas técnicos; también tienen impactos directos en el negocio que deben abordarse.

A lo largo de los capítulos anteriores, hemos visto cómo Clean Architecture minimiza naturalmente las cargas de mantenimiento a través de límites claros y dependencias explícitas. Ahora, podemos aplicar este mismo prisma arquitectónico para evaluar los sistemas existentes, identificando dónde ocurren las violaciones y cómo abordarlas sistemáticamente. Esto no significa forzar una Clean Architecture ideal en los sistemas heredados de golpe, sino, más bien, adoptar un enfoque equilibrado e incremental que respete las restricciones del negocio mientras mejora progresivamente el sistema.

Al analizar el código heredado a través de los principios de Clean Architecture, podemos descubrir límites naturales del sistema esperando a ser establecidos, conceptos de dominio listos para ser aislados e interfaces ansiosas por emerger. Esta evaluación constituye la base de nuestra estrategia de transformación, guiando las decisiones sobre qué cambiar, cuándo cambiarlo y cómo minimizar el riesgo a lo largo del proceso. Con cada mejora incremental, reducimos tanto la carga de mantenimiento como la inestabilidad asociada con los cambios futuros, creando un valor de negocio medible más allá de las mejoras técnicas.

#### Evaluación a través del prisma de Clean Architecture

Transformar un sistema existente comienza con la evaluación de su estado actual con respecto a los principios de Clean Architecture. Esta evaluación no consiste en documentar cada detalle, sino que apunta a identificar las violaciones arquitectónicas clave y evaluar su impacto en el negocio. Dado que una transformación global introduce un riesgo inaceptable, necesitamos un enfoque equilibrado que proporcione la comprensión suficiente para fundamentar las discusiones con los interesados (*stakeholders*) a la vez que permita un progreso significativo. Esta evaluación mesurada crea las bases para un análisis colaborativo más profundo una vez que se asegura el apoyo inicial de los interesados.

##### Realización del análisis arquitectónico preliminar

Antes de involucrar a los interesados, debemos realizar un análisis arquitectónico preliminar específico centrado en identificar los problemas técnicos clave que puedan comunicarse de manera efectiva a audiencias no técnicas. Esta evaluación inicial no es exhaustiva, pero proporciona suficiente información para ilustrar los problemas arquitectónicos en términos relevantes para el negocio.

Un análisis preliminar enfocado puede incluir:

- **Inventario arquitectónico (*Architectural inventory*):** Identificar los componentes principales y sus interacciones, creando una comprensión base sin documentar cada detalle.
- **Mapeo de dependencias (*Dependency mapping*):** Esbozar los flujos de dependencias de alto nivel que revelen las dependencias circulares más problemáticas y el acoplamiento a frameworks que viole los principios de Clean Architecture.
- **Evaluación de la penetración del framework (*Framework penetration assessment*):** Destacar ejemplos donde el código del framework haya impregnado significativamente la lógica de negocio, centrándose en áreas con un impacto visible en el mantenimiento o la flexibilidad.
- **Dispersión de la lógica de dominio (*Domain logic dispersion*):** Identificar algunos ejemplos claros donde las reglas de negocio estén fragmentadas a lo largo de la base de código, particularmente aquellas que afecten a funcionalidades que cambian con frecuencia.

Por ejemplo, al analizar un sistema de comercio electrónico en Python, podríamos descubrir que los modelos de Django contienen reglas de negocio críticas, la lógica de validación está duplicada en múltiples vistas y el código de procesamiento de pagos hace referencia directa a consultas nativas de base de datos. Este análisis preliminar proporciona ejemplos concretos que los interesados no técnicos pueden entender: *"Cuando necesitamos cambiar el funcionamiento de los precios, actualmente tenemos que modificar código en siete lugares diferentes a través de tres módulos distintos"*.

Este análisis sirve como una herramienta de comunicación, traducida a términos de impacto en el negocio, tales como un mayor tiempo de comercialización (*time-to-market*), tasas elevadas de errores y una menor capacidad para responder a requisitos cambiantes. Al enmarcar los problemas arquitectónicos en términos de negocio antes de comenzar la transformación, creamos las bases para el apoyo de los interesados y la asignación adecuada de recursos.

Esta evaluación arquitectónica preliminar sirve como punto de entrada para la transformación, no como un plano exhaustivo. Concéntrate en identificar solo las violaciones específicas suficientes para involucrar a los interesados con ejemplos creíbles que ilustren el impacto en el negocio. Resiste la tentación de diagramar cada relación en esta etapa. Tu comprensión se profundizará sustancialmente durante el análisis colaborativo del dominio que sigue a continuación. El objetivo es recopilar pruebas suficientes para defender la necesidad de la transformación, a la vez que se prepara el terreno para una exploración más profunda con los interesados.

##### Construcción de la alineación de los interesados (stakeholders)

Una vez completado el análisis arquitectónico preliminar e identificados los problemas clave, el siguiente paso es comunicar estos hallazgos a los interesados y asegurar el respaldo inicial para la transformación. Esta interacción inicial no se trata de obtener la aprobación final para cambios específicos, sino que busca generar una conciencia compartida sobre los problemas arquitectónicos y establecer el apoyo para un proceso de descubrimiento más colaborativo. Los conocimientos adquiridos a partir de nuestro análisis deben traducirse ahora en términos de impacto en el negocio que resuenen en los diferentes grupos de interesados, sentando las bases para el análisis colaborativo más profundo que vendrá después.

El primer paso es involucrar a los interesados adecuados:

- **Equipos de ingeniería:** Que comprenden los detalles técnicos y las restricciones de implementación.
- **Propietarios del producto (*Product owners*):** Que pueden articular las prioridades de negocio y validar el valor de los cambios arquitectónicos.
- **Personal de operaciones:** Que gestiona el despliegue del sistema y los aspectos de fiabilidad.
- **Usuarios finales:** Que pueden compartir los puntos de dolor relacionados con la estabilidad del sistema y la entrega de funcionalidades.

El alcance de la participación de los interesados debe corresponderse directamente con la escala de la transformación planificada. Las refactorizaciones más pequeñas pueden requerir únicamente la coordinación con tu equipo inmediato, mientras que las reestructuraciones arquitectónicas a nivel de todo el sistema pueden requerir la participación directa del CTO o del vicepresidente de ingeniería.

Una vez que los interesados están alineados en torno a una visión de transformación compartida, el siguiente paso crítico es establecer mediciones de referencia (*baseline*) que permitan rastrear el progreso y demostrar el valor. Estas métricas crean rendición de cuentas y proporcionan pruebas claras de mejora a lo largo del viaje de transformación:

- **Métricas de mantenimiento:** Tiempo dedicado a la corrección de errores (*bug fixes*), tiempo de entrega de nuevas funcionalidades (*feature delivery lead time*).
- **Indicadores de calidad:** Tasas de defectos, cobertura de pruebas, puntuaciones de análisis estático.
- **Eficacia del equipo:** Tiempo de incorporación de desarrolladores (*onboarding time*), frecuencia de despliegue.
- **Resultados de negocio:** Satisfacción del cliente, tasas de adopción de funcionalidades.

Estas métricas cumplen múltiples propósitos a lo largo de la transformación. Inicialmente, justifican el esfuerzo y ayudan a asegurar el apoyo del liderazgo. A medida que el trabajo progresa, validan la eficacia y destacan las áreas que necesitan ajustes. También ayudan a definir qué significa "terminado" (*done*) para la transformación, reconociendo que el objetivo es la mejora sostenible y no la perfección arquitectónica. Lo más importante es que las métricas traducen las mejoras técnicas al lenguaje del valor de negocio, creando un bucle de retroalimentación que mantiene la transformación alineada tanto con los objetivos técnicos como con las prioridades del negocio.

##### Análisis de dominio más profundo

Los dominios de negocio evolucionan naturalmente con el tiempo, lo que convierte la transformación arquitectónica en una oportunidad ideal para realinear los sistemas con las necesidades actuales del negocio. Tras asegurar el apoyo inicial de los interesados, el siguiente paso es profundizar nuestra comprensión mediante técnicas colaborativas de descubrimiento de dominios. Esta fase conecta nuestros conocimientos técnicos con el conocimiento del dominio de negocio, identificando límites significativos a la vez que consolida el respaldo de los interesados mediante su participación activa. Mientras que nuestro análisis preliminar se centró en problemas técnicos, el descubrimiento colaborativo tiende un puente entre estos hallazgos y los requisitos de negocio en evolución, asegurando que el sistema transformado no solo cuente con una mejor arquitectura, sino que también atienda mejor a las necesidades actuales.

Varios enfoques colaborativos pueden ayudar a tender un puente entre la comprensión técnica y la experiencia en el dominio:

- **Talleres de Event Storming** para mapear procesos de negocio y eventos de dominio ([https://www.eventstorming.com/](https://www.eventstorming.com/))
- **Sesiones de Domain Storytelling** donde los interesados narran los flujos de trabajo clave ([https://domainstorytelling.org/](https://domainstorytelling.org/))
- **Ejercicios de Context Mapping** para identificar los límites del sistema y los puntos de integración ([https://contextmapper.org/](https://contextmapper.org/))

Entre estos enfoques, Event Storming destaca como especialmente valioso para las transformaciones hacia Clean Architecture. Reúne a los interesados en talleres facilitados para validar la comprensión del dominio e identificar los límites arquitectónicos. Los participantes utilizan notas adhesivas codificadas por colores en un espacio de modelado compartido, creando una línea de tiempo visual de los procesos de negocio. La codificación por colores se mapea intencionalmente con las capas de Clean Architecture: los eventos de dominio naranjas representan entidades centrales en el centro de la arquitectura, los comandos azules se alinean con los casos de uso en la capa de Aplicación y las reglas de negocio moradas reflejan reglas de dominio que permanecen independientes de las preocupaciones externas. Los eventos de dominio típicos incluyen *Order Placed* (Pedido realizado), mientras que los comandos pueden incluir acciones como *Process Payments* (Procesar pagos). Este enfoque visual hace que los límites arquitectónicos sean tangibles para todos los interesados, ayudando a identificar puntos de separación naturales al transformar sistemas heredados. Aunque los esquemas de color específicos pueden variar entre equipos, lo más importante es mantener un lenguaje visual coherente.

> **Figura 11.1:** Visualización de Event Storming para un sistema de comercio electrónico, mostrando eventos de dominio, comandos, actores y posibles contextos delimitados (*bounded contexts*).

Este enfoque colaborativo se basa directamente en los principios de modelado de dominio del [Capítulo 4](https://subscription.packtpub.com/book/programming/9781836642893/4), aplicándolos para descubrir límites en sistemas existentes. Los mismos conceptos de Entidades (*Entities*), Objetos de Valor (*Value Objects*) y Agregados (*Aggregates*) ahora ayudan a identificar lo que el sistema heredado debería haber separado pero no lo hizo. Por ejemplo, una sesión de Event Storming podría revelar que el dominio de procesamiento de pedidos contiene eventos distintos como *Order Placed* (Pedido realizado), *Payment Approved* (Pago aprobado), *Inventory Reserved* (Inventario reservado) y *Shipment Created* (Envío creado). Asegúrate de separar las preocupaciones de negocio que podrían dividirse limpiamente en casos de uso discretos en lugar de ser manejadas por un controlador monolítico de pedidos (*Order Controller*).

Los artefactos visuales resultantes sirven como poderosas herramientas de comunicación, ayudando a los interesados a ver cómo los límites arquitectónicos se traducen en beneficios de negocio, como entregas más rápidas o reducción de errores. Este lenguaje compartido a menudo revela perspectivas que el análisis técnico por sí solo pasaría por alto, como que el procesamiento de pedidos y el de pagos tienen diferentes patrones de cambio que indican puntos de separación naturales. Con estos límites identificados a través de la colaboración con los interesados, podemos pasar del descubrimiento a la acción, traduciendo los conocimientos en una hoja de ruta priorizada para la mejora arquitectónica.

#### Creación de una hoja de ruta de implementación por etapas

Con los límites arquitectónicos identificados y priorizados en función del valor de negocio, el enfoque se traslada ahora a la planificación de la ejecución táctica. Transformar sistemas heredados no consiste únicamente en saber qué cambiar; se trata de organizar el trabajo en incrementos manejables y de bajo riesgo que mantengan la estabilidad del sistema mientras mejoran progresivamente la arquitectura.

Una planificación eficaz de la transformación requiere descomponer el trabajo en etapas diferenciadas con entregables claros. En lugar de abrumar a los equipos con un esfuerzo de refactorización masivo, una implementación por etapas crea puntos de control naturales para validar el progreso, recopilar comentarios y ajustar el rumbo según sea necesario.

> **Figura 11.2:** Etapas de transformación a Clean Architecture mostrando la progresión desde los cimientos hasta la optimización.

La etapa de cimientos (*foundation stage*) establece los conceptos centrales del dominio y las abstracciones que servirán como bloques de construcción para el trabajo posterior. Esto a menudo comienza creando modelos de entidades limpios junto a las implementaciones existentes y definiendo interfaces para repositorios y servicios. Al comenzar con estos elementos centrales, los equipos establecen una arquitectura objetivo clara a la vez que minimizan los cambios iniciales en el sistema en ejecución.

A medida que los cimientos toman forma, la etapa de interfaz (*interface stage*) se centra en implementar adaptadores que sirvan de puente entre el núcleo limpio y las preocupaciones externas. Esto incluye construir implementaciones de repositorios que funcionen con bases de datos existentes, crear adaptadores de servicios para integraciones de terceros y desarrollar controladores que traduzcan entre los frameworks y el dominio. Estos adaptadores crean una capa protectora alrededor de la Clean Architecture emergente.

La etapa de integración (*integration stage*) migra gradualmente la funcionalidad existente hacia la nueva arquitectura. Los equipos reemplazan el acceso directo a la base de datos con implementaciones de repositorios, sustituyen las reglas de negocio codificadas de forma rígida (*hardcoded*) por servicios de dominio e integran nuevos componentes con sistemas heredados a través de adaptadores adecuados. Esta etapa suele avanzar característica por característica o dominio por dominio, permitiendo cambios controlados e incrementales.

Por último, la etapa de optimización (*optimization stage*) refina y mejora la arquitectura a partir de la experiencia del mundo real. Los equipos abordan consideraciones de rendimiento en las implementaciones de repositorios, amplían la cobertura de pruebas y mejoran los patrones de manejo de errores y resiliencia. Esta etapa reconoce que la arquitectura objetivo no se logra de una sola vez, sino a través de un refinamiento continuo.

A lo largo de este enfoque por etapas, las métricas de referencia establecidas anteriormente desempeñan un papel crucial en la validación del progreso y en la comunicación del impacto de la transformación. Al rastrear métricas como el tiempo de mantenimiento, las tasas de defectos y la velocidad de entrega de funcionalidades antes, durante y después de cada etapa de transformación, los equipos pueden demostrar mejoras tangibles y ajustar su enfoque basándose en resultados reales en lugar de suposiciones. Estas métricas también ayudan a los equipos a identificar cuándo han alcanzado niveles aceptables de mejora arquitectónica, permitiendo a las organizaciones equilibrar el refinamiento arquitectónico con las necesidades continuas del negocio.

##### Enfoques para llevar a cabo el trabajo de transformación

La complejidad de ejecución de una transformación arquitectónica requiere una planificación logística cuidadosa más allá de los aspectos técnicos. Los equipos deben decidir cómo organizar el trabajo junto con el desarrollo continuo de funcionalidades y el mantenimiento. Vale la pena considerar varios enfoques:

- **Iteraciones dedicadas a la transformación:** Asignan ciclos de sprint específicos exclusivamente al trabajo arquitectónico. Este enfoque proporciona tiempo concentrado para refactorizaciones complejas, pero puede retrasar la entrega de funcionalidades. Funciona bien para componentes que necesitan cambios significativos pero que pueden completarse en una o dos iteraciones.
- **Pistas de transformación en paralelo:** Crean equipos dedicados centrados en mejoras arquitectónicas mientras otros equipos continúan con el desarrollo de funcionalidades. Este enfoque mantiene la velocidad de entrega, pero requiere una coordinación minuciosa para evitar conflictos. Es particularmente efectivo para sistemas más grandes donde la transformación abarcará varios trimestres.
- **Transformación basada en oportunidades:** Integra las mejoras arquitectónicas con el trabajo de funcionalidades en áreas relacionadas. A medida que las nuevas funcionalidades tocan un componente, los equipos lo refactorizan hacia Clean Architecture. Este enfoque minimiza el riesgo de una refactorización aislada, pero hace que el progreso dependa de las prioridades de las funcionalidades y puede dar lugar a una transformación desigual.

La mayoría de las transformaciones exitosas combinan estos enfoques en función de las prioridades del negocio y la estructura del equipo. Los componentes críticos pueden justificar esfuerzos dedicados, mientras que las áreas modificadas con menor frecuencia pueden evolucionar mediante una transformación basada en oportunidades. La clave es planificar explícitamente cómo se transformará cada componente en lugar de asumir un enfoque único para todos.

##### Navegación por la transformación en curso

Durante la transformación, el sistema contendrá temporalmente una mezcla de enfoques arquitectónicos antiguos y nuevos. La planificación cuidadosa de estos estados de transición es crucial para mantener la estabilidad del sistema. Para cada componente que se transforme, el plan debe abordar:

- **Estrategia de operación en paralelo:** Cómo coexistirán las implementaciones antiguas y nuevas.
- **Enfoque de verificación:** Métodos para confirmar la equivalencia funcional.
- **Criterios de cambio definitivo (*cutover*):** Condiciones claras para cambiar a la nueva implementación.
- **Procedimientos de reversión (*rollback*):** Mecanismos de seguridad en caso de que surjan problemas.

Las estrategias de pruebas integrales son esenciales durante estas transiciones. Las suites de pruebas de regresión validan que las nuevas implementaciones mantengan la funcionalidad existente, mientras que las pruebas de compatibilidad de interfaces aseguran que los componentes transformados se integren correctamente con el sistema más amplio. Las *feature flags* (indicadores de funcionalidad) proporcionan un mecanismo de cambio eficaz, permitiendo a los equipos habilitar selectivamente nuevas implementaciones para usuarios o escenarios específicos, manteniendo la capacidad de revertir instantáneamente si surgen problemas.

Es importante reconocer que, si bien esta sección describe un enfoque general para la planificación de la transformación, cada sistema heredado presenta desafíos únicos basados en su tamaño, complejidad, pila tecnológica y restricciones de negocio. La escala del trabajo diferirá drásticamente entre sistemas, y los equipos deben adaptar estas pautas a sus circunstancias específicas. Investigaciones adicionales sobre técnicas específicas de tu pila tecnológica o dominio te ayudarán a adaptar este enfoque a tus necesidades. La clave es mantener una mentalidad pragmática, tomando los principios de Clean Architecture como una guía más que como una prescripción rígida.

Con un plan de transformación integral que aborde tanto los cambios técnicos como su logística de implementación, los equipos están bien posicionados para comenzar el trabajo real de transformación. Las siguientes secciones explorarán técnicas concretas para implementar estos planes, comenzando por establecer límites de dominio centrales y refactorizando progresivamente hacia una Clean Architecture.

---

### Sección 11.3: Implementación progresiva de Clean Architecture

Una vez completada nuestra evaluación y establecida la estrategia de transformación, pasamos ahora a la implementación práctica. Esta sección demuestra cómo transformar progresivamente un sistema heredado a través de mejoras cuidadosamente escalonadas que aporten el mayor valor arquitectónico. En lugar de intentar cubrir el proceso de transformación de forma exhaustiva, lo que requeriría un libro por sí solo, destacaremos patrones estratégicos de refactorización que establecen los límites de Clean Architecture de forma incremental manteniendo la estabilidad del sistema.

Los siguientes ejemplos, extraídos de un sistema de procesamiento de pedidos en lugar de nuestra aplicación previa de gestión de tareas, ilustran cómo aplicar los principios de Clean Architecture al código heredado de una manera práctica. Cada etapa de implementación se basa en la anterior, pasando gradualmente de dependencias entrelazadas hacia una clara separación de responsabilidades, desde el establecimiento de límites de dominio hasta la creación de interfaces que unan las arquitecturas antigua y nueva.

#### Análisis inicial del sistema

En este escenario hipotético, te encuentras a cargo de un subsistema de procesamiento de pedidos que ha evolucionado a lo largo de varios años. Lo que comenzó como una simple aplicación Flask para gestionar pedidos de clientes ha crecido hasta incluir el procesamiento de pagos y el cumplimiento básico de pedidos. Aunque es funcionalmente completo, la base de código presenta una importante deuda técnica, con dependencias enredadas, responsabilidades difusas e inconsistencias arquitectónicas que hacen que incluso los cambios simples sean arriesgados y requieran mucho tiempo.

El equipo se enfrenta a problemas recurrentes que ponen de manifiesto los problemas arquitectónicos: un cambio simple en la lógica de cálculo de pedidos requiere modificaciones en tres archivos diferentes; agregar un nuevo método de pago lleva tres semanas en lugar de tres días; y cada despliegue viene acompañado del temor a efectos secundarios inesperados. Lo más revelador es que los nuevos desarrolladores necesitan meses para volverse productivos, rompiendo con frecuencia funcionalidades en áreas aparentemente no relacionadas al realizar cambios.

Basándonos en las fases de análisis arquitectónico preliminar y descubrimiento de dominio descritas en la primera sección de este capítulo, hemos identificado problemas arquitectónicos clave que debemos abordar en nuestra transformación. Comencemos examinando el estado actual del sistema a través del prisma de Clean Architecture, identificando violaciones específicas y límites arquitectónicos que necesitan refuerzo.

Examinemos uno de esos archivos que maneja la creación de pedidos, una pieza central de la funcionalidad del sistema y un candidato principal para nuestros esfuerzos de transformación:

```python
# order_system/app.py
from flask import Flask, request, jsonify
import sqlite3
import requests

app = Flask(__name__)


def get_db_connection():
    conn = sqlite3.connect("orders.db")
    conn.row_factory = sqlite3.Row
    return conn


@app.route("/orders", methods=["POST"])
def create_order():
    data = request.get_json()

    # Input validation mixed with business logic
    if not data or not "customer_id" in data or not "items" in data:
        return jsonify({"error": "Missing required fields"}), 400

    # Direct database access in route handler
    conn = get_db_connection()
```

El comienzo de este archivo ya revela varios problemas arquitectónicos. El manejador de rutas importa SQLite y `requests` directamente, estableciendo dependencias duras sobre estas implementaciones específicas. La función `get_db_connection` crea una conexión directa a una base de datos específica, sin ninguna capa de abstracción. Estas elecciones estructurales violan la Regla de Dependencia de Clean Architecture al permitir que las preocupaciones de las capas externas (framework web, base de datos) penetren en la lógica de negocio.

Continuando a lo largo de la función `create_order`, examinemos cómo procesa los pedidos el manejador de rutas:

```python
    # Business logic mixed with data access
    total_price = 0
    for item in data["items"]:
        # Inventory check via direct database query
        product = conn.execute(
            "SELECT * FROM products WHERE id = ?", (item["product_id"],)
        ).fetchone()
        if not product or product["stock"] < item["quantity"]:
            conn.close()
            return jsonify({"error": f'Product {item["product_id"]} out of stock'}), 400

        # Price calculation mixed with HTTP response preparation
        price = product["price"] * item["quantity"]
        total_price += price

    # External payment service call directly in route handler
    payment_result = requests.post(
        "https://payment-gateway.example.com/process",
        json={"customer_id": data["customer_id"], "amount": total_price, "currency": "USD"},
    )
```

Esta sección intermedia demuestra varias violaciones de Clean Architecture. La lógica de negocio central, como la comprobación de inventario y el cálculo de precios, se mezcla directamente con el acceso a la base de datos. La lógica de procesamiento de pagos realiza llamadas HTTP directas a un servicio externo, creando una dependencia dura que sería difícil de probar o cambiar. Estos detalles de implementación deberían ocultarse tras interfaces, de acuerdo con los principios de Clean Architecture, y no exponerse directamente en la lógica de negocio.

Finalmente, al concluir la función `create_order`, completamos el procesamiento del pedido:

```python
    if payment_result.status_code != 200:
        conn.close()
        return jsonify({"error": "Payment failed"}), 400

    # Order creation directly in route handler
    order_id = conn.execute(
        "INSERT INTO orders (customer_id, total_price, status) VALUES (?, ?, ?)",
        (data["customer_id"], total_price, "PAID"),
    ).lastrowid

    # Order items creation and inventory update
    for item in data["items"]:
        conn.execute(
            "INSERT INTO order_items (order_id, product_id, quantity, price) VALUES (?, ?, ?, ?)",
            (order_id, item["product_id"], item["quantity"], price),
        )
        conn.execute(  # Update inventory
            "UPDATE products SET stock = stock - ? WHERE id = ?",
            (item["quantity"], item["product_id"]),
        )
    conn.commit()
    conn.close()
    return jsonify({"order_id": order_id, "status": "success"}), 201
```

El análisis del código revela problemas arquitectónicos fundamentales en todo este manejador. Las sentencias SQL directas se entrelazan con la lógica de negocio, las respuestas HTTP y las llamadas a servicios externos, todo ello comprimido en una única función sin separación de responsabilidades. Esta estructura viola el Principio de Responsabilidad Única (*Single Responsibility Principle*) que discutimos en el [Capítulo 2](https://subscription.packtpub.com/book/programming/9781836642893/2) y hace que los cambios sean sumamente arriesgados, ya que las modificaciones en un área afectan con frecuencia a funcionalidades aparentemente no relacionadas.

El sistema carece del rico modelo de dominio que establecimos en el [Capítulo 4](https://subscription.packtpub.com/book/programming/9781836642893/4), ya que los pedidos y productos solo existen como registros de base de datos y diccionarios, en lugar de entidades adecuadas con comportamiento encapsulado y reglas de negocio.

> **Figura 11.3:** Responsabilidades entrelazadas en el manejador de procesamiento de pedidos actual.

La Figura 11.3 ilustra cómo un único manejador de rutas de Flask abarca múltiples responsabilidades que deberían estar separadas de acuerdo con los principios de Clean Architecture. La lógica de negocio está conectada directamente a preocupaciones de infraestructura como conexiones de base de datos y APIs externas, violando la Regla de Dependencia que exploramos en el [Capítulo 1](https://subscription.packtpub.com/book/programming/9781836642893/1).

Basándonos en nuestro análisis, hemos identificado problemas arquitectónicos clave que debemos abordar en nuestra transformación:

- **Violaciones de límites:** El manejador de rutas cruza múltiples límites arquitectónicos, mezclando preocupaciones web, lógica de negocio e infraestructura.
- **Falta de modelo de dominio:** Necesitamos establecer entidades de dominio apropiadas como `Order` y `Product` como el núcleo de nuestro sistema.
- **Inversión de dependencias requerida:** Las dependencias directas de infraestructura deben reemplazarse con abstracciones siguiendo los principios del [Capítulo 2](https://subscription.packtpub.com/book/programming/9781836642893/2).
- **Separación de interfaces necesaria:** Interfaces claras entre las capas arquitectónicas ayudarán a mantener los límites adecuados.

Existen problemas arquitectónicos clave en nuestro proceso de creación de pedidos; podemos ver un sistema que evolucionó sin una guía arquitectónica. La lógica de negocio, el acceso a datos y los servicios externos están fuertemente acoplados, sin límites claros entre las responsabilidades. El sistema funciona, pero su estructura hace que sea cada vez más difícil de mantener, extender o probar.

Con esta comprensión del sistema actual, ya estamos listos para comenzar nuestro viaje de transformación. Empezaremos estableciendo un modelo de dominio limpio en la siguiente sección, creando límites adecuados entre capas a medida que refactorizamos progresivamente hacia una Clean Architecture.

#### Etapa 1: Establecimiento de los límites del dominio

Habiendo analizado nuestro sistema heredado, comenzamos nuestra transformación estableciendo un modelo de dominio limpio que servirá como nuestra base arquitectónica. Comenzar con la capa de Dominio proporciona un núcleo estable alrededor del cual podemos reconstruir progresivamente las capas externas de nuestro sistema.

En nuestro sistema de procesamiento de pedidos, necesitamos extraer los conceptos de dominio implícitos que están enterrados en nuestras consultas de base de datos y en la lógica del controlador. Las entidades más críticas en nuestro sistema parecen ser:

- **`Order` (Pedido):** La entidad de negocio central.
- **`Customer` (Cliente):** El comprador que realiza el pedido.
- **`Product` (Producto):** Los artículos que se adquieren.
- **`OrderItem` (Artículo del pedido):** La asociación entre pedidos y productos.

Comencemos implementando la entidad `Order` y sus objetos de valor relacionados:

```python
# order_system/domain/entities/order.py
from dataclasses import dataclass, field
from datetime import datetime
from enum import Enum
from typing import List, Optional
from uuid import UUID, uuid4


class OrderStatus(Enum):
    CREATED = "CREATED"
    PAID = "PAID"
    FULFILLING = "FULFILLING"
    SHIPPED = "SHIPPED"
    DELIVERED = "DELIVERED"
    CANCELED = "CANCELED"


@dataclass
class OrderItem:
    product_id: UUID
    quantity: int
    price: float

    @property
    def total_price(self) -> float:
        return self.price * self.quantity
```

Aquí hemos definido un enum `OrderStatus` para reemplazar las constantes de cadena utilizadas anteriormente en todo el código. También hemos creado un objeto de valor `OrderItem` para representar la relación entre pedidos y productos. Este enfoque se alinea con el patrón de objetos de valor que exploramos en el [Capítulo 4](https://subscription.packtpub.com/book/programming/9781836642893/4), creando objetos inmutables que representan conceptos importantes del dominio.

Ahora implementemos la entidad `Order` en sí:

```python
@dataclass
class Order:
    customer_id: UUID
    items: List[OrderItem] = field(default_factory=list)
    id: UUID = field(default_factory=uuid4)
    status: OrderStatus = OrderStatus.CREATED
    created_at: datetime = field(default_factory=lambda: datetime.now())
    updated_at: Optional[datetime] = None

    @property
    def total_price(self) -> float:
        return sum(item.total_price for item in self.items)

    def add_item(self, item: OrderItem) -> None:
        self.items.append(item)
        self.updated_at = datetime.now()

    def mark_as_paid(self) -> None:
        if self.status != OrderStatus.CREATED:
            raise ValueError(f"Cannot mark as paid: order is {self.status.value}")
        self.status = OrderStatus.PAID
        self.updated_at = datetime.now()
```

Nuestra entidad `Order` ahora encapsula adecuadamente los conceptos de negocio centrales que antes estaban dispersos por toda la base de código. Hemos implementado métodos que hacen cumplir las reglas de negocio, como la validación de transiciones de estado al marcar un pedido como pagado. Estas validaciones estaban anteriormente enterradas en la lógica del controlador, pero ahora residen en su lugar apropiado dentro de la propia entidad.

Necesitamos crear las entidades de dominio restantes para completar nuestro modelo central:

```python
# order_system/domain/entities/product.py
from dataclasses import dataclass, field
from uuid import UUID, uuid4


@dataclass
class Product:
    name: str
    price: float
    stock: int
    id: UUID = field(default_factory=uuid4)

    def decrease_stock(self, quantity: int) -> None:
        if quantity <= 0:
            raise ValueError("Quantity must be positive")
        if quantity > self.stock:
            raise ValueError(f"Insufficient stock: requested {quantity}, available {self.stock}")
        self.stock -= quantity
```

La entidad `Product` ahora encapsula la lógica de gestión de inventario que antes estaba repartida entre los métodos del controlador. Hace cumplir reglas de negocio como evitar un stock negativo o extracciones excesivas. Este es un ejemplo del principio *Tell, Don't Ask* (Dile, no le preguntes) que ayuda a mantener la integridad del dominio.

Con nuestras entidades de dominio centrales definidas, necesitamos crear abstracciones para los servicios y repositorios de soporte. Siguiendo el Principio de Inversión de Dependencias, definiremos las interfaces que el dominio necesita sin acoplarnos a implementaciones específicas:

```python
# order_system/domain/repositories/order_repository.py
from abc import ABC, abstractmethod
from typing import List, Optional
from uuid import UUID
from order_system.domain.entities.order import Order


class OrderRepository(ABC):
    @abstractmethod
    def save(self, order: Order) -> None:
        """Save an order to the repository"""
        pass

    @abstractmethod
    def get_by_id(self, order_id: UUID) -> Optional[Order]:
        """Retrieve an order by its ID"""
        pass

    @abstractmethod
    def get_by_customer(self, customer_id: UUID) -> List[Order]:
        """Retrieve all orders for a customer"""
        pass
```

Este `OrderRepository` abstracto define las operaciones que nuestra capa de Dominio necesita sin especificar cómo se implementan. Crearemos interfaces similares para `ProductRepository` y otros repositorios necesarios. Estas abstracciones son un elemento crucial de Clean Architecture, ya que permiten que nuestra capa de Dominio permanezca independiente de mecanismos de persistencia específicos.

Si recuerdas el sistema de gestión de tareas de capítulos anteriores, establecimos interfaces de repositorio similares como `TaskRepository` en el [Capítulo 5](https://subscription.packtpub.com/book/programming/9781836642893/5). Ambos siguen el mismo patrón: definir métodos abstractos que los componentes del dominio requieren sin especificar detalles de implementación. Esta coherencia demuestra cómo los principios de Clean Architecture se aplican a través de diferentes dominios y aplicaciones, creando un patrón fiable para mantener límites apropiados.

A continuación, definamos las interfaces de servicio para operaciones externas como pagos y notificaciones:

```python
# order_system/domain/services/payment_service.py
from abc import ABC, abstractmethod
from dataclasses import dataclass
from typing import Optional
from order_system.domain.entities.order import Order


@dataclass
class PaymentResult:
    success: bool
    error_message: Optional[str] = None


class PaymentService(ABC):
    @abstractmethod
    def process_payment(self, order: Order) -> PaymentResult:
        """Process payment for an order"""
        pass
```

Con estos componentes centrales de dominio definidos, hemos creado una base limpia para nuestro sistema. Las reglas y conceptos de negocio que antes estaban dispersos entre controladores y funciones de utilidad ahora tienen un hogar adecuado en un modelo de dominio bien estructurado. Esta transformación proporciona varios beneficios inmediatos:

- **Centralización de reglas de negocio:** Reglas como "no se puede marcar como PAID un pedido que no esté en estado CREATED" ahora están definidas explícitamente en el modelo de dominio.
- **Mejora en la capacidad de prueba (*testability*):** Las entidades y servicios de dominio se pueden probar de forma aislada sin requerir conexiones a bases de datos ni frameworks web.
- **Límites más claros:** La separación entre los conceptos centrales del negocio y las preocupaciones de infraestructura es ahora explícita.
- **Modelo de dominio más rico:** Hemos pasado de registros anémicos de base de datos a un modelo de dominio rico con comportamiento.

Tomémonos un momento para revisar esta nueva capa de Dominio:

> **Figura 11.4:** El modelo de dominio recién establecido con límites limpios.

Este diagrama ilustra nuestro primer gran paso de transformación: establecer una capa de Dominio adecuada con límites limpios. Hemos creado entidades, objetos de valor e interfaces de servicio que encapsulan nuestros conceptos y reglas de negocio centrales. Al comparar esto con la Figura 11.2, podemos ver un progreso significativo hacia el desenredo de las responsabilidades que antes estaban mezcladas en nuestra implementación heredada de controladores.

##### Estrategias de integración incremental

En las transformaciones del mundo real, un error común es intentar implementar toda la Clean Architecture de forma aislada antes de la integración. Este enfoque de lanzamiento "Big Bang" introduce un riesgo significativo, ya que para el momento en que ocurre la integración, el sistema de producción puede haber evolucionado sustancialmente, generando complejos conflictos de fusión (*merge conflicts*) y cambios de comportamiento inesperados.

Para mitigar este riesgo, se pueden emplear varias estrategias de integración incremental:

- **Patrón Adapter (Adaptador):** Crear adaptadores que sirvan de puente entre los componentes heredados y las nuevas entidades de dominio, permitiendo que coexistan dentro del sistema en ejecución. Esto permite una adopción gradual sin interrumpir la funcionalidad existente.
- **Implementación paralela:** Implementar nueva funcionalidad utilizando Clean Architecture junto al código heredado, utilizando *feature flags* para controlar qué implementación gestiona las solicitudes. Esto proporciona un mecanismo sencillo de reversión si surgen problemas.
- **Patrón Strangler Fig (Higuera estranguladora):** Reemplazar incrementalmente partes de la aplicación heredada manteniendo las mismas interfaces externas, suplantando gradualmente la implementación antigua hasta que pueda eliminarse de forma segura ([https://martinfowler.com/bliki/StranglerFigApplication.html](https://martinfowler.com/bliki/StranglerFigApplication.html)).
- **Modo sombra (*Shadow mode*):** Ejecutar nuevas implementaciones junto con el código de producción mediante el uso de un proxy que duplica todas las solicitudes. Esto le da a la nueva implementación la oportunidad de procesar su copia de la solicitud y comparamos las salidas con las del sistema heredado. Esto valida el comportamiento sin afectar a los usuarios.

A lo largo de esta transformación incremental, las pruebas de regresión exhaustivas son absolutamente esenciales. Antes de realizar cualquier cambio arquitectónico, establece una suite de pruebas completa que capture el comportamiento actual del sistema. Estas pruebas cumplen múltiples propósitos:

- Verifican que la refactorización no haya roto la funcionalidad existente.
- Documentan el comportamiento actual del sistema para referencia futura.
- Aportan confianza a los interesados de que la transformación se está desarrollando de forma segura.

Como discutimos en el [Capítulo 8](https://subscription.packtpub.com/book/programming/9781836642893/8), las pruebas proporcionan redes de seguridad cruciales durante la transformación arquitectónica. Para nuestro sistema de procesamiento de pedidos, estableceríamos pruebas de extremo a extremo (*end-to-end*) que verifiquen los flujos completos de pedidos antes de comenzar nuestra transformación, complementándolas luego con pruebas más granulares a medida que establecemos límites arquitectónicos limpios.

Al adoptar estas estrategias incrementales y priorizar las pruebas de regresión, podemos transformar nuestro sistema mientras mantenemos la estabilidad y continuamos entregando valor de negocio. En la siguiente sección, comenzaremos a implementar el enfoque de integración en producción descrito anteriormente, basándonos en nuestro modelo de dominio mediante la implementación de la capa de Adaptadores de Interfaz (*Interface Adapters*).

#### Etapa 2: Implementación de la capa de interfaz

Con nuestras entidades e interfaces de dominio establecidas, nos enfrentamos ahora a un desafío de transición crítico: integrar esta base limpia con nuestra base de código existente. A diferencia del desarrollo desde cero, la transformación nos exige evolucionar nuestro sistema de manera incremental mientras mantenemos una operación continua. La capa de Interfaz nos brinda la primera oportunidad de tender un puente entre las arquitecturas antigua y nueva.

##### Identificación de los límites de transformación

El primer paso en nuestra transformación es identificar puntos de corte viables donde podamos introducir interfaces limpias sin alterar excesivamente el sistema existente. Al observar nuevamente nuestro controlador heredado, el proceso de creación de pedidos destaca como un límite natural:

```python
# order_system/app.py
@app.route('/orders', methods=['POST'])
def create_order():
    data = request.get_json()

    # Input validation mixed with business logic
    if not data or not 'customer_id' in data or not 'items' in data:
        return jsonify({'error': 'Missing required fields'}), 400

    # Direct database access in route handler
    conn = get_db_connection()

    # Business logic implementation
    # ... existing implementation ...

    return jsonify({'order_id': order_id, 'status': 'success'}), 201
```

Este método de controlador representa un flujo de trabajo autónomo con entradas y salidas claras, lo que lo convierte en un candidato ideal para nuestra transformación inicial. Antes de modificar este código, necesitamos establecer una cobertura de pruebas exhaustiva que capture su comportamiento actual. Estas pruebas servirán como nuestra red de seguridad durante la refactorización, asegurando que mantengamos la funcionalidad mientras mejoramos la arquitectura:

```python
# test_order_creation.py
def test_create_order_success():
    # Setup test data and expected results
    response = client.post(
        "/orders", json={"customer_id": "12345", "items": [{"product_id": "789", "quantity": 2}]}
    )

    # Verify status code and response structure
    assert response.status_code == 201
    assert "order_id" in response.json

    # Verify database state - order was created with correct values
    conn = get_db_connection()
    order = conn.execute(
        "SELECT * FROM orders WHERE id = ?", (response.json["order_id"],)
    ).fetchone()
    assert order["status"] == "PAID"


# Additional order creation test scenarios ...
```

Con las pruebas en su lugar, podemos comenzar a implementar los componentes de la capa de Interfaz que tenderán un puente entre nuestro modelo de dominio limpio y la infraestructura existente.

##### Implementación de adaptadores de repositorio

Nuestro primer paso es crear adaptadores de repositorio que satisfagan nuestras interfaces limpias de dominio mientras interactúan con el esquema de base de datos existente. Este componente crucial conecta nuestras entidades de dominio y la infraestructura heredada.

```python
# order_system/infrastructure/repositories/sqlite_order_repository.py
class SQLiteOrderRepository(OrderRepository):

    # ... truncated implementation
    
    def save(self, order: Order) -> None:
        conn = sqlite3.connect(self.db_path)
        
        try:
            cursor = conn.cursor()
            # Check if order exists and perform insert or update
            if self._order_exists(conn, order.id):
                # ... SQL update operation ...
            else:
                # ... SQL insert operation ...
                
                # ... SQL operations for order items ...
            conn.commit()
        except Exception as e:
            conn.rollback()
            raise RepositoryError(f"Failed to save order: {str(e)}")
        finally:
            conn.close()
```

Este adaptador de repositorio juega un papel vital en nuestra estrategia de transformación. Tal vez recuerdes del [Capítulo 6](https://subscription.packtpub.com/book/programming/9781836642893/6) que introdujimos implementaciones de repositorio similares para nuestro sistema de gestión de tareas. Al igual que en aquellos ejemplos, este adaptador implementa nuestra interfaz limpia `OrderRepository` (de la Etapa 1) mientras gestiona los detalles de nuestro esquema de base de datos existente. El adaptador traduce entre entidades de dominio y registros de base de datos, gestionando el desfase de impedancia (*impedance mismatch*) entre nuestro rico modelo de dominio y la estructura relacional plana.

También implementaríamos un `SQLiteProductRepository` similar que siga el mismo patrón, implementando una interfaz de dominio limpia mientras interactúa con el esquema de base de datos existente. Estas implementaciones de repositorio manejan todos los detalles de acceso a la base de datos, la gestión de conexiones y el manejo de errores, proporcionando una interfaz limpia para el resto de nuestra arquitectura.

Además, implementaríamos adaptadores para servicios externos como el procesamiento de pagos. Estos adaptadores de servicio seguirían el mismo patrón, implementando nuestras interfaces limpias de dominio mientras encapsulan los detalles de las interacciones con los servicios externos. Por brevedad, no mostraremos estas implementaciones aquí, pero el código completo está disponible en el repositorio de GitHub del libro.

Con estos adaptadores de infraestructura en su lugar, ahora disponemos de un puente entre nuestro modelo de dominio limpio y la infraestructura heredada. Esto nos permite implementar casos de uso que operen con entidades de dominio adecuadas mientras interactúan de forma fluida con la base de datos existente y los servicios externos a través de interfaces, en lugar de interactuar directamente con implementaciones concretas.

##### Construcción de casos de uso limpios

Ahora que contamos con adaptadores de repositorios y servicios que se conectan a nuestra infraestructura existente, podemos implementar los casos de uso que orquestan nuestra lógica de negocio. En el [Capítulo 5](https://subscription.packtpub.com/book/programming/9781836642893/5), establecimos que los casos de uso sirven como reglas de negocio específicas de la aplicación que coordinan las entidades de dominio para cumplir con los requisitos específicos del usuario. Siguiendo este patrón, veamos el caso de uso de creación de pedidos que reemplazará nuestra enredada implementación heredada:

```python
# order_system/application/use_cases/create_order.py
from dataclasses import dataclass
from typing import List, Dict, Any
from uuid import UUID


@dataclass
class CreateOrderRequest:
    customer_id: UUID
    items: List[Dict[str, Any]]


@dataclass
class CreateOrderUseCase:
    order_repository: OrderRepository
    product_repository: ProductRepository
    payment_service: PaymentService

    def execute(self, request: CreateOrderRequest) -> Order:
        # Create order entity with basic information
        order = Order(customer_id=request.customer_id)

        # Add items to order, checking inventory
        for item_data in request.items:
            product_id = UUID(item_data["product_id"])
            quantity = item_data["quantity"]

            # ... inventory validation logic ...

            # Update inventory
            product.decrease_stock(quantity)
            self.product_repository.update(product)
```

El método `execute` de nuestro caso de uso comienza creando una entidad `Order` y le agrega artículos, comprobando la disponibilidad de inventario en el proceso. Observa cómo trabaja con entidades de dominio adecuadas en lugar de registros crudos de base de datos.

Examinemos ahora el resto del método `execute`:

```python
        # Process payment
        payment_result = self.payment_service.process_payment(order)
        if not payment_result.success:
            raise ValueError(f"Payment failed: {payment_result.error_message}")
        
        # Mark order as paid and save
        order.mark_as_paid()
        self.order_repository.save(order)
        
        return order
```

La segunda mitad de nuestro método `execute` continúa el proceso de creación del pedido gestionando el procesamiento del pago, actualizando el estado del pedido y guardando el pedido completado.

Este caso de uso demuestra la separación de responsabilidades de Clean Architecture en acción. Orquesta el proceso de creación de pedidos mediante:

1. La creación de una entidad `Order` con la información básica.
2. La adición de artículos al pedido, verificando el inventario.
3. El procesamiento del pago.
4. La actualización del estado del pedido y su almacenamiento.

Cada paso interactúa con el modelo de dominio a través de interfaces bien definidas, sin conocimiento de la infraestructura subyacente. El caso de uso depende de las interfaces abstractas `OrderRepository`, `ProductRepository` y `PaymentService`, y no de implementaciones concretas.

Observa cómo las reglas de negocio ahora son explícitas y están centralizadas en este caso de uso. La verificación de inventario, el procesamiento de pagos y la gestión del estado del pedido fluyen a través de un proceso limpio y organizado en lugar de estar dispersos en métodos de controlador y funciones de utilidad. Esta claridad hace que el código sea más mantenible y adaptable a los requisitos cambiantes.

##### Implementación de controladores limpios

Con nuestros repositorios y casos de uso en su lugar, ahora implementamos controladores que sirvan de puente entre nuestro framework web y el núcleo de la aplicación. Como establecimos en el [Capítulo 6](https://subscription.packtpub.com/book/programming/9781836642893/6), los controladores actúan como capas de traducción en el límite de nuestra arquitectura, convirtiendo los formatos de solicitud externa en entradas que nuestros casos de uso puedan procesar. Estos controladores mantienen la separación entre nuestro núcleo de aplicación y los mecanismos de entrega, asegurando que las preocupaciones específicas de la web no penetren en nuestra Clean Architecture:

```python
# order_system/interfaces/controllers/order_controller.py
from dataclasses import dataclass
from typing import Dict, Any
from uuid import UUID


@dataclass
class OrderController:
    create_use_case: CreateOrderUseCase
    
    def handle_create_order(self, request_data: Dict[str, Any]) -> Dict[str, Any]:
        try:
            # Transform web request to domain request format
            customer_id = UUID(request_data['customer_id'])
            items = request_data['items']
            
            request = CreateOrderRequest(
                customer_id=customer_id,
                items=items
            )
            
            # Execute use case
            order = self.create_use_case.execute(request)
            
            # Transform domain response to web response format
            return {
                'order_id': str(order.id),
                'status': order.status.value
            }
        except ValueError as e:
            # ... exception logic
```

Este controlador muestra los límites de Clean Architecture en funcionamiento, actuando como una capa de traducción entre las solicitudes externas y nuestras operaciones de dominio. El núcleo de este controlador es la línea única `order = self.create_use_case.execute(request)`, que representa el límite crítico entre nuestra capa de Interfaz y el núcleo de la aplicación. Observa que el controlador no hace referencia a Flask, a códigos de estado HTTP ni al formateo JSON. Estas preocupaciones específicas de la web se manejan en el límite del framework, manteniendo una separación limpia entre nuestra lógica de aplicación y el mecanismo de entrega. Esta independencia del framework permite que nuestro controlador permanezca enfocado en su responsabilidad central: transformar solicitudes externas en operaciones de dominio y traducir los resultados de regreso a un formato adecuado para el llamador.

#### Etapa 3: Estrategia de integración: Uniendo las implementaciones heredadas y limpias

Ahora llega el paso crucial: integrar nuestra implementación limpia con el sistema existente. En lugar de reemplazar inmediatamente todo el manejador de rutas heredado, lo modificaremos para que delegue en nuestro controlador limpio mediante el patrón Adapter:

```python
# Modified route in order_system/app.py
from flask import request, jsonify


@app.route('/orders', methods=['POST'])
def create_order():
    data = request.get_json()
    
    # Basic input validation remains in the route handler
    if not data or not 'customer_id' in data or not 'items' in data:
        return jsonify({'error': 'Missing required fields'}), 400
    
    try:
        # Feature flag to control which implementation handles the request
        if app.config.get('USE_CLEAN_ARCHITECTURE', False):
            # Use the clean implementation
            result = order_controller.handle_create_order(data)
            return jsonify(result), 201
        else:
            # ... original implementation remains here ...
    except ValidationError as e:
        return jsonify({'error': str(e)}), 400
    except SystemError:
        return jsonify({'error': 'Internal server error'}), 500
```

La parte clave de esta modificación es el condicional de la *feature flag*. Cuando `USE_CLEAN_ARCHITECTURE` está habilitado, delegamos el procesamiento del pedido a nuestro nuevo controlador, que a su vez invoca el caso de uso limpio. Esto crea una vía controlada hacia nuestra implementación de Clean Architecture sin perturbar la ruta de código existente. La *feature flag* nos brinda un mecanismo simple para alternar entre implementaciones, ya sea globalmente o para solicitudes específicas.

Este manejador de rutas modificado demuestra varios patrones de transformación clave:

- **Control mediante Feature Flags:** Usamos un ajuste de configuración para determinar qué implementación procesa la solicitud, lo que nos permite realizar una transición gradual del tráfico.
- **Interfaces coherentes:** Ambas implementaciones producen formatos de respuesta idénticos, garantizando una transición fluida desde la perspectiva del usuario.
- **Migración incremental:** El código heredado permanece completamente funcional, sirviendo como alternativa de respaldo (*fallback*) si surgen problemas con la implementación limpia.
- **Traducción de excepciones:** Mapeamos las excepciones específicas del dominio a respuestas HTTP adecuadas en el límite del framework.

Al integrarnos con frameworks específicos como Flask, debemos atender a los detalles propios del framework en los límites del sistema. En el caso de Flask, necesitamos configurar nuestro contenedor de inyección de dependencias, registrar nuestros componentes de Clean Architecture y establecer el mecanismo de *feature flagging*. Creamos un punto de configuración central que instancia todos los componentes necesarios (repositorios, servicios, casos de uso y controladores) y los conecta de acuerdo con las reglas de dependencia de Clean Architecture. Esta configuración ocurre al inicio de la aplicación, manteniendo todo el código de inicialización específico del framework en el borde del sistema, que es donde corresponde. Vimos esto en acción en nuestra aplicación de gestión de tareas en el [Capítulo 7](https://subscription.packtpub.com/book/programming/9781836642893/7).

##### Enfoque de transformación incremental

Durante este proceso de transformación, las pruebas exhaustivas son absolutamente esenciales. Aprovechamos nuestra suite de pruebas de regresión para asegurar que la refactorización no haya roto la funcionalidad existente. Estas pruebas verifican tanto la implementación heredada como nuestros nuevos componentes de Clean Architecture, brindando confianza de que la transformación mantiene la paridad funcional.

Cada paso de nuestra transformación se valida cuidadosamente antes de proceder al siguiente. No avanzamos hasta haber verificado que nuestros cambios mantienen el comportamiento y la estabilidad del sistema. Este enfoque incremental minimiza el riesgo y nos permite entregar valor continuamente a lo largo de todo el proceso de transformación.

A un alto nivel, nuestro enfoque se alinea con el patrón Strangler Fig ([https://martinfowler.com/bliki/StranglerFigApplication.html](https://martinfowler.com/bliki/StranglerFigApplication.html)), donde reemplazamos gradualmente partes de la aplicación heredada mientras mantenemos las mismas interfaces externas. Este enfoque minimiza el riesgo al permitir una validación incremental y una reversión rápida si fuera necesario.

> **Figura 11.5:** Arquitectura actual del sistema mostrando implementaciones paralelas.

La Figura 11.5 ilustra nuestro estado arquitectónico actual, con las implementaciones heredada y limpia coexistiendo en el sistema. Los componentes heredados representan el código enredado y no estructurado que mezcla directamente la lógica de negocio con las preocupaciones de infraestructura. Por el contrario, la implementación de Clean Architecture muestra una separación adecuada de responsabilidades con capas diferenciadas e interfaces bien definidas.

A través de este enfoque de implementación incremental, hemos logrado un progreso significativo en nuestro viaje de transformación:

- Hemos establecido un modelo de dominio limpio con entidades y objetos de valor apropiados.
- Hemos implementado adaptadores de repositorio que unen nuestro modelo de dominio con la base de datos existente.
- Hemos creado casos de uso que orquestan la lógica de negocio utilizando nuestro modelo de dominio.
- Hemos construido controladores que traducen entre las solicitudes web y nuestro lenguaje de dominio.
- Hemos integrado nuestra implementación limpia junto al código heredado utilizando el patrón Adapter.

A través de este enfoque de implementación incremental, hemos demostrado cómo transformar un sistema heredado utilizando los principios de Clean Architecture a la vez que mantenemos la estabilidad del sistema y la funcionalidad a lo largo de todo el proceso.

#### Etapa 4: Etapa de optimización

Aunque nuestro ejemplo se ha centrado principalmente en las etapas de cimientos, interfaz e integración, una transformación completa eventualmente incluiría una etapa de optimización. Esta fase final generalmente implica ajustes de rendimiento, una ampliación de la cobertura de pruebas y la mejora de los patrones de manejo de errores en función del uso en el mundo real.

En lugar de proporcionar ejemplos detallados de esta etapa, señalaremos que la optimización debe abordarse con la misma mentalidad incremental. Los equipos deben priorizar aquellas optimizaciones que aporten el mayor valor de negocio, eliminando gradualmente las *feature flags* a medida que las implementaciones limpias demuestren ser estables y, en última instancia, desmantelando por completo las rutas de código heredadas.

La etapa de optimización reconoce que la transformación arquitectónica no es un esfuerzo puntual, sino más bien un proceso continuo de refinamiento que equilibra la excelencia técnica con las prioridades del negocio. Los equipos deben definir métricas claras para determinar cuándo se ha alcanzado un resultado "suficientemente bueno", evitando la trampa del perfeccionismo sin fin.

---

### Sección 11.4: Resumen

En este capítulo, hemos explorado cómo aplicar los principios de Clean Architecture a sistemas heredados mediante una transformación sistemática. Comenzamos examinando cómo evaluar los sistemas existentes a través del prisma de Clean Architecture, identificando violaciones arquitectónicas y creando un enfoque por etapas para la transformación.

Establecimos un marco para construir la alineación de los interesados traduciendo la deuda técnica a términos de impacto en el negocio y recopilando una comprensión más profunda del dominio mediante técnicas colaborativas como Event Storming. Este enfoque colaborativo fundamentó directamente nuestro plan de implementación por etapas, basando nuestras decisiones arquitectónicas en las prioridades del negocio.

A través de nuestro ejemplo de procesamiento de pedidos, demostramos un enfoque de implementación progresiva que mantiene la estabilidad del sistema mientras establece límites arquitectónicos limpios. Comenzamos con la capa de Dominio, creando entidades y objetos de valor apropiados que encapsulan reglas de negocio anteriormente dispersas por toda la base de código. Luego implementamos interfaces de repositorio que protegen nuestro dominio de los detalles de infraestructura, seguidas de casos de uso que orquestan las operaciones de negocio.

La capa de Adaptadores de Interfaz proporcionó un puente entre nuestra implementación limpia y el código heredado, permitiendo una adopción incremental mediante *feature flags* y patrones de adaptación. Este enfoque por etapas nos permitió validar nuestra transformación a la vez que minimizábamos el riesgo, demostrando cómo Clean Architecture puede aplicarse de manera pragmática a sistemas del mundo real.

Siguiendo estos patrones de transformación, puedes mejorar sistemáticamente la calidad arquitectónica en sistemas existentes, reduciendo los costes de mantenimiento y aumentando la adaptabilidad mientras continúas entregando valor de negocio. Este enfoque encarna los principios fundamentales de Clean Architecture al tiempo que reconoce las restricciones prácticas que impone la evolución de sistemas en producción.

---

### Sección 11.5: Lecturas complementarias

- **Working Effectively with Legacy Code** ([https://www.oreilly.com/library/view/working-effectively-with/0131177052/](https://www.oreilly.com/library/view/working-effectively-with/0131177052/)) por Michael Feathers. Proporciona técnicas para trabajar con bases de código existentes, incluyendo estrategias para introducir pruebas de forma segura y realizar mejoras incrementales.
- **Event Storming** ([https://www.eventstorming.com](https://www.eventstorming.com/)). Un excelente recurso para aprender más sobre las sesiones de Event Storming y su planificación.

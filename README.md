# Microservicios
Traducción de: http://martinfowler.com/articles/microservices.html

El término "Arquitectura de Microservicios" ha surgido en los últimos años para describir una forma particular de diseñar aplicaciones de software como suites de servicios independientemente desplegables. Aunque no existe una definición precisa de este estilo de arquitectura, hay ciertas características comunes alrededor de organización en torno a la capacidad del negocio, despliegue automatizado, inteligencia en endpoints (puntos de evaluación), y control descentralizado de lenguas y datos. 


"Microservicios" - otro nuevo termino en las agentadas calles de la arquitectura de software. A pesar de que nuestra inclinación natural es pasar estas cosas con una mirada despectiva, este pedacito de la terminología describe un estilo de sistemas de software que encontramos más y más atractivo. Hemos visto muchos proyectos que utilizan este estilo en los últimos años, y resultados hasta ahora han sido positivos, tanto que para muchos de nuestros colegas se está volviendo el estilo predeterminado para construir aplicaciones empresariales. Tristemente, sin embargo, no hay mucha información que describa lo que es el estilo de microservicios y cómo hacerlo.

En resumen, el estilo arquitectónico de microservicios es un enfoque para desarrollar una sola aplicación como una suite de servicios pequeños, cada uno corriendo en su propio proceso y comunicándose con mecanismos ligeros, a menudo con una API de recurso HTTP. Estos servicios están construidos alrededor de las capacidades del negocio y desplegables independientemente por la maquinaria de implementación completamente automatizada. Hay un mínimo de gestión centralizada de estos servicios, que pueden ser escritos en diferentes lenguajes de programación y usan diferentes tecnologías de almacenamiento de datos.

Para comenzar a explicar el estilo de microservicios es útil compararlo con el estilo monolítico: una aplicación monolítica construida como una sola unidad. Las aplicaciones empresariales a menudo se construyen en tres partes principales: una interfaz de usuario del lado del cliente (que consiste de páginas HTML y JavaScript corriendo en un navegador en la máquina del usuario) una base de datos (que consiste de muchas tablas insertadas en unos sistemas de gestión de base de datos común y usualmente relacional), y una aplicación del lado del servidor. La aplicación del lado del servidor manejará las peticiones HTTP, ejecutará la lógica de dominio, recuperará y actualizará los datos de la base de datos y seleccionará y llenará vistas HTML que se enviarán al navegador. Esta aplicación del lado del servidor es un monolito - un ejecutable lógico único. Cualquier cambio en el sistema involucra la creación y despliegue de una nueva versión de la aplicación del lado del servidor.

Tal servidor monolítico es una forma natural de acercarse a construir tal sistema. Toda la lógica para manejar una petición corre en un solo proceso, permitiendo utilizar las funciones básicas del lenguaje para dividir la aplicación en clases, funciones y espacios de nombres. Con algo de cuidado, se puede correr y probar la aplicación en la laptop de un desarrollador, y el uso de una tubería de despliegue para asegurarse de que los cambios están debidamente probados y desplegados en producción. Se puede escalar horizontalmente el monolito corriendo muchas instancias detrás de un equilibrador de carga.

Las aplicaciones monolíticas pueden tener éxito, pero mucha gente se está sintiendo frustrada con ellas - especialmente a medida que más aplicaciones se están desplegando a la nube. Cambiar ciclos que están unidos juntos - un cambio hecho a una pequeña parte de la aplicación, requiere que todo el monolito sea reconstruido y desplegado. Con el tiempo, seguido es difícil mantener una buena estructura modular, haciendo difícil mantener los cambios que deban afectar a un solo módulo dentro de ese módulo. El escalamiento requiere escalamiento de toda la aplicación en lugar de partes de ella que requieren un mayor recurso.

 
# Características de la arquitectura de microservicios

No podemos decir que hay una definición formal de del estilo arquitectónico de microservicios, pero podemos tratar de describir que vemos como características comunes para las arquitecturas que se ajusten a la etiqueta. Como con cualquier definición que describa las características comunes, no todas las arquitecturas de microservicios tienen todas las características, pero esperamos que la mayoría de las arquitecturas de microservicios muestren la mayoría de las características. Mientras que nosotros, los autores hemos sido miembros activos de esta algo suelta comunidad, nuestra intención es la de intentar una descripción de lo que vemos en nuestro propio trabajo y en los esfuerzos similares por parte de los equipos que conocemos. En particular, no estamos sentando alguna definición para ajustarse.

#Componentización a través de Servicios

Durante el tiempo que hemos estado involucrados en la industria del software, ha habido un deseo de construir sistemas al conectar juntos los componentes, Algo así como la forma en que vemos como las cosas se hacen en el mundo físico. Durante el último par de décadas hemos visto un progreso considerable con grandes compendios de bibliotecas comunes que forman parte de la mayoría de las plataformas de lenguaje.

Cuando se habla de componentes nos encontramos con la difícil definición de qué hace un componente. Nuestra definición es que un componente es una unidad de software que es independientemente reemplazable y actualizable.

las arquitecturas de microservicios utilizarán bibliotecas, pero su principal forma de componentizar su propio software es al descomponerse en servicios. Definimos las bibliotecas como componentes que están vinculados a un programa y son llamadas usando llamadas a funciones en memoria, mientras que los servicios son componentes fuera de proceso que se comunican con un mecanismo como una solicitud de servicio web, o una llamada a procedimiento remoto. (Este es un concepto diferente a la de un objeto de servicio en muchos programas orientados a objetos.)

Una razón principal de utilizar servicios como componentes (en lugar de bibliotecas) es que los servicios son independientemente desplegables. Si se tiene una aplicación que consiste en bibliotecas múltiples en un solo proceso, un cambio a cualquier componente simple resulta en tener que re-desplegar toda la aplicación. Pero si esta aplicación se descompone en múltiples servicios, se puede esperar muchos cambios de servicio únicos sólo para requerir que dicho servicio sea re-desplegado. Eso no es un absoluto, algunos cambios cambiarán interfaces de servicios resultando en cierta coordinación, pero el objetivo de una buena arquitectura de microservicios es reducir al mínimo estos a través de límites de servicio cohesivos y mecanismos de evolución en los contratos de servicio.

Otra consecuencia de la utilización de servicios como componentes es una interfaz de componente más explícita. La mayoría de los lenguajes no tienen un buen mecanismo para definir una Interfaz de Publicación explícita. Con frecuencia es sólo la documentación y la disciplina la que evita que los clientes rompan la encapsulación de un componente, dando lugar a acoplamiento excesivamente apretado entre los componentes. Los servicios hacen que sea más fácil de evitar esto mediante el uso de mecanismos explícitos de llamada remota.

Usando servicios como este tiene desventajas. Las llamadas remotas son más caras que las llamadas en proceso, y por lo tanto las APIs remotas necesitan ser de "grano más grueso", que es a menudo más raro de usar. Si se necesita cambiar la asignación de responsabilidades entre los componentes, tales movimientos de comportamiento son más difíciles de hacer cuando se está cruzando los límites del proceso.

En una primera aproximación, se puede observar que los servicios mapean a procesos en tiempo de ejecución, pero eso es sólo a primera aproximación. Un servicio puede consistir en múltiples procesos que siempre serán desarrollados y desplegados juntos, como un proceso de aplicación y una base de datos que sólo es usada por ese servicio.

#Organizado en torno a las capacidades comerciales

Al mirar para dividir una aplicación grande en partes, a menudo la gestión se centra en la capa de la tecnología, lo que lleva a los equipos de interfaz de usuario, a los equipos de la lógica del lado del servidor, y a los equipos de base de datos. Cuando los equipos están separados a lo largo de estas líneas, incluso cambios simples pueden dar lugar a un proyecto entre equipos tomando tiempo y aprobación de presupuesto. Un equipo inteligente optimizará alrededor de esto y decidirá por el menor de dos males - simplemente obligar a la lógica en cualquiera aplicación tengan acceso a. La lógica en todas partes, en otras palabras. Este es un ejemplo de la ley de Conway en acción.

>Cualquier organización que diseñe un sistema (definido en términos generales)
>producirá un diseño cuya estructura es una copia de la estructura de
>comunicación de la organización.

> - Melvyn Conway, 1967
 

El enfoque de microservicios a la división es diferente, separándose en servicios organizados en torno a la capacidad del negocio. Estos servicios tienen una implementación de amplia pila de software para esa área de negocios, incluyendo la interfaz de usuario, el almacenamiento persistente, y cualquier colaboración externa. En consecuencia, los equipos son multifuncionales, incluyendo toda la gama de habilidades necesarias para el desarrollo: la experiencia del usuario, la base de datos y gestión de proyectos.

 
Una compañía organizada de esta manera es www.comparethemarket.com. equipos multi-funcionales son responsables de la construcción y el funcionamiento de cada producto y cada producto se divide en un número de servicios individuales que se comunican a través de un bus de mensajes.

Las grandes aplicaciones monolíticas siempre pueden ser modularizados alrededor de las capacidades de negocio también, aunque ese no es el caso común. Ciertamente se insta a un gran equipo la creación de una aplicación monolítica para dividirse a lo largo de las líneas de negocio. El principal problema que se ha visto aquí, es que tienden a organizarse en torno a demasiados contextos. Si el monolito se extiende por muchos de estos límites modulares puede ser difícil para los miembros individuales de un equipo de encajar en su memoria a corto plazo. Además, se observa que las líneas modulares requieren una gran cantidad de disciplina para hacer cumplir. La separación necesariamente más explícita requerida por los componentes de servicio hace que sea más fácil mantener los límites del equipo clara.

#Productos, no proyectos

La mayoría de los esfuerzos de desarrollo de aplicaciones que se ven usan un modelo de proyecto: donde el objetivo es entregar alguna pieza de software que se considera entonces que esté terminada. Una vez finalizado el software es entregado a una organización de mantenimiento y el equipo del proyecto que lo construyó es disuelto.

proponentes de microservicios tienden a evitar este modelo, prefiriendo en cambio la noción de que un equipo debe poseer un producto durante toda su vida útil. Una inspiración común de esto es la noción de Amazon "tú construyes, tú lo ejecutas", donde un equipo de desarrollo asume la plena responsabilidad por el software en producción. Esto lleva a los desarrolladores en contacto día a día con la forma en que su software se comporta en la producción y aumenta el contacto con sus usuarios, ya que tienen que asumir al menos parte de la carga de apoyo.

La mentalidad del producto, enlaza con la vinculación con las capacidades de negocio. En lugar de ver al software como un conjunto de funcionalidades que están terminadas, hay una relación en curso, donde la cuestión es: ¿cómo puede el software ayudar a sus usuarios para mejorar la capacidad de negocio.

No hay ninguna razón por qué este mismo enfoque no puede ser tomado con aplicaciones monolíticas, pero la granularidad más pequeña de los servicios puede hacer que sea más fácil para crear las relaciones personales entre los desarrolladores de servicios y sus usuarios.

#Criterios de valoración inteligentes y tuberías tontas

Cuando se construyen estructuras de comunicación entre los diferentes procesos, se han visto muchos productos y enfoques el estresa, poniendo astucia significativa en el propio mecanismo de comunicación. Un buen ejemplo de esto es el Enterprise Service Bus (ESB), donde los productos ESB a menudo incluyen sofisticadas instalaciones para el enrutamiento de mensajes coreografía transformación, y aplicando las reglas de negocio.

La comunidad de microservicios favorece un enfoque alternativo: criterios de valoración inteligentes y tuberías tontas. Las aplicaciones construidas a partir de microservicios pretenden ser tan disociados y tan cohesivos como sea posible - que poseen su propia lógica de dominio y actúan más como filtros en el sentido clásico de Unix - recibiéndole una solicitud, aplicando lógica según el caso y produciendo una respuesta. Estas son coreografiadas utilizando protocolos REST simples en lugar de protocolos complejos tales como "WS-Choreography" o "BPEL" o la orquestación por una herramienta central.

>Sé de la web, no detrás de la web
>
>- Ian Robinson

Equipos de microservicios usan los principios y protocolos que la world wide web (y en gran medida, Unix) está construida. A menudo, los recursos utilizados pueden almacenados en caché con muy poco esfuerzo de parte de los desarrolladores o staff de operaciones.

El segundo acercamiento de uso común es la mensajería por un bus de mensaje corto. La infraestructura elegida es normalmente tonta (tonta como que actúa como solo como un router del mensaje) - implementaciones sencillas como RabbitMQ o ZeroMQ no hacen mucho más que proporcionar una tela asincrónica confiable - la inteligencia sigue viviendo en los puntos extremos que están produciendo y consumiendo mensajes; en los servicios.

#Gobernanza descentralizada

Una de las consecuencias de gobernanza centralizada es la tendencia a estandarizar las plataformas tecnológicas simples. La experiencia demuestra que este enfoque se constriñe - no todo problema es un clavo y no toda solución un martillo. Es preferible utilizar la herramienta adecuada para el trabajo y mientras que las aplicaciones monolíticas pueden tomar ventaja de diferentes lenguajes, en cierta medida, que no es tan común.

Dividiendo los componentes del monolito en servicios tenemos una opción al crear cada uno de ellos. ¿Se desea utilizar Node.js para levantar una página sencilla de informes? Fácil. ¿C++ para un componente en tiempo casi real, particularmente retorcido?  está bien Se desea intercambiar en un sabor diferente de base de datos que se adapte mejor a la lectura de comportamiento de un componente? Tenemos la tecnología para reconstruirlo.

Por supuesto, sólo porque se puede hacer algo, no significa que debería- pero particionar el sistema de esta manera significa que hay la opción.

Netflix es un buen ejemplo de una organización que sigue esta filosofía. Compartiendo útil y, sobre todo, "código de batalla probado" como bibliotecas anima a otros desarrolladores a resolver problemas similares de manera similar, sin embargo, deja la puerta abierta a la selección de un enfoque diferente si es necesario. Las bibliotecas compartidas tienden a enfocarse en los problemas comunes de almacenamiento de datos, la comunicación entre procesos, y como se verá más adelante, la automatización de la infraestructura.

Para la comunidad de microservicios, los gastos generales son particularmente poco atractivos. Esto no quiere decir que la comunidad no valora los contratos de servicios. Más bien al contrario, ya que tienden a ser muchos más de ellos. Es sólo que ellos están buscando diferentes formas de gestionar dichos contratos. Patrones como "lector tolerante" y "Contratos dirigidos por el consumidor" se aplican a menudo a los microservicios. Estos ayudan a los contratos de servicios en evolucionar de forma independiente. Ejecutando los contratos dirigidos por el consumidor como parte de su construcción incrementa la confianza y proporciona retroalimentación rápida sobre si sus servicios están funcionando. De hecho, se sabe de un equipo en Australia, que dirige la creación de nuevos servicios con contratos dirigidos por el consumidor. Ellos usan herramientas simples que les permiten definir el contrato por un servicio. Esto se convierte en parte de la construcción automatizada antes de código para el nuevo servicio que está aún escrito. El servicio, es luego creado a cabo sólo hasta el punto en que cumple el contrato - un enfoque elegante para evitar el dilema 'YAGNI' al crear nuevo software. Estas técnicas y las herramientas que crecen alrededor de ellos, limitan la necesidad de gestión de contrato central, al disminuir el acoplamiento temporal entre los servicios.

#Gestión de datos descentralizada

La descentralización de la gestión de datos se presenta en un número de maneras diferentes. En el nivel más abstracto, significa que el modelo conceptual del mundo será diferente entre los sistemas.

Este problema es común entre las aplicaciones, pero también puede ocurrir dentro de las aplicaciones, en particular cuando la aplicación se divide en componentes separados. Una forma útil de pensar acerca de esto es la noción de Diseño de dominio dirigido de contexto acotado. El DDD divide un dominio complejo en múltiples contextos delimitados, y se desarrollan las relaciones entre ellos.

Así como la descentralización de las decisiones sobre los modelos conceptuales, los microservicios también descentralizan las decisiones de almacenamiento de datos. Mientras que las aplicaciones monolíticas prefieren una base de datos lógica única de datos persistentes, las empresas a menudo prefieren una sola base de datos través de un rango de aplicaciones - muchas de estas decisiones conducidas a través de modelos comerciales de los proveedores en torno a la concesión de licencias. Los microservicios prefieren dejar que cada servicio maneje su propia base de datos, o bien diferentes instancias de la misma tecnología de base de datos, o sistemas de bases totalmente diferentes - un enfoque llamado Persistencia Políglota. Se puede utilizar la persistencia políglota en un monolito, pero aparece con mayor frecuencia con microservicios.

 

#Automatización de infraestructura

las técnicas de automatización de infraestructura han evolucionado enormemente en los últimos años - la evolución de la nube y AWS en particular, ha reducido la complejidad de las operaciones de construcción, despliegue y microservicios operativos.

Muchos de los productos o sistemas que están siendo construidos con microservicios están siendo construidos por equipos con amplia experiencia en la entrega continua y su precursora, la integración continua. Los equipos de construcción de software de esta manera hacen un amplio uso de las técnicas de automatización de la infraestructura. Esto se ilustra en la tubería de construcción se muestra a continuación.

 
Una aplicación monolítica será construida, probada y lanzada través de estos entornos felizmente. Resulta que una vez que se ha invertido en la automatización de la ruta de acceso a la producción de un monolito, entonces desplegar más aplicaciones no parece tan temible. Recordemos, uno de los objetivos de la CD es hacer aburrido el despliegue, por lo que, si es una o tres aplicaciones, siempre y cuando sea aburrido no importa.

 

#Diseño para el fracaso

Una consecuencia de usar servicios como componentes, es que las aplicaciones necesitan ser diseñadas para que puedan tolerar el fracaso de los servicios. Cualquier llamada de servicio podría fallar debido a la falta de disponibilidad del proveedor, el cliente tiene que responder a esto con tanta gracia como sea posible. Esto es una desventaja comparada con un diseño monolítico, ya que introduce una complejidad adicional para manejar. La consecuencia es que los equipos de microservicios reflejan constantemente en cómo fallas en el servicio afectan a la experiencia del usuario. El ejército de simios de Netflix induce fallas de servicios e incluso centros de datos durante la jornada de trabajo para poner a prueba tanto la capacidad de recuperación como el monitoreo de la aplicación.

Dado que los servicios pueden fallar en cualquier momento, es importante ser capaz de detectar los fallos rápidamente y, si es posible, restaurar automáticamente el servicio. aplicaciones de microservicios ponen mucho énfasis en el monitoreo en tiempo real de la aplicación, comprobando ambos elementos arquitectónicos (el número de solicitudes por segundo que está teniendo la base de datos) las métricas relevantes del negocio (como el número de pedidos por minuto que son recibidos). el monitoreo semántico puede proporcionar un sistema de alerta temprana de que algo va mal que activa a los equipos de desarrollo a dar seguimiento e investigar.

Los monolitos pueden ser construidos para ser tan transparente como un microservicio - de hecho, lo deberían ser. La diferencia es que es absolutamente necesario saber cuándo los servicios corriendo en diferentes procesos están desconectados. Con las bibliotecas dentro del mismo proceso es menos probable que sea útil este tipo de transparencia.

los equipos de microservicios esperarían ver sofisticadas configuraciones de supervisión y registro para cada servicio individual, como cuadros de mando que muestran el estado "arriba / abajo" y una variedad de métricas relevantes al negocio. Los detalles sobre el estado del interruptor de circuito, el rendimiento actual y la latencia son otros ejemplos a menudo nos encontramos en la naturaleza.

#Diseño evolutivo

Los practicantes de microservicios, por lo general han venido de un fondo de diseño evolutivo y ven la descomposición de servicio como una herramienta más para permitir que los desarrolladores de aplicaciones puedan controlar los cambios en su aplicación sin ralentizar el cambio. El control de cambios no significa necesariamente que la reducción del cambio - con las actitudes y las herramientas adecuadas se pueden realizar cambios frecuentes y rápido, y bien controlados en el software.

El sitio web de The Guardian es un buen ejemplo de una aplicación que fue diseñada y construida como un monolito, pero ha ido evolucionando en una dirección microservicio. El monolito sigue siendo el núcleo de la página web, pero prefieren para añadir nuevas funciones construir microservicios que utilizan la API del monolito. Este enfoque es particularmente útil para aquellas que son inherentemente temporales, como las páginas especializadas para el manejo de un evento deportivo. Dicha parte del sitio web puede ser puesta rápidamente utilizando lenguajes de desarrollo rápido, y es retirada una vez que el evento ha terminado. Se han visto enfoques similares en una institución financiera donde se añaden nuevos servicios para una oportunidad de mercado y se descarta después de unos pocos meses o incluso semanas.

#¿Los microservicios son el futuro?

Nuestro principal objetivo al escribir este artículo es explicar las principales ideas y principios de microservicios. Al tomar el tiempo para hacer esto pensamos claramente que el estilo arquitectónico de los microservicios es una idea importante - que vale la pena considerar seriamente para las aplicaciones empresariales. Hemos construido recientemente varios sistemas que utilizan el estilo y conocimientos de otros que han utilizado y favorecido este enfoque.

Aquellos que sabemos quiénes son de alguna manera pioneros en el estilo arquitectónico incluyen Amazon, Netflix, The Guardian, el Servicio Digital del Gobierno del Reino Unido, realestate.com.au, Forward y comparethemarket.com. El circuito de conferencias en 2013 estaba llena de ejemplos de empresas que se están moviendo a algo que clasificaría como microservicios - incluyendo Travis CI. Además, hay un montón de organizaciones que durante mucho tiempo han estado haciendo lo que clasificaría como microservicios, pero sin usar el nombre. (A menudo esto se etiqueta como SOA - aunque, como hemos dicho, SOA viene en muchas formas contradictorias.)

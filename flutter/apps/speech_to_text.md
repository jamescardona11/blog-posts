# Explore Speech to text  - Flutter
Aprende como integrar speech to text en tus aplicaciones.

## Introducción:

Con los avances en AI creo que se hace muy útil poder hablar por medio de comandos de voz con una AI como chatGPT. Para eso podemos algunas alternativas y hoy exploraremos como hacer esto de forma fácil con Speech to text.
Este post explorará la forma correcta de integrarlos y separar separar la lógica de tu app de la UI. Usando la librería [speech_to_text]()

### Tabla de contenido:

- a
- b
- c


### ¿Por qué existen las Sealed Classes?

La existencia se basa en establecer restricciones en la jerarquia de clases, limitada y cerrada de casos posibles para ciertos tipos de datos. Por lo general esta situación podriamos pensar en resolverla con Enums, pero veremos más adelante sus ventajas y limitaciones.

#### Ventajas y Desventajas

1. Control y estabilidad: Al restringir la herencia a un conjunto específico de subclases, las Sealed proporcionan un mayor control sobre la jerarquia de clases y evitan la adición de subclases no deseadas. (Expandiremos la idea más adelante)

2. Limita los comportamientos inesperados: Al limitar las opciones, también limitamos el ¿qué y el cómo?, de estas clases, ofreciendo más control sobre el contexto y las intenciones de estas clases, reduce la posibilidad de errores o comportamientos no deseados.

3. Facilita la exhaustividad en estructuras de control: Al usar `when` o `map` se puede garantizar que cubramos todos los escenarios y variaciones de las subclases. Esto nos ayuda a evitar errores en tiempo de ejecución.

4. Rigidez: Su diseño y ventaja es limitar, controlar o restringir, pero también puede ser una desventaja al limitar la flexibilidad del diseño y dificultar la extensibilidad futura. Si se requiere agregar una nueva subclase en el futuro, será necesario modificar la clase sellada y todas las partes del código que la utilizan, lo que puede resultar en un esfuerzo considerable.

5. Acoplamiento: El uso de Sealed Classes puede aumentar el acoplamiento entre las clases, ya que la clase sellada y sus subclases están estrechamente relacionadas y dependen entre sí. Esto puede hacer que el código sea más difícil de mantener.

### El problema y la solución

Hasta antes de Dart 3.0 no existia nativamente algo para representar las Sealed Classes para ello vamos a tratar de simular el problema y resolverlo sin Dart 3.0 para que al final lleguemos a la forma que podemos hacerlo desde ahora.

Supongamos que vamos a crear una jerarquia de clases para representar los organos del ser humano.

Organs: Brain, Heart, Lungs, Liver


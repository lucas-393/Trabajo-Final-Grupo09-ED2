# TRABAJO PRÁCTICO FINAL – ELECTRÓNICA DIGITAL II

## Sistema Electrónico de Juego "Batalla Naval" con Comunicación Inter-Microcontrolador

Este proyecto comprende el diseño, desarrollo e implementación de un sistema embebido de lógica distribuida basado en dos microcontroladores independientes. El sistema emula el juego clásico de Batalla Naval, permitiendo el desarrollo de una partida en tiempo real mediante dos consolas de juego interconectadas.

---

### Especificaciones Generales

* **Asignatura:** Electrónica Digital II – FCEFyN, UNC
* **Integrantes:** Grupo 09
* **Plataforma de Hardware:** Microcontrolador PIC16F887 (Microchip)
* **Lenguaje de Programación:** Ensamblador (ASM)
* **Entorno de Desarrollo:** MPLAB X IDE

---
## 1. Descripción del Proyecto

El sistema consta de dos unidades de control o consolas independientes. Cada terminal administra una matriz de juego de 8 × 8 posiciones, procesa la entrada analógica de periféricos de control (potenciómetros para la selección de coordenadas) y gestiona los comandos de entrada del usuario mediante pulsadores. 

La sincronización de datos y la transferencia de eventos entre ambas consolas se realiza mediante el módulo de comunicación serial asíncrona (EUSART) nativo del microcontrolador, estableciendo un esquema de transmisión de ataques y recepción de estados de juego.

Para cumplir con los requerimientos funcionales, el diseño integra los siguientes módulos del periférico:
* **Conversión Analógico-Digital (ADC):** Para la digitalización de las coordenadas de juego.
* **Controladores de Matriz LED (MAX7219):** Para la visualización dinámica del tablero de juego.
* **Interrupciones Externas e Internas:** Para la captura inmediata de las acciones del usuario y el control de tiempos (vía Timer0/Timer1) en animaciones y generación de señales acústicas.

El firmware está desarrollado en su totalidad en lenguaje Ensamblador (ASM) optimizado para la arquitectura del PIC16F887.

---
## 2. Índice

- [1. Descripción del Proyecto](#1-descripción-del-proyecto)
- [3. Alcance del Proyecto (Historias de Usuario)](#3-alcance-del-proyecto-historias-de-usuario)
- [4. Estructura del Repositorio](#4-estructura-del-repositorio)
- [5. Arquitectura del Sistema: Hardware y Software](#5-arquitectura-del-sistema-hardware-y-software)
  - [5.1. Diagrama de Bloques](#51-diagrama-de-bloques)
  - [5.2. Esquemático del Circuito](#52-esquemático-del-circuito)
  - [5.3. Diagrama de Flujo](#53-diagrama-de-flujo)
- [6. Definición de Terminado (DoD)](#6-definición-de-terminado-dod)
- [7. Instrucciones de Simulación](#7-instrucciones-de-simulación)
## 3. Alcance del Proyecto (Historias de Usuario)

El sistema está diseñado para permitir el desarrollo de una partida de Batalla Naval entre dos jugadores, mediante dos consolas electrónicas independientes basadas en microcontroladores PIC16F887. El funcionamiento se organiza en dos fases principales: posicionamiento de las embarcaciones y desarrollo de los turnos de ataque y defensa.

**1. Posicionamiento y configuración de la flota:**

- Cada jugador dispone de una matriz de juego de 8 × 8 posiciones.
- La selección de coordenadas se realiza mediante dos potenciómetros lineales conectados a los canales AN0 y AN1 del microcontrolador.
- El jugador dispone de tres embarcaciones de longitudes 2, 3 y 4 casillas.
- Cada embarcación se identifica mediante un ID numérico único, almacenado en una matriz de RAM de 8 × 8.
- El sistema verifica la contigüidad de las posiciones correspondientes a cada embarcación y evita la superposición de barcos.
- Se mantiene un arreglo de puntos de salud asociado a cada embarcación, según su longitud.

**2. Desarrollo de los turnos de ataque y defensa:**

- Una vez finalizado el posicionamiento, ambas consolas sincronizan su estado mediante comunicación serial EUSART.
- Durante el modo de ataque, el jugador selecciona una coordenada mediante los potenciómetros y confirma el disparo utilizando el pulsador conectado a RB0/INT.
- La consola transmite la coordenada del ataque a la unidad contraria y pasa al modo de defensa.
- Durante el modo de defensa, la matriz LED cambia su visualización para mostrar la ubicación de los barcos propios y la consola queda a la espera de un ataque entrante.

**3. Procesamiento y resolución de impactos:**

- Al recibir una coordenada, el microcontrolador consulta la matriz de posiciones almacenada en RAM para determinar si el ataque impactó sobre una embarcación o sobre una casilla de agua.
- Cuando se produce un impacto sobre un barco, se decrementa su cantidad de puntos de salud.
- Si el barco conserva puntos de salud, el sistema informa el resultado como **TOCADO**.
- Cuando los puntos de salud llegan a cero, el sistema informa el resultado como **HUNDIDO**.
- Si la posición atacada no contiene una embarcación, el resultado se informa como **AGUA**.

**4. Visualización y señalización de eventos:**

- La representación del tablero se realiza mediante una matriz LED de 8 × 8 controlada por el integrado MAX7219.
- El Timer0 o Timer1 genera interrupciones periódicas para controlar animaciones, como el parpadeo del cursor de selección y la señalización de impactos.
- Los indicadores luminosos permiten identificar los diferentes eventos: LED verde para el turno del jugador, azul para agua, amarillo para tocado y rojo para hundido.
- Un buzzer activo de 5 V, accionado mediante un transistor NPN, genera diferentes patrones sonoros según el resultado del ataque.

**5. Comunicación entre consolas:**

- Las dos unidades intercambian información mediante comunicación serial bidireccional EUSART.
- Se utiliza un protocolo de tramas de un byte para transmitir coordenadas de ataque, resultados y señales de sincronización.
- Los mensajes permiten comunicar los estados AGUA, TOCADO y HUNDIDO, además de las señales de consola lista y cambio de turno.
---
## 4. Estructura del Repositorio

La organización de los directorios y componentes de software del proyecto se estructura de la siguiente manera:

* `README.md`: Documentación técnica general, especificaciones funcionales, alcance y descripción de la arquitectura del sistema.
* `src/`: Directorio raíz que aloja el código fuente desarrollado en lenguaje Ensamblador (ASM) para las unidades de control.
  * `main.asm`: Módulo principal del firmware encargado de la inicialización global y la coordinación de los estados del juego.
  * `configuracion.asm`: Archivo de asignación de directivas, palabras de configuración del microcontrolador y parametrización de registros iniciales.
  * `modulos/`: Subdirectorio de librerías y subrutinas específicas (conversión ADC, control de periféricos MAX7219, rutinas de servicio de interrupción y protocolos de comunicación EUSART).
* `sim/`: Contiene los archivos de simulación del circuito embebido en entornos virtuales (Proteus), estructurados por módulos de consola y escenarios de integración.
* `docs/`: Repositorio de documentación técnica complementaria, diagramas de bloques, diagramas de flujo de algoritmos, diagramas esquemáticos y tablas de conexiones del hardware.
* `pruebas/`: Aloja los programas de testeo unitario para la validación individual de periféricos y los scripts de verificación de integración del sistema de juego.

TRABAJO PRÁCTICO FINAL – ELECTRÓNICA DIGITAL II

Batalla Naval: Sistema de Juego Electrónico con Comunicación entre Microcontroladores

Asignatura: Electrónica Digital II – FCEFyN, UNC
Integrantes: Grupo 09
Microcontrolador: PIC16F887
Lenguaje: Ensamblador (ASM)
Entorno de desarrollo: MPLAB X IDE

Descripción del proyecto

El presente proyecto consiste en el diseño e implementación de un sistema electrónico basado en dos microcontroladores PIC16F887, que permite desarrollar una partida del clásico juego Batalla Naval mediante dos consolas independientes.

Cada consola administra un tablero de juego de 8 × 8 posiciones, permite realizar la selección de coordenadas mediante potenciómetros y gestionar las acciones del jugador a través de pulsadores. La comunicación entre ambas unidades se realiza mediante el módulo de comunicación serial asíncrona (EUSART), permitiendo transmitir los ataques y recibir las respuestas correspondientes.

El sistema incorpora conversión analógica-digital (ADC) para la lectura de las coordenadas, matrices LED controladas mediante el integrado MAX7219 para la visualización del tablero, interrupciones externas para la detección de acciones del usuario y temporizadores para la gestión de eventos del juego.

El firmware será desarrollado en lenguaje Ensamblador (ASM), utilizando el microcontrolador PIC16F887.
## Índice

- [Descripción del proyecto](#descripción-del-proyecto)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Alcance del proyecto (Historias de Usuario)](#alcance-del-proyecto-historias-de-usuario)
- [Arquitectura del sistema: hardware y software](#arquitectura-del-sistema-hardware-y-software)
  - [Diagrama de bloques](#diagrama-de-bloques)
  - [Esquemático del circuito](#esquemático-del-circuito)
  - [Diagrama de flujo](#diagrama-de-flujo)
- [Definición de Terminado (DoD)](#definición-de-terminado-dod)
- [Instrucciones de Simulación](#instrucciones-de-simulación)
## Alcance del proyecto (Historias de Usuario)

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

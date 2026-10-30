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

El sistema está diseñado para permitir el desarrollo de una partida de Batalla Naval entre dos jugadores, utilizando dos consolas electrónicas independientes que intercambian información mediante comunicación serial.

Las funcionalidades principales se organizan de la siguiente manera:

**1. Configuración y preparación del tablero:**

- Cada jugador dispone de un tablero de 8 × 8 posiciones.
- El sistema permite seleccionar las coordenadas de ubicación de los barcos mediante dos potenciómetros, correspondientes a los ejes X e Y.
- Cada jugador debe ubicar una flota compuesta por tres barcos de diferentes longitudes: 2, 3 y 4 posiciones.
- El sistema debe verificar que las posiciones seleccionadas sean válidas, evitando superposiciones y ubicaciones fuera de los límites del tablero.

**2. Selección y ejecución de ataques:**

- Durante la partida, el jugador puede seleccionar una coordenada del tablero contrario mediante los potenciómetros.
- Un pulsador permite confirmar el ataque sobre la posición seleccionada.
- La coordenada elegida se transmite a la otra consola mediante comunicación serial UART.
- El sistema debe impedir que se realicen ataques repetidos sobre una misma posición.

**3. Recepción y procesamiento de ataques:**

- Cada consola recibe las coordenadas de los ataques enviados por el oponente.
- El microcontrolador verifica la posición recibida consultando la información almacenada en la memoria RAM.
- Según el resultado, el sistema determina si el ataque corresponde a agua o a un barco.
- Cuando un barco recibe un impacto, se actualiza su estado. Si todas sus posiciones fueron alcanzadas, se informa que ha sido hundido.
- La consola transmite al oponente el resultado del ataque.

**4. Visualización y señalización:**

- Una matriz LED de 8 × 8 controlada mediante el integrado MAX7219 permite visualizar el tablero.
- El sistema utiliza indicadores luminosos para diferenciar los eventos de la partida, como el turno del jugador, los impactos y los barcos hundidos.
- Un buzzer proporciona señales acústicas asociadas a determinados eventos del juego.

**5. Control de turnos y finalización:**

- Los jugadores alternan sus turnos mediante la comunicación entre ambas consolas.
- El sistema debe evitar que los dos jugadores ejecuten ataques simultáneamente.
- La partida finaliza cuando uno de los jugadores consigue hundir la totalidad de los barcos del oponente.

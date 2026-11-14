Hack-Crossy

Juego tipo Crossy Road escrito en Jack para la plataforma Hack (Nand2Tetris). Organización de Computadores, Universidad EAFIT, 2026.

Equipo: Jose David Acevedo, Juan José Roldán, Simon Arias
Video de demostración: [PEGAR AQUÍ EL ENLACE]

Concepto

El jugador guía a un pollo que debe cruzar un mundo infinito de carreteras y praderas. Cada carril nuevo alcanzado suma un punto. Los carros eliminan al jugador y los árboles bloquean el paso. Cada 10 puntos sube el nivel y los carriles nuevos traen tráfico más rápido.

Diferenciador

Los clones de Frogger en Jack suelen usar una sola pantalla fija. Hack-Crossy se diferencia en:

Mundo infinito con cámara que sigue al jugador. Los carriles se generan con un LFSR de 16 bits y se liberan de memoria al salir de pantalla.
Salto con física: el pollo describe una parábola con gravedad constante.
Motor sin multiplicaciones ni divisiones: el código compilado no llama a Math.multiply ni a Math.divide.
Redibujado incremental: solo se repintan las celdas que cambian.
Controles
Flechas o W A S D: saltar arriba, izquierda, abajo o derecha.
ENTER: comenzar o jugar de nuevo.
Requisitos técnicos
Interactividad por teclado: Keyboard.keyPressed() en cada cuadro, sin lecturas bloqueantes. Un salto por pulsación.
Entorno dinámico: vehículos en movimiento continuo y cámara que avanza generando mundo nuevo.
Comportamiento físico: salto parabólico con z = z + vz y vz = vz - G (v0 = 6, G = 4). Los vehículos se mueven con velocidad constante en punto fijo.
Puntaje: marcador con PUNTOS, RECORD y NIVEL.
Estructura
README.md
.gitignore
HackCrossy/: carpeta que se carga en el emulador
Main.jack: punto de entrada
Game.jack: bucle principal, reglas, puntaje, colisiones y cámara
Lane.jack: carriles y vehículos
Player.jack: jugador y física del salto
Gfx.jack: acceso directo a la memoria de video
Sprites.jack: imágenes de 16x16
Rng.jack: generador pseudoaleatorio
Archivos .vm: código compilado, listo para el VM Emulator
Cómo ejecutarlo
Descargar el Nand2Tetris Software Suite desde nand2tetris.org.
Opcional: compilar desde la carpeta tools con JackCompiler.bat HackCrossy (Windows) o ./JackCompiler.sh HackCrossy (Mac/Linux). La carpeta ya incluye los .vm compilados.
No copiar los .vm del sistema operativo (Screen, Output, Math, etc.) en la carpeta.
Abrir el VMEmulator, ir a File > Load Program y seleccionar la carpeta HackCrossy.
Poner Animate en No animation, la velocidad al máximo y View en Screen.
Presionar Run y hacer clic en el ícono del teclado debajo de la pantalla.
Presionar ENTER en la pantalla de título.

Si el juego va muy lento o muy rápido, cambiar el valor de frameMs en Game.jack (función initStatics). Un valor menor lo hace más rápido.

Arquitectura
Main: inicializa los módulos y repite partidas.
Game: bucle principal, reglas, puntaje, colisiones y desplazamiento de cámara.
Lane: carril de césped o carretera, guardado como un arreglo circular de 32 celdas.
Player: inicio del salto, física y dibujo con XOR.
Gfx: escritura directa en la memoria de video usando tablas de direcciones precalculadas.
Sprites: imágenes de 16x16 de todos los elementos.
Rng: generador pseudoaleatorio LFSR de 16 bits.

Cada cuadro del juego:

Lee el teclado sin bloquear.
Si el jugador está en el suelo y hay una tecla pendiente, inicia el salto.
Borra al jugador con XOR.
Avanza los carriles y repinta solo las celdas que cambiaron.
Calcula la física del salto y, al aterrizar, suma puntaje y mueve la cámara.
Detecta colisiones.
Dibuja al jugador y, si cambió, el marcador.
Espera frameMs milisegundos.
Decisiones de bajo nivel
Cada celda mide 16x16 píxeles, es decir, exactamente una palabra de ancho. Por eso la columna es directamente el índice de palabra y nunca se divide x entre 16.
La dirección de cada fila de la pantalla (16384 + 32 * y) se precalcula una vez sumando 32 en un bucle.
La ALU de Hack no tiene XOR, así que se construye como (a | b) & ~(a & b). Dibujar al jugador dos veces en el mismo lugar lo borra sin tocar el fondo.
(c - offset) & 31 reemplaza al módulo 32 y funciona con negativos gracias al complemento a 2.
La colisión se detecta leyendo el tipo de la celda donde está el jugador, sin calcular distancias.
El generador aleatorio desplaza bits sumando el número consigo mismo (x + x) y usa máscaras AND para los rangos.
El nivel se calcula con un contador que se reinicia cada 10 puntos, en vez de dividir.
Los textos fijos se crean una sola vez y los carriles que salen de pantalla se liberan, así que no hay fugas de memoria.
Pruebas

El código se ejecutó en un emulador de pruebas con teclado automatizado:

1500 cuadros de juego normal con varias muertes y reinicios, sin errores.
3000 cuadros avanzando más de 300 carriles, sin errores.
Uso de memoria constante durante toda la ejecución.
Ninguna llamada a Math.multiply ni a Math.divide.
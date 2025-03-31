# **Actividad 1: Lenguaje Ensamblador**  

#### **1. Entrada/Salida Mapeada a Memoria**  
- **¿Qué es?**  
La entrada-salida mapeada a memoria es una técnica donde los dispositivos periféricos como teclados y pantallas se controlan mediante direcciones de memoria específicas en lugar de instrucciones especializadas. En la plataforma Hack, esto se implementa asignando direcciones fijas: el teclado se lee en la dirección 24576 (0x6000), devolviendo el código ASCII de la tecla presionada, mientras la pantalla se controla escribiendo en la dirección 16384 (0x4000), donde cada bit representa un píxel (1 para blanco, 0 para negro). Por ejemplo, un programa sencillo puede leer continuamente el teclado y mostrar el código ASCII de la tecla en la primera posición de pantalla, demostrando cómo la CPU interactúa con dispositivos mediante accesos a memoria convencionales, simplificando el diseño del hardware y el software. El simulador Hack permite probar este concepto mostrando en tiempo real cómo los valores escritos en esas direcciones afectan los periféricos.

#### **2. Programa de Ejemplo**  
```assembly
// Muestra la tecla presionada en pantalla
(LOOP)
    @KBD        // Accede al teclado (dirección 24576)
    D=M         // Lee el código ASCII
    @SCREEN     // Accede a la pantalla (dirección 16384)
    M=D         // Escribe el código en el primer píxel
    @LOOP
    0;JMP       // Bucle infinito
```


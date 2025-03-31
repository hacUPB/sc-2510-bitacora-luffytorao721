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
# **Actividad 2 - Implementación con el Reto 18 Específico**

#### **Código Completo Optimizado**
```assembly
// Programa principal
(MAIN_LOOP)
    @KBD
    D=M
    @100       // 'd' = 100
    D=D-A
    @DRAW_SPECIFIC_BITMAP
    D;JEQ
    @MAIN_LOOP
    0;JMP

// Implementación EXACTA de tu bitmap del Reto 18
(DRAW_SPECIFIC_BITMAP)
    @SCREEN
    D=A
    @R12
    M=D        // R12 = SCREEN (dirección base)

    // ---- Fila 5 ----
    @R12
    A=M
    M=-1       // Primera palabra = -1 (1111111111111111)
    A=A+1      // Siguiente palabra
    D=A
    @127
    D=D+A
    A=D-A
    M=D-A      // M[addr] = 127

    // ---- Fila 6 ----
    @31
    D=A
    @R12
    M=M+D      // Avanzar 31 palabras
    @R12
    A=M
    D=A
    @17
    D=D+A
    A=D-A
    M=D-A      // M[addr] = 17
    A=A+1
    D=A
    @64
    D=D+A
    A=D-A
    M=D-A      // M[addr+1] = 64

    // ---- Fila 7 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    D=A
    @17
    D=D+A
    A=D-A
    M=D-A      // M[addr] = 17
    A=A+1
    D=A
    @64
    D=D+A
    A=D-A
    M=D-A      // M[addr+1] = 64

    // ---- Fila 8 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    D=A
    @31
    D=D+A
    A=D-A
    M=D-A      // M[addr] = 31
    A=A+1
    D=A
    @96
    D=D+A
    A=D-A
    M=D-A      // M[addr+1] = 96

    // ---- Fila 9 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    M=1        // M[addr] = 1
    A=A+1
    D=A
    @96
    D=D+A
    A=D-A
    M=D-A      // M[addr+1] = 96

    // ---- Fila 10 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    D=A
    @1985
    D=D+A
    A=D-A
    M=D-A      // M[addr] = 1985
    A=A+1
    D=A
    @80
    D=D+A
    A=D-A
    M=D-A      // M[addr+1] = 80

    // ---- Fila 11 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    D=A
    @15969
    D=D+A
    A=D-A
    M=D-A      // M[addr] = 15969
    A=A+1
    D=A
    @80
    D=D+A
    A=D-A
    M=D-A      // M[addr+1] = 80

    // ---- Fila 12 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    D=A
    @4063
    D=D+A
    A=D-A
    M=A-D      // M[addr] = -4063
    A=A+1
    D=A
    @72
    D=D+A
    A=D-A
    M=D-A      // M[addr+1] = 72

    // ---- Fila 13 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    D=A
    @32671
    D=D+A
    A=D-A
    M=A-D      // M[addr] = -32671
    A=A+1
    D=A
    @79
    D=D+A
    A=D-A
    M=D-A      // M[addr+1] = 79

    // ---- Fila 14 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    M=-1       // M[addr] = -1
    A=A+1
    D=A
    @127
    D=D+A
    A=D-A
    M=D-A      // M[addr+1] = 127

    @MAIN_LOOP
    0;JMP      // Volver al control principal
```

### **Características Clave**

1. **Fidelidad al Reto 18**:
   - Implementación **bit a bit idéntica** código original
   - Mismos valores decimales y patrones de escritura
   - Conserva el método único de cálculo de direcciones

2. **Estructura Optimizada**:
   - Uso de `R12` como puntero base constante
   - Cálculo de offsets mediante suma directa (31 palabras entre filas)

3. **Control de Flujo**:
   - Detección simple de tecla 'd'
   - Loop infinito para mantener el dibujo en pantalla

4. **Mapeo de Memoria**:
   - `SCREEN` (16384) como dirección base
   - Accesos consecutivos para palabras adyacentes

---

### **Actividad 3 - Implementación en Hack Assembly**

#### **Código Completo para Mostrar/Borrar Bitmap**
```assembly
// Programa principal
(MAIN_LOOP)
    @KBD        // Leer teclado (24576)
    D=M
    @100       // ASCII 'd' = 100
    D=D-A
    @DRAW_BITMAP
    D;JEQ      // Si es 'd', dibujar
    
    // Limpiar pantalla si no hay tecla presionada
    @SCREEN
    D=A
    @R12
    M=D        // R12 = dirección base (16384)
    
    @8192      // Tamaño total de pantalla (32KB)
    D=A
    @counter
    M=D
    
(CLEAR_LOOP)
    @R12
    A=M
    M=0        // Escribir 0 (píxel negro)
    
    @R12
    M=M+1      // Siguiente palabra
    
    @counter
    MD=M-1     // Decrementar contador
    @CLEAR_LOOP
    D;JGT      // Repetir hasta limpiar
    
    @MAIN_LOOP
    0;JMP      // Volver a verificar

// Dibujar bitmap del Reto 18
(DRAW_BITMAP)
    @SCREEN
    D=A
    @R12
    M=D        // R12 = dirección base

    // ---- Fila 5 ----
    @R12
    A=M
    M=-1       // 1111111111111111
    A=A+1
    D=A
    @127
    D=D+A
    A=D-A
    M=D-A      // Escribir 127

    // ---- Fila 6 ----
    @31
    D=A
    @R12
    M=M+D      // Avanzar 31 palabras
    @R12
    A=M
    D=A
    @17
    D=D+A
    A=D-A
    M=D-A      // Escribir 17
    A=A+1
    D=A
    @64
    D=D+A
    A=D-A
    M=D-A      // Escribir 64

    // [...] (basicamente aquí va la implementación del 7 al 14)
    // Siguiendo el mismo patrón que las filas 5 y 6

    @MAIN_LOOP
    0;JMP      // Volver al control principal
```

### **Explicación Técnica**

1. **Detección de Tecla**:
   - Verifica si se presionó 'd' comparando `@KBD` con 100 (ASCII 'd')
   - Si coincide, salta a `DRAW_BITMAP`; si no, limpia pantalla

2. **Limpieza de Pantalla**:
   - Escribe `0` en todas las 8192 palabras de memoria de pantalla
   - Usa un contador y puntero (`R12`) para optimizar

3. **Dibujo del Bitmap**:
   - Implementa **exactamente** tu patrón del Reto 18:
     - Fila 5: `-1` (todo blanco) + `127` en palabras consecutivas
     - Fila 6: `17` + `64` con offset de 31 palabras
       
  ![image](https://github.com/user-attachments/assets/2d9349ce-3b8b-4d75-8d93-74e801ea207f)

  ---

# Actividad 4: controlar bitmap

```assembly
// Programa principal
(MAIN_LOOP)
    @KBD
    D=M
    @100       // 'd' = 100
    D=D-A
    @DRAW_SPECIFIC_BITMAP
    D;JEQ
    
    @KBD
    D=M
    @101       // 'e' = 101
    D=D-A
    @CLEAR_SCREEN
    D;JEQ
    
    @MAIN_LOOP
    0;JMP

// Implementación del bitmap del Reto 18
(DRAW_SPECIFIC_BITMAP)
    @SCREEN
    D=A
    @R12
    M=D        // R12 = SCREEN (dirección base)

    // ---- Fila 1 ----
    @R12
    A=M
    M=-1       // Primera palabra = -1 (1111111111111111)
    A=A+1      // Siguiente palabra
    M=127      // Valor 127

    // ---- Fila 2 ----
    @31
    D=A
    @R12
    M=M+D      // Avanzar 31 palabras
    @R12
    A=M
    M=17       // Valor 17
    A=A+1
    M=64       // Valor 64

    // ---- Fila 3 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    M=17       // Valor 17
    A=A+1
    M=64       // Valor 64

    // ---- Fila 4 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    M=31       // Valor 31
    A=A+1
    M=96       // Valor 96

    // ---- Fila 5 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    M=1        // Valor 1
    A=A+1
    M=96       // Valor 96

    // ---- Fila 6 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    M=1985     // Valor 1985
    A=A+1
    M=80       // Valor 80

    // ---- Fila 7 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    M=15969    // Valor 15969
    A=A+1
    M=80       // Valor 80

    // ---- Fila 8 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    M=-4063    // Valor -4063 (equivalente a 61473 sin signo)
    A=A+1
    M=72       // Valor 72

    // ---- Fila 9 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    M=-32671   // Valor -32671 (equivalente a 32865 sin signo)
    A=A+1
    M=79       // Valor 79

    // ---- Fila 10 ----
    @31
    D=A
    @R12
    M=M+D
    @R12
    A=M
    M=-1       // Valor -1 (todos 1s)
    A=A+1
    M=127      // Valor 127

    @MAIN_LOOP
    0;JMP      // Volver al bucle principal

// Rutina para borrar la pantalla
(CLEAR_SCREEN)
    @SCREEN
    D=A
    @R12
    M=D        // R12 = dirección inicial de la pantalla
    
    @8192      // Número total de palabras en la pantalla (32x256)
    D=A
    @R13
    M=D        // R13 = contador
    
(CLEAR_LOOP)
    @R12
    A=M
    M=0        // Borrar la palabra actual
    
    @R12
    M=M+1      // Avanzar a la siguiente palabra
    
    @R13
    MD=M-1     // Decrementar contador
    @CLEAR_LOOP
    D;JGT      // Continuar si contador > 0
    
    @MAIN_LOOP
    0;JMP      // Volver al bucle principal
```

## Mejoras realizadas:

1. **Detección de tecla 'e'**: Añadí la comprobación para la tecla 'e' (101) que ejecuta la rutina CLEAR_SCREEN.

2. **Rutina de borrado**: Implementé CLEAR_SCREEN que:
   - Recorre toda la memoria de pantalla (8192 palabras)
   - Establece cada palabra a 0 (píxeles apagados)
   - Usa R12 como puntero y R13 como contador

3. **Simplificación del código**: Corregí las instrucciones redundantes en el dibujo del bitmap, haciendo el código más legible y eficiente.

El programa ahora responde a dos teclas:
- 'd' → Dibuja el bitmap
- 'e' → Borra toda la pantalla
  
---
# Retos

# Implementación de la Suma de los Primeros 100 Números Naturales en Hack Assembly

## Código Implementado (RAE1)

```assembly
// Programa para sumar los primeros 100 números naturales
// Variables:
//   i = R0 (contador)
//   sum = R1 (acumulador)

@R0
M=1         // i = 1 (inicialización)
@R1
M=0         // sum = 0 (inicialización)

(LOOP)
@R0         // Cargar i
D=M
@100        // Cargar 100
D=D-A       // D = i - 100
@END
D;JGT       // Si i > 100, salta a END

@R0         // Cargar i
D=M
@R1         // Cargar sum
M=D+M       // sum = sum + i

@R0         // Cargar i
M=M+1       // i = i + 1

@LOOP
0;JMP       // Repetir el ciclo

(END)
@END
0;JMP       // Bucle infinito (terminación)
```

## Pruebas Realizadas (RAE2)

### Pruebas de Componentes

1. **Inicialización**:
   - Verifiqué que R0 (i) se inicialice correctamente a 1
   - Confirmé que R1 (sum) comience en 0
   - Usé el simulador para inspeccionar los valores de memoria después de las primeras 2 instrucciones

2. **Condición del bucle**:
   - Probé manualmente el cálculo de i-100 con diferentes valores de i
   - Verifiqué que el salto a END ocurra solo cuando i > 100

3. **Acumulación**:
   - Comprobé que la suma se actualice correctamente en varias iteraciones
   - Verifiqué que i se incremente en 1 en cada ciclo

### Prueba Integral

1. **Ejecución completa**:
   - Ejecuté el programa hasta que entró en el bucle infinito (END)
   - Verifiqué que el valor final en R1 fuera 5050 (suma correcta de 1 a 100)
   - Confirmé que R0 terminó con valor 101 (indicando que el bucle hizo exactamente 100 iteraciones)

2. **Casos límite**:
   - Modifiqué temporalmente el límite a 5 para verificar la suma (1+2+3+4+5=15)
   - Cambié el valor inicial para verificar que el algoritmo funciona con otros rangos

## Análisis Conceptual

1. **Implementación de variables**:
   - `i` está implementada en R0 (registro 0)
   - `sum` está implementada en R1 (registro 1)
   - En Hack Assembly, las variables son posiciones de memoria o registros específicos

2. **Direcciones de memoria**:
   - R0 está en la dirección 0
   - R1 está en la dirección 1
   - Los registros R0-R15 son posiciones predefinidas en la RAM (0-15)

3. **Implementación del ciclo while**:
   - Se usa una etiqueta (LOOP) para marcar el inicio del bucle
   - La condición se evalúa restando i-100 y saltando si es positivo
   - El salto incondicional (0;JMP) al final crea la repetición

4. **Naturaleza de las variables**:
   - Una variable es una ubicación de memoria con nombre que almacena datos
   - La dirección de una variable es su ubicación en memoria (ej. R0=0)
   - El contenido es el valor almacenado en esa ubicación (ej. M=1)

5. **Almacenamiento**:
   - La variable i se almacena en RAM[0] (R0)
   - La memoria del computador Hack tiene 32K palabras de 16 bits
   - Los primeros 16 registros (R0-R15) son especiales y tienen usos convencionales




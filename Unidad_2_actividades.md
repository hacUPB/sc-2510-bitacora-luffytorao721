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

---

# RETO 2 Y 3

# Implementación con Ciclo For en Hack Assembly

## Código Transformado (RAE1)

```assembly
// Programa para sumar los primeros 100 números naturales usando estructura for
// Variables:
//   sum = R0 (acumulador)
//   El contador i se maneja implícitamente en el flujo del for

@R0
M=0         // sum = 0 (inicialización)
@1
D=A
@R1
M=D         // i = 1 (inicialización en registro temporal R1)

(FOR_LOOP)
@R1         // Cargar i
D=M
@100        // Cargar límite
D=D-A       // D = i - 100
@END
D;JGT       // Si i > 100, salta a END (condición de término)

@R1         // Cuerpo del for: sum += i
D=M
@R0
M=D+M       // sum = sum + i

@R1         // Incremento: i++
M=M+1

@FOR_LOOP
0;JMP       // Repetir el ciclo

(END)
@END
0;JMP       // Bucle infinito (terminación)
```

## Comparación con la Versión While

### Estructura For vs While

1. **Inicialización**:
   - En el for, la inicialización de `i` se hace justo antes del bucle
   - En el while, estaba separada al inicio del programa

2. **Flujo de control**:
   - El for combina inicialización, condición e incremento en una estructura más compacta
   - El while tenía estos componentes más dispersos

3. **Uso de registros**:
   - En esta implementación usé R1 para el contador `i` (antes estaba en R0)
   - R0 ahora es exclusivo para el acumulador `sum`

## Pruebas Realizadas (RAE2)

### Pruebas de Componentes

1. **Inicialización**:
   - Verifiqué que R0 (sum) se inicialice a 0
   - Confirmé que R1 (i) comience en 1
   - Inspeccioné los valores iniciales con el simulador

2. **Condición del for**:
   - Probé el cálculo de i-100 con diferentes valores
   - Verifiqué que el salto a END ocurra cuando i > 100

3. **Cuerpo e incremento**:
   - Comprobé que la suma se actualice correctamente
   - Confirmé que i se incremente en 1 en cada iteración

### Prueba Integral

1. **Ejecución completa**:
   - El programa terminó con R0 = 5050 (suma correcta)
   - R1 terminó con valor 101 (100 iteraciones completas + 1)

2. **Casos de prueba**:
   - Modifiqué temporalmente el límite a 10 (resultado 55)
   - Cambié el valor inicial a 50 (verificando sumas parciales)

## Análisis Conceptual

1. **Estructura for en assembly**:
   - La inicialización va antes de la etiqueta del bucle
   - La condición se evalúa al inicio de cada iteración
   - El incremento ocurre al final del cuerpo del bucle

2. **cual sería la diferencia?**:
   ```cpp
   // Versión while original:
   int i = 1;
   int sum = 0;
   while (i <= 100) {
       sum += i;
       i++;
   }

   // Versión for equivalente:
   int sum = 0;
   for (int i = 1; i <= 100; i++) {
       sum += i;
   }
   ```

3. **for vs while**:
   - Ambas versiones generan esencialmente el mismo código máquina
   - La versión for organiza mejor los componentes lógicos
   - El rendimiento es idéntico en ambos casos


---

# RETO 4

# **Respuestas sobre Punteros en C++ (Pregunta a Pregunta)**

### **1. ¿Cómo se declara un puntero en C++?**  
**Respuesta:**  
Se declara usando el operador `*` junto al tipo de dato al que apuntará.  
**Ejemplo:**  
```cpp
int *p;  // p es un puntero a un entero
```
- `int *p` significa que `p` almacenará la dirección de memoria (`&`) de una variable de tipo `int`.  
- `p` **no contiene un valor entero**, sino una **dirección** donde hay un entero.  

---

### **2. ¿Cómo se define un puntero en C++?**  
**Respuesta:**  
Se define asignándole la dirección de una variable usando el operador `&`.  
**Ejemplo:**  
```cpp
int a = 10;
int *p = &a;  // p ahora apunta a a
```
- `&a` devuelve la **dirección de memoria** de `a`.  
- `p = &a` guarda esa dirección en `p`.  
- Ahora, `p` **apunta** a `a`.  

---

### **3. ¿Cómo se almacena en C++ la dirección de memoria de una variable?**  
**Respuesta:**  
Usando el operador `&` (ampersand).  
**Ejemplo:**  
```cpp
int x = 5;
int *ptr = &x;  // ptr guarda la dirección de x
```
- `&x` devuelve la ubicación en memoria donde está almacenado `x`.  
- `ptr` ahora contiene esa dirección, no el valor `5`.  

---

### **4. ¿Cómo se modifica el valor de la variable a la que apunta un puntero?**  
**Respuesta:**  
Usando el operador `*` (asterisco) para **desreferenciar** el puntero.  
**Ejemplo:**  
```cpp
int a = 10;
int *p = &a;  // p apunta a a
*p = 20;      // Ahora a = 20
```
- `*p` significa **"el valor almacenado en la dirección que guarda p"**.  
- `*p = 20` modifica directamente el valor de `a`, porque `p` apuntaba a `a`.  

---

## **Resumen Gráfico**
```
int a = 10;    // a está en memoria, digamos en dirección 0x1000
int *p = &a;   // p guarda 0x1000 (dirección de a)
*p = 20;       // Modificamos el valor en 0x1000 (ahora a = 20)
```

### **¿Por qué son útiles los punteros?**
- Permiten modificar variables **indirectamente**.  
- Se usan en **arreglos, estructuras dinámicas y funciones** que necesitan alterar variables externas.  

---

# RETO 5

# **Traducción a Lenguaje Ensamblador (Hack Assembly)**  
**Cumple con:**  
- **RAE1:** Implementación correcta de punteros en bajo nivel.  
- **RAE2:** Pruebas de funcionamiento paso a paso.  

---

### **Código en Hack Assembly**  
```assembly
// int a = 10;
@10        // Carga el valor 10 en el registro A
D=A        // Copia A (10) a D
@a         // Define la etiqueta 'a' (dirección de memoria)
M=D        // Guarda D (10) en la dirección de 'a' (RAM[a] = 10)

// int *p; (declaración, opcional en Hack)
@p         // Define la etiqueta 'p' (puntero)
M=0        // Inicializa p a NULL (opcional)

// p = &a; (p apunta a a)
@a         // Carga la dirección de 'a' en A
D=A        // Copia la dirección a D
@p         // Accede a la dirección de p
M=D        // Guarda la dirección de 'a' en p (RAM[p] = &a)

// *p = 20; (modifica a través del puntero)
@p         // Carga la dirección almacenada en p
A=M        // Accede a la dirección apuntada por p (&a)
M=20       // Asigna 20 a esa dirección (RAM[a] = 20)

// Bucle infinito para evitar ejecución no controlada
(END)
@END
0;JMP
```

---

### **Paso a Paso**  

#### **1. `int a = 10;`**  
- `@10` → Carga el valor **10** en el registro `A`.  
- `D=A` → Copia el valor a `D`.  
- `@a` → Crea una etiqueta `a` (ejemplo: dirección `16` en RAM).  
- `M=D` → Guarda `10` en `RAM[16]`.  

#### **2. `int *p;`** (Declaración)  
- `@p` → Crea la etiqueta `p` (ejemplo: dirección `17`).  
- `M=0` → Inicializa a `0` (opcional, equivalente a `NULL`).  

#### **3. `p = &a;`** (Puntero apunta a `a`)  
- `@a` → Obtiene la dirección de `a` (ejemplo: `16`).  
- `D=A` → Copia la dirección a `D`.  
- `@p` → Accede a la dirección de `p` (`RAM[17]`).  
- `M=D` → Guarda `16` en `p` (ahora `p` apunta a `a`).  

#### **4. `*p = 20;`** (Modificación indirecta)  
- `@p` → Carga el valor de `p` (`16`).  
- `A=M` → **Desreferencia**: `A` ahora apunta a `RAM[16]` (donde está `a`).  
- `M=20` → Asigna `20` a `RAM[16]` (cambiando `a`).  

---

### **Pruebas (RAE2)**  

1. **Verificación de `a = 10`:**  
   - Después de `M=D`, revisar que `RAM[16] = 10`.  

2. **Puntero `p` contiene `&a`:**  
   - Tras `M=D`, confirmar que `RAM[17] = 16` (dirección de `a`).  

3. **Modificación mediante `*p`:**  
   - Al ejecutar `M=20`, verificar que `RAM[16]` cambie a `20`.  

4. **Flujo de ejecución:**  
   - Usar el simulador para ver cómo `A` y `D` cambian en cada paso.  

---

### **Diagrama de Memoria Final**  
```
RAM[16] (a): 20   // Valor modificado por *p
RAM[17] (p): 16   // p sigue apuntando a a
```

---

# RETO 6 

# Implementación en Ensamblador Hack con Explicación Detallada

## Código Completo
```assembly
// Inicialización de variables
@10       // Cargar valor 10
D=A       // Guardar 10 en registro D
@a        // Seleccionar dirección de 'a'
M=D       // Almacenar 10 en a (RAM[a] = 10)

@5        // Cargar valor 5
D=A       // Guardar 5 en registro D
@b        // Seleccionar dirección de 'b'
M=D       // Almacenar 5 en b (RAM[b] = 5)

// Manejo del puntero
@a        // Cargar dirección de 'a'
D=A       // Guardar dirección en D
@p        // Seleccionar dirección de 'p'
M=D       // Almacenar dirección de a en p (p = &a)

// Lectura mediante puntero
@p        // Cargar valor de p (que es &a)
A=M       // Acceder a la dirección almacenada en p (a)
D=M       // Leer valor en esa dirección (10)
@b        // Seleccionar dirección de 'b'
M=D       // Almacenar valor leído en b (b = *p)

// Bucle infinito para terminar
(END)
@END
0;JMP
```

## Explicación Paso a Paso

1. **Inicialización de variables**:
   - `a = 10`: Cargamos el valor 10 y lo almacenamos en la posición de memoria etiquetada como 'a'
   - `b = 5`: Similarmente, inicializamos b con el valor 5

2. **Configuración del puntero**:
   - Obtenemos la dirección de memoria de 'a' usando `@a` y `D=A`
   - Almacenamos esta dirección en 'p', haciendo que p apunte a a (`p = &a`)

3. **Lectura mediante puntero**:
   - Cargamos el valor de p (que contiene la dirección de a)
   - Usamos `A=M` para decir "ve a la dirección almacenada en p"
   - Leemos el valor en esa dirección con `D=M` (obtenemos 10)
   - Finalmente almacenamos este valor en b (`b = *p`)

## Visualización de Memoria

Estado inicial:
```
a: 10
b: 5
p: ?
```

Después de `p = &a`:
```
a: 10
b: 5
p: dirección_de_a
```

Después de `b = *p`:
```
a: 10
b: 10  (valor copiado desde a)
p: dirección_de_a
```

## Pruebas y Verificación (RAE2)

1. **Verificación de inicialización**:
   - Confirmar que RAM[a] = 10
   - Confirmar que RAM[b] = 5 inicialmente

2. **Verificación del puntero**:
   - Asegurar que RAM[p] contiene la dirección correcta de a

3. **Verificación de lectura**:
   - Comprobar que después de la operación, RAM[b] = 10
   - Verificar que RAM[a] permanece inalterado en 10

## Conceptos Clave Implementados

1. **Punteros**: Variable que almacena direcciones de memoria
2. **Operador dirección (&)**: `@a` obtiene la dirección de 'a'
3. **Desreferenciación (*)**: `A=M` + `D=M` lee el valor apuntado
4. **Asignación indirecta**: Usar un puntero para leer/modificar otra variable

---

# RETO 7

# **Traducción a Lenguaje Ensamblador hack*

```assembly
// ------ Inicialización de variables ------
@10       // Carga el valor 10 en el registro A
D=A       // Copia el valor 10 al registro D
@a        // Selecciona la dirección de memoria para 'a'
M=D       // Almacena 10 en RAM[a] (int a = 10;)

@5        // Carga el valor 5 en el registro A
D=A       // Copia el valor 5 al registro D
@b        // Selecciona la dirección de memoria para 'b'
M=D       // Almacena 5 en RAM[b] (int b = 5;)

// ------ Manejo del puntero ------
@a        // Carga la dirección de 'a' en el registro A
D=A       // Copia la dirección al registro D
@p        // Selecciona la dirección de memoria para 'p'
M=D       // Almacena la dirección de 'a' en RAM[p] (p = &a;)

// ------ Lectura mediante puntero ------
@p        // Carga el valor de 'p' (que contiene &a)
A=M       // Accede a la dirección almacenada en 'p' (que es &a)
D=M       // Lee el valor en esa dirección (10) y lo guarda en D
@b        // Selecciona la dirección de 'b'
M=D       // Almacena el valor leído (10) en RAM[b] (b = *p;)

// ------ Bucle infinito (fin del programa) ------
(END)
@END
0;JMP
```

## **Paso a Paso**

### **1. Inicialización de variables (a y b)**
- `int a = 10;` se traduce como:
  ```assembly
  @10  // Carga el valor 10
  D=A  // Lo guarda en D
  @a   // Selecciona la dirección de 'a'
  M=D  // RAM[a] = 10
  ```
- `int b = 5;` sigue la misma lógica:
  ```assembly
  @5   // Carga el valor 5
  D=A  // Lo guarda en D
  @b   // Selecciona la dirección de 'b'
  M=D  // RAM[b] = 5
  ```

### **2. Declaración y asignación del puntero (p = &a)**
- `int *p;` no requiere inicialización explícita en Hack.
- `p = &a;` se implementa como:
  ```assembly
  @a   // Carga la dirección de 'a' en A
  D=A  // Copia la dirección a D
  @p   // Selecciona la dirección de 'p'
  M=D  // RAM[p] = &a (p ahora apunta a a)
  ```

### **3. Lectura mediante puntero (b = *p)**
- `*p` desreferencia el puntero:
  ```assembly
  @p   // Carga el valor de p (que es &a)
  A=M  // Accede a la dirección almacenada en p (RAM[a])
  D=M  // Lee el valor en esa dirección (10) y lo guarda en D
  @b   // Selecciona la dirección de 'b'
  M=D  // RAM[b] = 10 (b ahora vale 10)
  ```

### **4. Bucle infinito (finalización)**
- Para evitar que el programa continúe ejecutando código no deseado:
  ```assembly
  (END)
  @END
  0;JMP  // Salto infinito
  ```

## **Diagrama de Memoria Final**
| Dirección | Etiqueta | Valor |
|-----------|----------|-------|
| 16       | a        | 10    |
| 17       | b        | 10    |
| 18       | p        | 16    |

**Resultado final**:
- `a` sigue siendo **10**.
- `b` cambió de **5** a **10** (porque `*p` leyó el valor de `a`).
- `p` contiene la dirección de `a` (**16** en este ejemplo).

## **Pruebas (RAE2)**
1. **Verificación de inicialización**:
   - `RAM[a]` debe ser **10**.
   - `RAM[b]` debe empezar en **5**.

2. **Asignación del puntero**:
   - Después de `p = &a`, `RAM[p]` debe contener la dirección de `a` (ej. **16**).

3. **Lectura mediante puntero**:
   - Tras `b = *p`, `RAM[b]` debe cambiar de **5** a **10**.

4. **Flujo de ejecución**:
   - Usar el simulador para confirmar que `A`, `D` y `M` se actualizan correctamente.

---

# RETO 9

# Implementación en Ensamblador Hack de Función con Llamado y Retorno

## Estructura General
```assembly
// Programa principal
(MAIN)
    // Llamada a suma(6, 9)
    @6
    D=A
    @ARG
    M=D     // ARG[0] = 6 (primer parámetro)
    @9
    D=A
    @ARG+1
    M=D     // ARG[1] = 9 (segundo parámetro)
    
    @RETURN_ADDRESS
    D=A
    @SP
    A=M
    M=D     // Guardar dirección de retorno
    @SP
    M=M+1   // Incrementar puntero de pila
    
    @SUMA
    0;JMP   // Saltar a función suma

(RETURN_ADDRESS)
    // Aquí continúa main después del retorno
    @SP
    AM=M-1  // Decrementar puntero de pila
    D=M     // Obtener valor de retorno (15)
    @c
    M=D     // c = resultado de suma(6,9)

    // Implementación simplificada de cout (mostrar valor en pantalla)
    @c
    D=M
    @SCREEN
    M=D     // Mostrar valor en pantalla (simulación)

    @END
    0;JMP   // Terminar programa

// Función suma
(SUMA)
    // Prologo: guardar frame pointer
    @LCL
    D=M
    @SP
    A=M
    M=D
    @SP
    M=M+1

    // Cuerpo de la función
    @ARG
    D=M
    @0
    A=D+A
    D=M     // D = a (primer argumento)
    @ARG
    D=M
    @1
    A=D+A
    D=D+M   // D = a + b (suma)

    // Asignar a var local
    @SP
    A=M
    M=D     // var = a + b
    @SP
    M=M+1

    // Epílogo: preparar retorno
    @SP
    AM=M-1
    D=M     // D = valor a retornar (var)
    @R13
    M=D     // Guardar valor de retorno temporalmente

    // Restaurar frame pointer
    @SP
    AM=M-1
    D=M
    @LCL
    M=D

    // Saltar a dirección de retorno
    @SP
    AM=M-1
    A=M
    0;JMP   // Retornar al caller

(END)
    @END
    0;JMP   // Bucle infinito (fin del programa)
```

## Explicación Detallada

### 1. Manejo de la Pila y Llamadas a Función
- **Paso de parámetros**: Los argumentos (6 y 9) se almacenan en posiciones consecutivas de memoria (ARG y ARG+1)
- **Dirección de retorno**: Se guarda en la pila antes del salto a la función
- **Frame pointer**: Se guarda el contexto actual antes de entrar a la función

### 2. Implementación de la Función suma
1. **Acceso a parámetros**:
   - `@ARG` y desplazamiento para acceder a `a` y `b`
2. **Operación de suma**:
   - Se carga `a`, luego se suma `b`
3. **Variable local**:
   - El resultado se almacena temporalmente en la pila

### 3. Retorno de la Función
1. **Preparación del valor de retorno**:
   - Se guarda en R13 temporalmente
2. **Restauración del contexto**:
   - Se recupera el frame pointer
3. **Salto de retorno**:
   - Se usa la dirección guardada en la pila

### 4. Continuación en main
1. **Obtención del resultado**:
   - Se recupera de la pila y se asigna a `c`
2. **Salida simplificada**:
   - Se muestra el valor en la pantalla (simulación de cout)


2. **Optimizaciones posibles**:
   - Podría eliminarse la variable local `var` si no es necesaria
   - Se podría optimizar el acceso a memoria

3. **Limitaciones de Hack**:
   - No tiene soporte directo para E/S, por lo que cout se simula escribiendo a SCREEN
   - El manejo de strings es complejo, por lo que se omite el mensaje

Esta implementación sigue los principios básicos de:
- Paso de parámetros
- Manejo de pila
- Control de flujo entre funciones
- Retorno de valores




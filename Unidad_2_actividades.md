

## **Ejercicio 1: Entrada-Salida Mapeada a Memoria**

### **Preguntas y Respuestas**
1. **¿Qué es la entrada-salida mapeada a memoria?**
   - Es una técnica donde los dispositivos de entrada/salida (como el teclado o la pantalla) se controlan mediante direcciones de memoria específicas. En lugar de usar instrucciones especiales para acceder a los dispositivos, se lee y escribe en direcciones de memoria asociadas a ellos.

2. **¿Cómo se implementa en la plataforma Hack?**
   - En la plataforma Hack, hay dos registros especiales:
     - **KBD** (dirección 24576): Al leer esta dirección, se obtiene el código ASCII de la tecla presionada.
     - **SCREEN** (dirección 16384): Al escribir en esta dirección, se modifican los píxeles de la pantalla.

3. **Programa de ejemplo:**
   - Este programa lee una tecla del teclado y la muestra en la pantalla.
   ```assembly
   @KBD         // Accede al registro del teclado
   D=M          // Lee el código ASCII de la tecla presionada
   @SCREEN      // Accede al registro de la pantalla
   M=D          // Escribe el código ASCII en la primera posición de la pantalla
   ```

4. **Simulación:**
   - Usa el simulador de la plataforma Hack para probar el programa. Presiona una tecla y verifica que el código ASCII aparezca en la pantalla.

---

## **Ejercicio 2: Revisión del Reto 20**

- Revisa el reto 20 de la unidad anterior. Asegúrate de entender cómo funciona el programa, cómo se simula y cómo se interactúa con él. Experimenta modificando el código para familiarizarte con su comportamiento.

---

## **Ejercicio 3: Modificación del Reto 20**

### **Descripción**
Modifica el programa del reto 20 para que:
- Si se presiona la tecla "d", se muestre la imagen diseñada en el reto 18.
- Si no se presiona ninguna tecla, se borre la imagen.

### **Código**
```assembly
(LOOP)
    @KBD         // Accede al registro del teclado
    D=M          // Lee el código ASCII de la tecla presionada
    @SHOW_IMAGE  // Si no se presiona ninguna tecla, salta a borrar la imagen
    D;JEQ        
    @68          // Código ASCII de 'd'
    D=D-A        // Compara con 'd'
    @SHOW_IMAGE  // Si se presiona 'd', muestra la imagen
    D;JEQ        
    @LOOP        // Vuelve al inicio del ciclo
    0;JMP

(SHOW_IMAGE)
    // Código para mostrar la imagen del reto 18
    @LOOP        // Vuelve al ciclo principal
    0;JMP

(CLEAR_SCREEN)
    // Código para borrar la pantalla
    @LOOP        // Vuelve al ciclo principal
    0;JMP
```

---

## **Ejercicio 4: Nueva Variación del Programa**

### **Descripción**
Modifica el programa de la actividad anterior para que:
- Si se presiona la tecla "d", se muestre la imagen.
- Si se presiona la tecla "e", se borre la imagen.

### **Código**
```assembly
(LOOP)
    @KBD         // Accede al registro del teclado
    D=M          // Lee el código ASCII de la tecla presionada
    @SHOW_IMAGE  // Si no se presiona ninguna tecla, salta a borrar la imagen
    D;JEQ        
    @68          // Código ASCII de 'd'
    D=D-A        // Compara con 'd'
    @SHOW_IMAGE  // Si se presiona 'd', muestra la imagen
    D;JEQ        
    @69          // Código ASCII de 'e'
    D=D-1        // Compara con 'e'
    @CLEAR_SCREEN // Si se presiona 'e', borra la imagen
    D;JEQ        
    @LOOP        // Vuelve al inicio del ciclo
    0;JMP

(SHOW_IMAGE)
    // Código para mostrar la imagen del reto 18
    @LOOP        // Vuelve al ciclo principal
    0;JMP

(CLEAR_SCREEN)
    // Código para borrar la pantalla
    @LOOP        // Vuelve al ciclo principal
    0;JMP
```

---

## **Ejercicio 5: Suma de los Primeros 100 Números Naturales**

### **Preguntas y Respuestas**
1. **¿Cómo están implementadas las variables `i` y `sum`?**
   - En ensamblador, las variables se almacenan en direcciones de memoria. Por ejemplo:
     - `i` podría estar en la dirección 16.
     - `sum` podría estar en la dirección 17.

2. **¿En qué direcciones de memoria están estas variables?**
   - Depende del programa, pero por ejemplo:
     - `i` en la dirección 16.
     - `sum` en la dirección 17.

3. **¿Cómo está implementado el ciclo `while`?**
   - Se usa una etiqueta (`LOOP`) y un salto condicional (`JGT`) para repetir el ciclo mientras `i <= 100`.

4. **Código**
```assembly
@i
M=1          // Inicializa i = 1
@sum
M=0          // Inicializa sum = 0

(LOOP)
    @i
    D=M          // D = i
    @100
    D=D-A        // D = i - 100
    @END
    D;JGT        // Si i > 100, salta a END
    @i
    D=M          // D = i
    @sum
    M=D+M        // sum = sum + i
    @i
    M=M+1        // i = i + 1
    @LOOP
    0;JMP        // Repite el ciclo

(END)
    @END
    0;JMP        // Fin del programa
```

---

## **Ejercicio 6: Punteros en Ensamblador**

### **Descripción**
Traduce a ensamblador un programa en C++ que usa punteros:
```cpp
int a = 10;
int *p;
p = &a;
*p = 20;
```

### **Código**
```assembly
@a
M=10         // a = 10
@p
M=&a         // p = &a (dirección de a)
@p
A=M          // Accede a la dirección almacenada en p
M=20         // *p = 20
```

---

## **Ejercicio 7: Lectura mediante Punteros**

### **Descripción**
Traduce a ensamblador un programa en C++ que lee una variable usando un puntero:
```cpp
int a = 10;
int b = 5;
int *p;
p = &a;
b = *p;
```

### **Código**
```assembly
@a
M=10         // a = 10
@b
M=5          // b = 5
@p
M=&a         // p = &a (dirección de a)
@p
A=M          // Accede a la dirección almacenada en p
D=M          // D = *p (valor de a)
@b
M=D          // b = *p (b = 10)
```

---

## **Ejercicio 8: Preguntas Conceptuales sobre Punteros**

### **Preguntas y Respuestas**
1. **¿Qué hace `int *pvar;`?**
   - Declara un puntero `pvar` que apunta a un entero.

2. **¿Qué hace `pvar = var;`?**
   - Asigna la dirección de `var` a `pvar`.

3. **¿Qué hace `var2 = *pvar`?**
   - Asigna a `var2` el valor de la variable apuntada por `pvar`.

4. **¿Qué hace `pvar = &var3`?**
   - Asigna la dirección de `var3` a `pvar`.

---

## **Ejercicio 9: Traducción de una Función en C++ a Ensamblador**

### **Descripción**
Traduce a ensamblador una función en C++ que suma dos números:
```cpp
int suma(int a, int b) {
    return a + b;
}
```

### **Código**
```assembly
(SUMA)
    @a
    D=M          // D = a
    @b
    D=D+M        // D = a + b
    @RET
    A=M          // Retorna a la dirección almacenada en RET
    0;JMP

(MAIN)
    @6
    D=A
    @a
    M=D          // a = 6
    @9
    D=A
    @b
    M=D          // b = 9
    @RET
    M=$+1        // Guarda la dirección de retorno
    @SUMA
    0;JMP        // Llama a la función suma
    @c
    M=D          // c = resultado de suma
    // Imprime el valor de c (esto depende del sistema)
    @END
    0;JMP

(END)
    @END
    0;JMP        // Fin del programa
```

---



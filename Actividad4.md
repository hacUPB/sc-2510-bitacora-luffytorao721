## Actividad 4

<!--@16384
D = A
@16
M = D -->

```asm
@100  
D = A   
@32  
M = D
```

## Reto 1 
- Para cargar el valor 1978 en el registro D, se debe usar la instrucción @1978 para apuntar a ese valor y luego D=A para cargarlo en el registro D.

```asm
@1978
D=A
```

## Reto 2
- Para guardar el número 69 en la posición 100 de la memoria RAM, primero se tiene apuntar a la posición de memoria 100 y luego almacenar el valor.
```asm
@100
M=69
```

## Reto 3
```ams
@24
D=M
@200
M=D
```
- @24: Cargar la dirección 24 en A.
- D=M: Cargar en D el valor almacenado en la posición 24.
- @200: Cargar la dirección 200 en A.
- M=D: Guardar el valor de D (el contenido de la posición 24) en la posición 200.

## Reto 4
```asm
@100
D=M
@15
D=D-A
@100
M=D
```
- @100: Cargar la dirección 100 en A.
- D=M: Cargar en D el valor de la posición 100.
- @15: Cargar la constante 15 en A.
- D=D-A: Restar 15 a D.
- @100: Cargar la dirección 100 en A.
- M=D: Guardar el resultado (D) en la posición 100.

## Reto 5

```asm
@0
D=M
@1
D=D+M
@69
D=D+M
@2
M=D
```

- @0: Cargar la dirección 0 en A.
- D=M: Cargar en D el valor almacenado en la posición 0.
- @1: Cargar la dirección 1 en A.
- D=D+M: Sumar el valor de la posición 1 a D.
- @69: Cargar la constante 69 en A.
- D=D+M: Sumar 69 a D.
- @2: Cargar la dirección 2 en A.
- M=D: Guardar el resultado final (D) en la posición 2.

 ## Reto 6

```asm
@D
D=M
@ZERO
D;JEQ
@100
0;JMP
```
- @D: Cargar el registro D en A.
- D=M: Cargar el valor almacenado en D.
- @ZERO: Cargar una etiqueta de salto (para definir la condición de comparación).
- D;JEQ: Si D es igual a 0, hacer el salto.
- @100: Si D es 0, hacer un salto a la dirección 100 de la ROM.

## Reto 7 

```asm
@100
D=M
@ZERO
D;JEQ
@100
0;JMP
```
## Reto 8

#### **Interpretar el código**
```asm
@var1
D = M
@var2
D = D + M
@var3
M = D
```
- @var1: Carga la dirección de var1 en el registro A.
- D = M: Carga el valor almacenado en var1 (en la memoria en la dirección que apunta A) en el registro D.
- @var2: Carga la dirección de var2 en el registro A.
- D = D + M: Suma el valor de var2 con el valor que está en D (que inicialmente es el valor de var1).
- @var3: Carga la dirección de var3 en el registro A.
- M = D: Guarda el valor de D (que ahora es la suma de los valores de var1 y var2) en la dirección de var3.

#### **¿En qué posiciones de memoria están var1, var2, var3?**

Por lo general, las posiciones de memoria no están explícitamente definidas en el código ensamblador proporcionado. No se da un contexto inicial sobre la posición de las variables por lo que se intuye que están colocadas de forma secuencial
en memoria, en este caso a partir de la posición 16.

## Reto 9
#### **Traducir código a lenguaje ensamblador**

```cpp
i = 1
sum = 0
sum = sum + i
i = i + 1
```

```asm
// i = 1
@i
M=1
// sum = 0
@sum
M=0
// sum = sum + i
@i
D=M
@sum
M=D+M
// i = i + 1
@i
D=M+1
@i
M=D
```

#### **¿Qué hace el programa?**

El programa calcula la suma de i y sum y luego incrementa el valor de i

- i = 1: Se almacena el valor 1 en la variable i.
- sum = 0: Se almacena el valor 0 en la variable sum.
- sum = sum + i: Se toma el valor de i, se agrega al valor de sum, y el resultado se guarda de nuevo en sum.
- i = i + 1: Se incrementa en 1 el valor de i y se guarda el resultado en i.

#### Las variables en el lenguaje ensamblador se comenzarían a guardar desde la posición 16 esto debido a que las posiciones (0 a 15) estan reservados a registros especiales y variables del sistema.

#### **Optimización del programa**
```asm
// i = i + 1
@i
D=M+1
@i
M=D
```
Lo único que hay que hacer es obviar el registro D.

```asm
@i
M=M+1
```








## Reto 10
- La idea es duplicar el contenido de R0 y almacenarlo en R1. Una forma sencilla es cargar el valor de R0 en el registro D, sumarlo consigo mismo y guardar el resultado en R1
```asm
@R0      // Este coso hace que se cargue la dirección simbólica R0 (es 0)
D=M      // D=valor almacenado en R0
@R0      // Volver a cargar R0 para sumar su valor
D=D+M    // D = R0+R0(2*R0)
@R1      // Seleccionar la dirección R1 (es 1)
M=D      // Almacenar el resultado en R1
```
## Reto 11
#### *Decremento de i*
- Teniendo este programa en alto nivel

```c
i = 1000;
LOOP:
  if (i == 0) goto CONT;
  i = i - 1;
  goto LOOP;
CONT:
```
- Traducido a:
```asm
// i = 1000
@1000
D=A
@i
M=D
(LOOP)
// if (i == 0) goto CONT
@i
D=M
@CONT
D;JEQ
// i = i - 1
@i
M=M-1
// goto LOOP
@LOOP
0;JMP
(CONT)
```
### **¿Qué hace?**
Establece la variable i en 1000 y luego ejecuta un bucle en el cual se decrementa i hasta llegar a 0.

### **¿En qué memoria está almacenada la variable i? ¿En qué dirección de esa memoria?**
La variable i es una variable simbólica. En el ensamblador Hack, los símbolos no predefinidos se asignan en la RAM a partir de la dirección 16. Por lo tanto, i se almacena en la RAM en la dirección 16.

### **¿En qué memoria y en qué dirección de memoria está almacenado el comentario // i = 1000?**
Los comentarios no se traducen a código máquina. No se almacenan en ROM ni en RAM, son ignorados por el ensamblador.

### **¿Cuál es la primera instrucción del programa anterior? ¿En qué memoria y en qué dirección de memoria está almacenada esa instrucción?**
La primera instrucción es @1000. Esta instrucción se almacena en la ROM, en la dirección 0 (ya que la ROM contiene el programa).

### **¿Qué son CONT y LOOP?**
Son etiquetas (labels) que marcan posiciones del programa en ROM y se usan para saltos (branching).

### **¿Cuál es la diferencia entre los símbolos i y CONT?**
i es un símbolo que representa una variable en la RAM (datos), mientras que CONT es un símbolo que marca una posición en la ROM (una dirección de instrucción).


## Reto 12

- Implementar en ensamblador

```c
R4 = R1 + R2 + 69;
```

```asm
@R1
D=M         // D = R1
@R2
D=D+M       // D = R1 + R2
@69
D=D+A       // D = R1 + R2 + 69   (A contiene 69)
@R4
M=D         // Almacena el resultado en R4
```

## Reto 13

- Bucle infinito en asm

```c
if (R0 >= 0) 
    R1 = 1;
else 
    R1 = -1;

(LOOP)
goto LOOP;
```

Como la ALU del Hack sólo permite saltos condicionales basados en el valor en D y no dispone de una instrucción JGE, se puede usar JLT para detectar si R0 es negativo. Si R0 es negativo, se salta a la etiqueta (NEG), y de lo contrario se continúa con la asignación de 1 a R1. Finalmente, se entra en un bucle infinito.

```asm
@R0
D=M
@NEG      // Si D < 0, salta a NEG
D;JLT
// Caso R0 >= 0:
@1
D=A
@R1
M=D
@LOOP
0;JMP
(NEG)
// Caso R0 < 0:
@-1
D=A
@R1
M=D
@LOOP
0;JMP
(LOOP)
```
## Reto 14

- Valor en memoria direccionar

```c
R4 = RAM[R1];
```
- Traducido como

```asm
@R1
A=M       // A toma el valor almacenado en R1 (dirección de RAM)
D=M       // D = RAM[A]
@R4
M=D       // R4 = D
```
- Se utiliza el valor almacenado en R1 como dirección de memoria para leer un dato de RAM, y luego se guarda en R4.

## Reto 15
- Almacenar una región en memoria. *En la posición R0 está almacenada la dirección inicial de una región de memoria. En la posición R1 está almacenado el tamaño de la región. Almacena un -1 en esa región de memoria.*
Se tiene que usar un bucle que, para cada celda de la región, escriba el valor -1. Se emplea un registro auxiliar (por ejemplo, R2) para apuntar a la dirección actual y se decrementa el contador almacenado en R1 hasta llegar a 0.

```asm
// R0: dirección inicial de la región
// R1: tamaño de la región
@R0
D=M
@R2
M=D         // R2 = dirección actual
(LOOP)
@R1
D=M
@END
D;JEQ       // Si R1 == 0, termina
// Escribir -1 en la dirección apuntada por R2:
@-1
D=A
@R2
A=M
M=D
// Incrementar la dirección almacenada en R2:
@R2
M=M+1
// Decrementar el contador en R1:
@R1
M=M-1
@LOOP
0;JMP
(END)
```
## Reto 16 
- Hacer la suma de un arreglo

```c
int[] arr = new int[10];
int sum = 0;
for (int j = 0; j < 10; j++) {
    sum = sum + arr[j];
}
```
### ** Qué hace? **
- Declara un arreglo de 10 enteros, inicializa sum en 0 y luego suma todos los elementos del arreglo, almacenando el resultado en sum.

### ** ¿Cuál es la dirección base de arr en la memoria RAM? **
- Dado que los registros R0–R15 están reservados y las variables se asignan a partir de la dirección 16, se suele asignar arr a partir de la dirección 16.

### ¿Cuál es la dirección base de sum en la memoria RAM y por qué?
- Al declarar arr primero, éste ocupa 10 posiciones (de la 16 a la 25) y la siguiente variable (sum) se asigna a la siguiente dirección disponible, es decir, a la 26.

### ¿Cuál es la dirección base de j en la memoria RAM y por qué?
- La variable j se utiliza como contador en el bucle. Asumiendo que se asignan variables en orden de aparición, j se ubicaría en la siguiente dirección, osea en la 27.

```asm
// Inicializar arr (se asume que el arreglo ya tiene datos)
// Inicializar sum = 0
@0
D=A
@sum
M=D
// Inicializar j = 0
@0
D=A
@j
M=D
(LOOP)
  // if (j == 10) goto END
  @10
  D=A
  @j
  D=M-D
  @END
  D;JEQ
  // sum = sum + RAM[arr + j]
  @arr
  D=M       // D = dirección base de arr
  @j
  A=M+D     // A = arr + j
  D=M       // D = arr[j]
  @sum
  M=M+D     // sum = sum + arr[j]
  // j = j + 1
  @j
  M=M+1
  @LOOP
  0;JMP
(END)
```
## Reto 17
- Salto condicional en D- 7

```c
if ((D - 7) == 0) goto ROM[69];
```
- Solo se tendria qu que restar el 7 al valor de D y se saltaria si eseo es igual a 0.

```asm
@7
D=D-A     // D = D - 7
@69
D;JEQ     // Si (D-7)==0, salta a la instrucción en ROM[69]
```

## Reto 18

- Trabajo del Bitmap

```hack
(draw)
	// put bitmap location value in R12
	// put code return address in R13
	@SCREEN
	D=A
	@R12
	AD=D+M
	// row 5
	M=-1
	AD=A+1 // D holds addr
	@127 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 6
	D=A // D holds previous addr
	@31
	AD=D+A
	@17 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	AD=A+1 // D holds addr
	@64 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 7
	D=A // D holds previous addr
	@31
	AD=D+A
	@17 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	AD=A+1 // D holds addr
	@64 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 8
	D=A // D holds previous addr
	@31
	AD=D+A
	@31 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	AD=A+1 // D holds addr
	@96 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 9
	D=A // D holds previous addr
	@31
	AD=D+A
	M=1
	AD=A+1 // D holds addr
	@96 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 10
	D=A // D holds previous addr
	@31
	AD=D+A
	@1985 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	AD=A+1 // D holds addr
	@80 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 11
	D=A // D holds previous addr
	@31
	AD=D+A
	@15969 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	AD=A+1 // D holds addr
	@80 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 12
	D=A // D holds previous addr
	@31
	AD=D+A
	@4063 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=A-D // RAM[addr]=-val
	AD=A+1 // D holds addr
	@72 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 13
	D=A // D holds previous addr
	@31
	AD=D+A
	@32671 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=A-D // RAM[addr]=-val
	AD=A+1 // D holds addr
	@79 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 14
	D=A // D holds previous addr
	@31
	AD=D+A
	M=-1
	AD=A+1 // D holds addr
	@127 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// return
	@R13
	A=M
	D;JMP

}
```
## Reto 19
### Este programa en lenguaje de máquina corresponde a un programa compilado para la plataforma Hack. Basado en la estructura y las instrucciones (instrucciones A y C), el programa realiza lo siguiente:

-Carga una dirección (por ejemplo, la dirección base de la pantalla o una posición en memoria).
-Realiza operaciones aritméticas y lógicas, probablemente para modificar la memoria de la pantalla.
-Utiliza saltos condicionales y bucles para iterar sobre rangos de memoria.

El programa manipula directamente la memoria del dispositivo (como la pantalla) para dibujar un patrón gráfico o un bitmap. Cada instrucción se corresponde con una operación de la ALU o un salto, lo que en conjunto permite dibujar una imagen predefinida en la pantalla del Hack Computer.

## Reto 20

- Para lograrlo, se debe leer el teclado (la dirección de memoria mapeada para el teclado es 24576) y comparar el valor con el código ASCII de la tecla “d” (que es 100 decimal). Si se cumple la condición, se procede a copiar el bitmap almacenado (por ejemplo, en una variable o sección de memoria) a la memoria de la pantalla (que comienza en la dirección 16384). Se ilustra a continuación un ejemplo simplificado:

```asm
// Comprobar si se presionó la tecla "d"
@24576
D=M           // D = valor del teclado
@100          // 100 decimal = 'd'
D=D-A
@DRAW         // Si D==0, entonces se presionó "d"
D;JEQ
// Si no se presionó "d", saltar al fin del programa
@END
0;JMP

(DRAW)
// Aquí se copia el bitmap a la memoria de la pantalla
// Supongamos que el bitmap está almacenado a partir de la etiqueta BITMAP
@BITMAP
D=A         // Dirección de inicio del bitmap en ROM o en una variable
@SCREEN     // SCREEN es la etiqueta para la dirección base de la pantalla (16384)
(LOOP_DRAW)
  // Copiar palabra del bitmap a la pantalla
  A=D       // Usamos D como dirección fuente (suponiendo un bitmap almacenado en memoria)
  D=M       // D = contenido del bitmap
  @SCREEN
  A=M       // A = dirección actual en pantalla
  M=D       // Escribir en pantalla
  // Incrementar ambas direcciones:
  @BITMAP
  M=M+1
  @SCREEN
  M=M+1
  // Se puede utilizar un contador para determinar cuándo terminar la copia
  // Aquí se asume que el bitmap tiene N palabras y se finaliza en ese punto
  // ...
  @LOOP_DRAW
  0;JMP

(END)
```





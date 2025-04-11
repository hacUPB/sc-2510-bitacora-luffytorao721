# Bitácora de Aprendizaje: Estructuras de Datos en Programación Creativa

## Actividad 1: Listas Enlazadas - Análisis Profundo

### Pregunta 3: ¿Qué es una lista enlazada y en qué se diferencia de un arreglo en cuanto a la forma en que los elementos están almacenados en la memoria?

**Respuesta:**
Una lista enlazada es una estructura de datos lineal donde cada elemento (nodo) contiene:
- Los datos a almacenar
- Un puntero/referencia al siguiente nodo en la secuencia

**Diferencias clave con arreglos:**
1. **Almacenamiento físico**:
   - Arreglos: Memoria contigua y fija
   - Listas: Nodos dispersos en memoria, conectados lógicamente

2. **Acceso**:
   - Arreglos: O(1) por índices
   - Listas: O(n) por recorrido secuencial

3. **Flexibilidad**:
   - Arreglos: Tamaño fijo (en C++)
   - Listas: Crecimiento dinámico sin reorganización

Ejemplo visual:
```
Arreglo: [A][B][C][D][ ] (espacios contiguos)
Lista: A -> B -> C -> D (nodos en direcciones aleatorias)
```

### Pregunta 4: Al observar el código de una lista enlazada en C++, ¿Cómo crees que se vinculan los nodos entre sí? ¿Qué estructura se utiliza para lograr esta conexión?

**Respuesta:**
La conexión se logra mediante:
1. **Puntero next**: Cada nodo contiene un puntero al siguiente
2. **Estructura autoreferencial**:
   ```cpp
   class Node {
   public:
       float x, y;
       Node* next; // Puntero al mismo tipo
   };
   ```
3. **Terminación**: El último nodo apunta a nullptr

Ejemplo de vinculación:
```cpp
Node* node1 = new Node(10,20);
Node* node2 = new Node(30,40);
node1->next = node2; // Vinculación
node2->next = nullptr; // Fin de lista
```

### Pregunta 5: ¿Cómo se gestiona la memoria en una lista enlazada? Investiga cómo se crea y se destruye un nodo en memoria utilizando el operador new y delete en C++.

**Respuesta:**
**Ciclo de vida de un nodo**:
1. Creación (new):
   ```cpp
   Node* newNode = new Node(x,y); // Asigna memoria en heap
   ```
   - Reserva memoria para el objeto
   - Llama al constructor

2. Destrucción (delete):
   ```cpp
   delete node; // Libera memoria
   ```
   - Llama al destructor
   - Devuelve memoria al sistema

**Riesgos**:
- Memory leaks si no se libera
- Dangling pointers si se accede después de delete
- Double free si se libera dos veces

### Pregunta 6: Considerando la estructura de una lista enlazada, ¿qué ventajas ofrece en comparación con un arreglo cuando se trata de insertar o eliminar elementos en posiciones intermedias?

**Respuesta:**
**Comparación de operaciones**:

| Operación         | Arreglo | Lista Enlazada |
|-------------------|---------|----------------|
| Inserción en medio | O(n)    | O(1)*          |
| Eliminación en medio | O(n)   | O(1)*          |
| Acceso aleatorio  | O(1)    | O(n)           |

*Asumiendo que ya se tiene la referencia al nodo anterior

**Ventajas clave de listas**:
1. **Inserción/eliminación eficiente**: Solo requiere cambiar punteros
2. **Sin desplazamientos**: No necesita mover otros elementos
3. **Crecimiento dinámico**: No requiere redimensionamiento

Ejemplo práctico:
```cpp
// Insertar nuevo nodo después de 'prevNode'
void insertAfter(Node* prevNode, float x, float y) {
    Node* newNode = new Node(x,y);
    newNode->next = prevNode->next; // 1
    prevNode->next = newNode;       // 2
    // Solo 2 operaciones de punteros!
}
```

### Pregunta 7: En el código presentado, ¿Cómo se asegura que no haya fugas de memoria? ¿Qué papel juega el destructor en la clase LinkedList?

**Respuesta:**
**Mecanismos anti-fugas**:
1. **Destructor (~LinkedList)**:
   ```cpp
   ~LinkedList() {
       clear(); // Delega la limpieza
   }
   ```
2. **Método clear()**:
   ```cpp
   void clear() {
       Node* current = head;
       while (current != nullptr) {
           Node* next = current->next;
           delete current; // Libera memoria
           current = next;
       }
       head = tail = nullptr;
   }
   ```
3. **Liberación manual**:
   ```cpp
   void keyPressed(int key) {
       if (key == 'c') snake.clear();
   }
   ```

**Flujo de limpieza**:
1. Cuando la LinkedList sale de ámbito → se llama a ~LinkedList()
2. ~LinkedList() llama a clear()
3. clear() recorre toda la lista liberando cada nodo
4. Finalmente establece head y tail a nullptr

### Pregunta 8: ¿Qué sucede en la memoria cuando se invoca el método clear() en una lista enlazada? Explica paso a paso cómo se liberan los recursos.

**Respuesta:**
**Proceso detallado de clear()**:

1. **Inicialización**:
   ```cpp
   Node* current = head; // Comienza desde la cabeza
   ```

2. **Bucle de limpieza**:
   ```cpp
   while (current != nullptr) {
       // Paso 1: Guardar referencia al siguiente
       Node* nextNode = current->next;
       
       // Paso 2: Liberar nodo actual
       delete current; // Llama al destructor de Node
       
       // Paso 3: Avanzar al siguiente
       current = nextNode;
   }
   ```

3. **Estado final**:
   ```cpp
   head = tail = nullptr; // Lista vacía
   size = 0;
   ```

**Visualización en memoria**:
```
Antes: [Head] → N1 → N2 → N3 → [Tail]
Durante: 
   Delete N1 → Libera memoria de N1
   Delete N2 → Libera memoria de N2
   Delete N3 → Libera memoria de N3
Después: [Head] → nullptr, [Tail] → nullptr
```

### Pregunta 9: Explica cómo cambia la estructura en memoria de una lista enlazada al agregar un nuevo nodo al final de la lista. ¿Cómo afecta esto al rendimiento de la lista enlazada?

**Respuesta:**
**Proceso de addNode()**:

1. **Creación del nodo**:
   ```cpp
   Node* newNode = new Node(x, y);
   ```

2. **Conexión al final**:
   ```cpp
   if (tail != nullptr) {
       tail->next = newNode; // 1. Vincula el último al nuevo
       tail = newNode;       // 2. Actualiza tail
   }
   ```

**Análisis de rendimiento**:
- **Con tail pointer**: O(1) (constante)
- **Sin tail pointer**: O(n) (lineal, hay que recorrer)

**Evolución en memoria**:
```
Estado inicial: H → N1 → N2 → T
Operación: addNode(30,40)
Paso 1: new Node(30,40) creado en memoria
Paso 2: T->next = nuevoNodo
Paso 3: T = nuevoNodo
Estado final: H → N1 → N2 → N3 → T
```

### Pregunta 10: Analiza una situación en la que utilizar una lista enlazada sería más ventajoso que utilizar un arreglo. Justifica tu respuesta considerando la gestión de memoria y las operaciones de inserción y eliminación.

**Respuesta:**
**Caso ideal**: Editor de texto con operaciones de undo/redo

**Ventajas**:
1. **Inserción/eliminación frecuente**:
   - Cada tecla presionada es una inserción
   - Cada borrado es una eliminación
   - En listas: O(1) para operaciones locales

2. **Memoria dinámica**:
   - El texto puede crecer indefinidamente
   - No hay problemas de capacidad predefinida

3. **Eficiencia en memoria**:
   - Solo usa memoria para caracteres existentes
   - No hay desperdicio por capacidad no usada

**Ejemplo comparativo**:
```cpp
// Con lista
editorTexto.insertarCaracter('A'); // Inserción rápida
editorTexto.borrarCaracter();     // Eliminación rápida

// Con arreglo
editorTexto.insertarCaracter('A'); // Posible reubicación
editorTexto.borrarCaracter();     // Desplazamiento de elementos
```

### Pregunta 11: Después de estudiar el manejo de memoria en listas enlazadas, ¿Cómo aplicarías este conocimiento para diseñar una estructura de datos personalizada para una aplicación creativa? ¿Qué aspectos considerarías para asegurar la eficiencia y evitar fugas de memoria?

**Respuesta:**
**Diseño para sistema de partículas**:

1. **Estructura propuesta**:
   ```cpp
   class Particula {
       ofVec2f posicion;
       ofColor color;
       float vida;
       Particula* next;
   };

   class SistemaParticulas {
       Particula* head;
       Particula* freeList; // Pool de objetos
   };
   ```

2. **Buenas prácticas**:
   - **Object Pooling**: Reutilizar nodos eliminados
   - **Smart Pointers**: unique_ptr para gestión automática
   - **Límites**: Máximo de partículas activas
   - **RAII**: Adquirir recursos en constructores

3. **Optimizaciones**:
   ```cpp
   void update() {
       Particula* curr = head;
       while(curr) {
           curr->vida--;
           if(curr->vida <= 0) {
               // Mover a freeList en lugar de delete
               recycleParticula(curr);
           }
           curr = curr->next;
       }
   }
   ```

### Pregunta 12: Reflexiona sobre las diferencias en la gestión de memoria entre C++ y un lenguaje con recolección de basura automática como C#. ¿Qué ventajas y desafíos encuentras en la gestión explícita de memoria en C++ al trabajar con estructuras de datos?

**Respuesta:**
**Comparativa C++ vs C#**:

| Aspecto               | C++ (Manual)               | C# (GC Automático)         |
|-----------------------|----------------------------|----------------------------|
| Control               | Precisión absoluta         | Limitado por GC            |
| Rendimiento           | Mayor eficiencia           | Overhead por GC            |
| Complejidad           | Mayor riesgo de errores    | Más seguro                 |
| Determinismo          | Liberación inmediata       | Liberación no determinista |
| Uso de recursos       | Más eficiente              | Mayor consumo              |

**Ventajas C++**:
- Control fino sobre memoria
- Mejor rendimiento en tiempo real
- Predictibilidad en aplicaciones críticas

**Desafíos C++**:
- Mayor complejidad de código
- Riesgo de memory leaks/dangling pointers
- Requiere disciplina del programador

### Pregunta 13: Imagina que estás optimizando una pieza de arte generativo que usa listas enlazadas para representar elementos en movimiento. ¿Qué consideraciones tomarías en cuenta para garantizar que la gestión de la memoria sea eficiente y que no ocurran fugas de memoria?

**Respuesta:**
**Estrategias de optimización**:

1. **Object Pooling**:
   - Pre-asignar nodos al inicio
   - Reutilizar nodos "muertos"
   - Evitar new/delete frecuentes

2. **Límites inteligentes**:
   ```cpp
   const int MAX_ELEMENTOS = 1000;
   if(contadorElementos >= MAX_ELEMENTOS) {
       reutilizarElementoMasAntiguo();
   }
   ```

3. **Herramientas de diagnóstico**:
   - Valgrind para detectar leaks
   - Profilers para análisis de memoria
   - Contadores de instancias

4. **Técnicas avanzadas**:
   ```cpp
   // Ejemplo: Allocator personalizado
   template<typename T>
   class PoolAllocator {
       // Implementación para asignación eficiente
   };
   ```

5. **Patrón RAII**:
   ```cpp
   class NodoSeguro {
       unique_ptr<Nodo> next;
       // Destructor automático
   };
   ```

   ---

## Actividad 2: Pilas y Colas - Análisis Profundo

### Preguntas sobre Stack (Pila)

**Pregunta 1: ¿Cómo se gestiona la memoria en una implementación manual de un stack en C++?**

**Respuesta:**
En la implementación manual:
1. **Alocación**: 
   ```cpp
   void push(float x, float y) {
       Node* newNode = new Node(x, y); // Memoria en heap
       newNode->next = top;
       top = newNode;
   }
   ```
2. **Liberación**:
   ```cpp
   void pop() {
       if (top != nullptr) {
           Node* temp = top;
           top = top->next;
           delete temp; // Liberación explícita
       }
   }
   ```
3. **Riesgos**: Cada `new` debe tener su correspondiente `delete` para evitar leaks.

**Pregunta 2: ¿Por qué es importante liberar la memoria al desapilar?**

**Respuesta:**
1. **Fugas de memoria**: Cada nodo no liberado permanece en heap
2. **Impacto**: En aplicaciones de larga duración puede consumir toda la memoria
3. **Ejemplo**: Un stack que procesa 1000 ops/sec perdería ~86MB/día si no libera

**Pregunta 3: std::stack vs implementación manual**

| Aspecto          | std::stack           | Implementación Manual |
|------------------|----------------------|-----------------------|
| Seguridad        | Mayor (usa RAII)     | Riesgo de errores     |
| Flexibilidad     | Limitada             | Total control         |
| Complejidad      | Baja                 | Alta                  |
| Rendimiento      | Optimizado           | Personalizable        |

**Pregunta 4: Naturaleza LIFO del stack**

**Implicaciones**:
- Útil para: 
  - Undo/redo operations
  - Recursión (call stack)
  - Evaluación de expresiones
- Limitación: Acceso solo al top

**Ejemplo artístico**:
```cpp
stack<Transformacion> transformStack;
transformStack.push(rotacion45); // Apilar
dibujarElemento();
transformStack.pop(); // Restaurar estado
```

### Preguntas sobre Queue (Cola)

**Pregunta 1: Gestión de memoria en colas**

**Implementación clave**:
```cpp
void enqueue(float x, float y) {
    Node* newNode = new Node(x, y); // Alocación
    if (rear == nullptr) {
        front = rear = newNode;
    } else {
        rear->next = newNode;
        rear = newNode;
    }
}

void dequeue() {
    if (front != nullptr) {
        Node* temp = front;
        front = front->next;
        delete temp; // Liberación
        if (front == nullptr) rear = nullptr;
    }
}
```

**Pregunta 2: Desafíos vs stack**

1. **Manejo de dos punteros** (front/rear vs solo top)
2. **Condición vacía más compleja**: 
   ```cpp
   bool isEmpty() {
       return front == nullptr && rear == nullptr;
   }
   ```
3. **Eliminación más costosa**: Requiere actualizar ambos extremos

**Pregunta 3: Ventajas FIFO**

Casos de uso ideales:
- Procesamiento de eventos
- Buffers de animación
- Sistemas de partículas por capas

**Ejemplo**:
```cpp
queue<ComandoAnimacion> animQueue;
animQueue.push(comando1); // Encolar
while(!animQueue.empty()) {
    procesar(animQueue.front());
    animQueue.pop(); // FIFO
}
```


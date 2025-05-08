## Actividad 1 y 2: Resultado del programa inicial

Al ejecutar el primer programa, se obtiene una ventana negra donde aparece un círculo blanco que sigue el movimiento del ratón. El círculo tiene un radio de 20 píxeles y se dibuja exactamente en la posición actual del cursor.

## Actividad 2: Análisis del código avanzado

### Archivo ofApp.h

En el archivo de cabecera (.h) incluimos:
1. Las declaraciones de las funciones principales (setup, update, draw)
2. Declaramos dos funciones adicionales para manejar eventos del ratón (mouseMoved y mousePressed)
3. Definimos dos variables privadas:
   - `particles`: un vector que almacena posiciones 2D (coordenadas x,y)
   - `particleColor`: un objeto que representa el color de las partículas

### Funcionamiento de la aplicación

La aplicación crea un rastro de círculos (partículas) que siguen el movimiento del ratón. Cuando haces clic, cambia aleatoriamente el color de todos los círculos. El rastro tiene un límite de 100 partículas, eliminando las más antiguas cuando se supera este número.

### Análisis detallado de las funciones

**mouseMoved(int x, int y):**
- Añade la posición actual del ratón (x,y) al vector de partículas
- Si el vector supera 100 elementos, elimina la partícula más antigua (la primera del vector)

**mousePressed(int x, int y, int button):**
- Cambia el color de las partículas a un color RGB aleatorio
- Cada componente (rojo, verde, azul) tiene un valor aleatorio entre 0 y 255

**setup():**
- Inicializa el fondo negro (ofBackground(0))
- Establece el color inicial de las partículas como blanco

**update():**
- En este código no hace nada, pero normalmente se usaría para actualizar lógica del programa antes de dibujar

**draw():**
- Recorre todas las partículas almacenadas en el vector
- Para cada partícula, establece el color actual y dibuja un círculo en su posición
- Los círculos tienen un radio fijo de 50 píxeles

---

## Actividad 3: Experimentación

### **1. Funciones Principales**
#### **`setup()`**
- **Propósito**: Configuración inicial de la aplicación.
- **Líneas de código**:
  ```cpp
  ofBackground(0);          // Establece el fondo en color negro (RGB: 0,0,0)
  particleColor = ofColor::white; // Inicializa el color de las partículas en blanco
  ```
- **Explicación**:
  - `ofBackground(0)` define el color de fondo de la ventana como negro.
  - `particleColor` se inicializa en blanco (`ofColor::white`).

#### **`update()`**
- **Propósito**: Actualizar lógica del programa (en este caso, no hace nada).
- **Líneas de código**:
  ```cpp
  // Vacío (podría usarse para actualizar física, animaciones, etc.)
  ```
- **Explicación**:
  - Normalmente aquí se actualizarían posiciones, velocidades o comportamientos, pero en este ejemplo no se usa.

#### **`draw()`**
- **Propósito**: Dibujar elementos en pantalla.
- **Líneas de código**:
  ```cpp
  for(auto &pos: particles) {  // Recorre todas las partículas almacenadas
      ofSetColor(particleColor); // Establece el color actual
      ofDrawCircle(pos.x, pos.y, 50); // Dibuja un círculo en (x,y) con radio 50
  }
  ```
- **Explicación**:
  - `for(auto &pos: particles)` itera sobre cada posición guardada en el vector `particles`.
  - `ofSetColor(particleColor)` define el color de dibujo.
  - `ofDrawCircle(pos.x, pos.y, 50)` dibuja un círculo en la posición `(x,y)` con radio `50`.

---

### **2. Funciones de Eventos del Ratón**
#### **`mouseMoved(int x, int y)`**
- **Propósito**: Añadir una nueva partícula cuando el ratón se mueve.
- **Líneas de código**:
  ```cpp
  particles.push_back(ofVec2f(x, y)); // Añade la posición actual del ratón al vector
  if (particles.size() > 100) {       // Si hay más de 100 partículas...
      particles.erase(particles.begin()); // Elimina la más antigua (primera del vector)
  }
  ```
- **Explicación**:
  - `particles.push_back(ofVec2f(x, y))` guarda la posición actual del ratón como una nueva partícula.
  - `if (particles.size() > 100)` asegura que no haya más de 100 partículas.
  - `particles.erase(particles.begin())` borra la partícula más antigua (FIFO: First In, First Out).

#### **`mousePressed(int x, int y, int button)`**
- **Propósito**: Cambiar el color de las partículas al hacer clic.
- **Líneas de código**:
  ```cpp
  particleColor = ofColor(ofRandom(255), ofRandom(255), ofRandom(255));
  ```
- **Explicación**:
  - `ofRandom(255)` genera un valor aleatorio entre `0` y `255` para cada componente RGB.
  - `ofColor(R, G, B)` crea un nuevo color con esos valores aleatorios.

---

## **Experimento: Modificando el Código**
### **Cambio 1: Aumentar el Tamaño Máximo de Partículas**
- **Modificación**:
  ```cpp
  if (particles.size() > 200) {  // Cambiamos de 100 a 200
      particles.erase(particles.begin());
  }
  ```
- **Resultado**: El rastro de partículas será más largo antes de borrar las más antiguas.

---

### **Cambio 2: Hacer que las Partículas Cambien de Tamaño**
- **Modificación en `draw()`**:
  ```cpp
  for(auto &pos: particles) {
      ofSetColor(particleColor);
      float radius = ofRandom(10, 60); // Radio aleatorio entre 10 y 60
      ofDrawCircle(pos.x, pos.y, radius);
  }
  ```
- **Resultado**: Los círculos tendrán tamaños aleatorios, creando un efecto más dinámico.

---

### **Cambio 3: Cambiar el Fondo al Mover el Ratón**
- **Modificación en `mouseMoved`**:
  ```cpp
  void ofApp::mouseMoved(int x, int y) {
      particles.push_back(ofVec2f(x, y));
      if (particles.size() > 100) {
          particles.erase(particles.begin());
      }
      // Añadir esto:
      ofBackground(ofRandom(50), ofRandom(50), ofRandom(50)); // Fondo oscuro aleatorio
  }
  ```
- **Resultado**: El fondo cambiará a un color oscuro aleatorio al mover el ratón.

---

## Actividad 4

### **Objetivo**  
Entender conceptos clave de programación en C++ aplicables a alto nivel, sin profundizar en el lenguaje mismo.  

### **Videos Recomendados** (Playlist de Mike Shah)  
1. **"Introduction to C++"** → Sintaxis básica, variables, tipos de datos.  
2. **"Functions in C++"** → Cómo se estructuran las funciones (similar a `setup()`, `draw()` en OpenFrameworks).  
3. **"Classes and Objects"** → Base para entender cómo OpenFrameworks usa clases (ej: `ofApp`).  

### **Cosas importantes a tener en cuenta**  
- **Sintaxis**: Compara cómo se escriben bucles (`for`, `while`) y condicionales (`if`) con otros lenguajes.  
- **Funciones**: Nota cómo se declaran y usan (similar a lo visto en `ofApp.cpp`).  
- **Clases**: Relación con la estructura de proyectos en OpenFrameworks (herencia de `ofBaseApp`).  

---

## Actividad 5


```cpp
#pragma once

#include "ofMain.h"

class Sphere {
public:
    Sphere(float x, float y, float radius);
    void draw();
    void update(float x, float y);
    float getX();
    float getY();
    float getRadius();

private:
    float x, y;
    float radius;
    ofColor color;
};

class ofApp : public ofBaseApp{

    public:
        void setup();
        void update();
        void draw();

        void mouseMoved(int x, int y );
        void mousePressed(int x, int y, int button);

    private:

        vector<Sphere*> spheres;
        Sphere* selectedSphere;
};
```



```cpp
#include "ofApp.h"

Sphere::Sphere(float x, float y, float radius) : x(x), y(y), radius(radius) {
    color = ofColor(ofRandom(255), ofRandom(255), ofRandom(255));
}

void Sphere::draw() {
    ofSetColor(color);
    ofDrawCircle(x, y, radius);
}

void Sphere::update(float x, float y) {
    this->x = x;
    this->y = y;
}

float Sphere::getRadius() {
    return radius;
}

float Sphere::getX() {
    return x;
}

float Sphere::getY() {
    return y;
}

//--------------------------------------------------------------
void ofApp::setup(){
    ofBackground(0);

    for (int i = 0; i < 5; i++) {
        float x = ofRandomWidth();
        float y = ofRandomHeight();
        float radius = ofRandom(20, 50);
        spheres.push_back(new Sphere(x, y, radius));
    }
    selectedSphere = nullptr;

}

//--------------------------------------------------------------
void ofApp::update(){
    if (selectedSphere != nullptr) {
        selectedSphere->update(ofGetMouseX(), ofGetMouseY());
    }
}

//--------------------------------------------------------------
void ofApp::draw(){
    for (auto sphere : spheres) {
        sphere->draw();
    }
}

//--------------------------------------------------------------
void ofApp::mouseMoved(int x, int y ){

}

//--------------------------------------------------------------
void ofApp::mousePressed(int x, int y, int button){

    if(button == OF_MOUSE_BUTTON_LEFT){
        for (auto sphere : spheres) {
            float distance = ofDist(x, y, sphere->getX(), sphere->getY());
            if (distance < sphere->getRadius()) {

                selectedSphere = sphere;
                break;
            }
        }
    }
}
```

# **Análisis de Punteros en el Código**

### **1. Definición de un puntero**  
Un puntero es una variable que **almacena la dirección de memoria** de otra variable u objeto, en lugar de su valor directo.

---

### **2. Dónde está el puntero en el código**  
Hay dos punteros clave:  
- `vector<Sphere*> spheres`: Un vector de punteros a objetos `Sphere`.  
- `Sphere* selectedSphere`: Puntero que guarda la esfera seleccionada (o `nullptr` si ninguna está seleccionada).  

---

### **3. Inicialización del puntero**  
- En `setup()`:  
  ```cpp
  spheres.push_back(new Sphere(x, y, radius)); // new devuelve un puntero
  selectedSphere = nullptr; // Inicializado como "nulo"
  ```
  - `new Sphere()` crea un objeto en memoria dinámica y devuelve su dirección.  
  - `nullptr` indica que no apunta a ningún objeto.  

---

### **4. Uso del puntero**  
- **Almacenamiento**: `vector<Sphere*>` guarda múltiples esferas sin copiarlas.  
- **Selección**: `selectedSphere` apunta a la esfera clickeada (en `mousePressed`).  
- **Modificación**: `selectedSphere->update()` mueve la esfera seleccionada (en `update`).  

---

### **5. ¿Qué almacena el puntero?**  
- **`spheres[i]`**: Dirección de memoria de una esfera creada con `new`.  
- **`selectedSphere`**:  
  - `nullptr` si no hay selección.  
  - Dirección de la esfera clickeada (cuando `distance < radius`).  


**Punteros = direcciones de memoria → eficiencia y manipulación directa.

---

## Actividad 6
### **Error en el código** (para corregir en la siguiente actividad)  
- **Problema**: No se deselecciona la esfera. Si se hace clic fuera de cualquier esfera, `selectedSphere` sigue apuntando a la última seleccionada.  
- **Solución posible**: Añadir en `mousePressed`:  
  ```cpp
  if (button == OF_MOUSE_BUTTON_LEFT) {
      selectedSphere = nullptr; // Resetear primero
      for (auto sphere : spheres) {
          // ... lógica de selección ...
      }
  }
  ```

## Actividad 7

```cpp
#pragma once

#include "ofMain.h"

class Sphere {
public:
    Sphere(float x, float y, float radius);
    void draw() const;

    float x, y;
    float radius;
    ofColor color;
};

class ofApp : public ofBaseApp {
public:
    void setup();
    void update();
    void draw();

    void keyPressed(int key);

private:
    std::vector<Sphere*> globalVector;
    void createObjectInStack();
};
```

Y el archivo `ofApp.cpp` así:

```cpp
#include "ofApp.h"

Sphere::Sphere(float x, float y, float radius) : x(x), y(y), radius(radius) {
    color = ofColor(ofRandom(255), ofRandom(255), ofRandom(255));
}

void Sphere::draw() const {
    ofSetColor(color);
    ofDrawCircle(x, y, radius);
}

void ofApp::setup() {
    ofBackground(0);
}

void ofApp::update() {
}

void ofApp::draw() {
    ofSetColor(255);
    for (Sphere* sphere : globalVector) {
        if (sphere != nullptr) {
            ofDrawBitmapString("Objects pointed: " + ofToString(globalVector.size()), 20, 20);
            ofDrawBitmapString("Attempting to draw stored object...", 20, 40);
            ofDrawBitmapString("Stored Object Position: " + ofToString(sphere->x) + ", " + ofToString(sphere->y), 20, 60);
            sphere->draw();
        }
    }
}

void ofApp::keyPressed(int key) {
    if (key == 'c') {
        if (globalVector.empty()) {
            createObjectInStack();
        }
    }
    else if (key == 'd') {
        if (!globalVector.empty()) {
            ofLog() << "Accessing object in global vector: Position (" << globalVector[0]->x << ", " << globalVector[0]->y << ")";
        }
        else {
            ofLog() << "No objects in the global vector.";
        }
    }
}

void ofApp::createObjectInStack() {
    Sphere localSphere(ofRandomWidth(), ofRandomHeight(), 30);
    globalVector.push_back(&localSphere);
    ofLog() << "Object created in stack: Position (" << localSphere.x << ", " << localSphere.y << ")";
    localSphere.draw();
}
```


# **Análisis del Manejo de Memoria en el Programa**

## **1. Comportamiento Original (Objeto en Stack)**
Cuando se presiona **'c'** en la versión original:
1. **`createObjectInStack()`** crea un objeto `localSphere` en el **stack** (memoria automática).
2. Se guarda su dirección en `globalVector` con `push_back(&localSphere)`.
3. **Problema**: Cuando la función termina, `localSphere` se destruye (es variable local), pero `globalVector` sigue apuntando a esa dirección inválida.
4. **Resultado**:  
   - El programa intenta dibujar un objeto que ya no existe → **comportamiento indefinido** (puede crashear o mostrar datos corruptos).  
   - En `draw()`, `sphere->draw()` accede a memoria liberada.

---

## **2. Comportamiento Modificado (Objeto en Heap)**
Con la modificación que usa `new`:
1. **`createObjectInStack()`** crea un objeto en el **heap** (memoria dinámica) con `new Sphere()`.
2. Su dirección se guarda en `globalVector` (ahora es válida después de que la función termine).
3. **Resultado**:  
   - El objeto persiste en memoria hasta que se libere con `delete`.  
   - `draw()` puede acceder correctamente al objeto.  
   - **Nuevo problema**: Si no se libera la memoria (con `delete`), habrá **memory leaks**.

---

## **¿Por qué ocurre esto?**
- **Stack**: Memoria automática para variables locales. Se libera al salir de la función.  
- **Heap**: Memoria dinámica que persiste hasta que se libere explícitamente (`delete`).  
- **Error original**:  
  - Guardar un puntero a un objeto del stack en un vector global es peligroso, porque el objeto se destruye, pero el puntero sigue apuntando a esa dirección.  

## **Solución Correcta**
1. **Usar heap** (como en la modificación) para objetos que deben persistir.  
2. **Liberar memoria** cuando ya no se necesite (ej: en el destructor de `ofApp`):  
   ```cpp
   ~ofApp() {
       for (Sphere* sphere : globalVector) {
           delete sphere; // Liberar cada objeto
       }
   }
   ```
3. Alternativa mejor: Usar **smart pointers** (`std::unique_ptr`) para manejo automático de memoria.

**Qué debo tener en cuenta?**:  
- El stack es para datos temporales.  
- El heap es para datos persistentes, pero requiere gestión manual.  
- **Nunca se guardan punteros a variables locales en estructuras globales.**  

---

## Actividad 8

# **Experimento: Comparación de Almacenamiento de Objetos en Memoria**

## **Programa Demo (OpenFrameworks)**
```cpp
// ofApp.h
#pragma once
#include "ofMain.h"

class GameObject {
public:
    GameObject(float x, float y, string type);
    void draw();
    string getType();

private:
    float x, y;
    string memoryType; // "heap", "stack" o "global"
};

// ofApp.cpp
#include "ofApp.h"

// --- Objeto en MEMORIA GLOBAL (dura todo el programa) ---
GameObject globalObj(100, 100, "global");

GameObject::GameObject(float x, float y, string type) 
    : x(x), y(y), memoryType(type) {}

void GameObject::draw() {
    ofSetColor(255);
    ofDrawCircle(x, y, 30);
    ofDrawBitmapString(memoryType, x - 20, y + 40);
}

//--------------------------------------------------------------
void ofApp::setup() {
    ofBackground(0);
}

//--------------------------------------------------------------
void ofApp::update() {}

//--------------------------------------------------------------
void ofApp::draw() {
    // Dibujar objeto global (siempre disponible)
    globalObj.draw();

    // Dibujar objeto en heap (si existe)
    if (heapObj != nullptr) {
        heapObj->draw();
    }

    // Dibujar objeto en stack (solo si createStackObject() está activo)
    if (drawStackObj) {
        stackObj.draw();
    }
}

//--------------------------------------------------------------
void ofApp::keyPressed(int key) {
    if (key == 'h') { // Crear en HEAP
        heapObj = new GameObject(ofRandomWidth(), ofRandomHeight(), "heap");
    }
    else if (key == 's') { // Crear en STACK (temporal)
        createStackObject();
    }
    else if (key == 'd') { // Liberar memoria del heap
        delete heapObj;
        heapObj = nullptr;
    }
}

// Función que crea un objeto en el stack (solo vive durante esta función)
void ofApp::createStackObject() {
    GameObject stackObj(ofRandomWidth(), ofRandomHeight(), "stack");
    this->stackObj = stackObj; // Copia (no recomendado para objetos complejos)
    drawStackObj = true;
}
```

---

## **Resultados del Experimento**
| **Memoria** | **Creación**                | **Duración**                     | **¿Cuándo usarla?**                     |
|-------------|-----------------------------|----------------------------------|-----------------------------------------|
| **Heap**    | `new GameObject()`          | Hasta que se llame a `delete`    | Objetos dinámicos que deben persistir.  |
| **Stack**   | `GameObject obj(...)`       | Solo durante la función actual   | Objetos temporales (cálculos rápidos). |
| **Global**  | Fuera de cualquier función  | Toda la ejecución del programa   | Recursos compartidos (ej: configuración). |

---

## **¿Cuándo usar cada una?**
1. **Heap (`new`/`delete`)**  
   - Cuando necesitas que el objeto **viva más allá de una función**.  
   - Ej: Partículas en un juego, elementos de UI dinámicos.  
   - ✅ **Ventaja**: Control preciso de la vida útil.  
   - ❌ **Riesgo**: Memory leaks si no se libera con `delete`.  

2. **Stack (variables locales)**  
   - Para objetos **temporales** (ej: cálculos intermedios).  
   - Ej: Transformaciones matemáticas en un frame.  
   - ✅ **Ventaja**: Automáticamente liberados.  
   - ❌ **Riesgo**: No puedes retenerlos fuera de su función.  

3. **Memoria Global**  
   - Para datos **compartidos** que deben existir siempre.  
   - Ej: Configuración del juego, instancias únicas (Singleton).  
   - ✅ **Ventaja**: Accesible desde cualquier lugar.  
   - ❌ **Riesgo**: Dificulta pruebas y modularidad.  

---

## **funcionalidad*  
- **Usa heap** para objetos dinámicos con vida larga.  
- **Usa stack** para datos temporales (eficiencia).  
- **Usa global** solo para recursos verdaderamente globales.  

**Prueba**:  
- Presiona **'h'** para crear en heap (persiste hasta que pulses **'d'**).  
- Presiona **'s'** para crear en stack (solo se dibuja una vez).  
- El objeto global siempre está visible.

---

## Actividad 9

Cuando presionas la tecla **“f”**, el programa ejecuta el siguiente bloque de código:

```cpp
if(!heapObjects.empty()) {
    delete heapObjects.back();
    heapObjects.pop_back();
}
```

Este bloque realiza lo siguiente **paso a paso**:

---

#### 1. `if(!heapObjects.empty())`
- Verifica si el vector `heapObjects` **no está vacío**, es decir, si hay al menos un puntero a un `ofVec2f` en la lista.
- Esta condición evita errores por intentar eliminar un objeto de un vector vacío.

#### 2. `delete heapObjects.back();`
- **Libera la memoria** en el *heap* del último objeto creado con `new ofVec2f(x, y)`.
- Es decir, destruye el objeto apuntado por el último puntero en el vector.
- Esto es necesario porque en `mousePressed` se crearon objetos con `new`, y por lo tanto la memoria **no se libera automáticamente**.

#### 3. `heapObjects.pop_back();`
- Elimina el **último puntero** del vector `heapObjects`, una vez que la memoria del objeto ya fue liberada.
- Así, no queda un puntero colgando (dangling pointer) dentro del vector.

---

### ¿Qué efecto tiene presionar “f”?
- **Borra el último círculo azul** que aparece en pantalla (el más recientemente creado con el mouse).
- **Libera su memoria**, evitando fugas de memoria (memory leaks).
- En resumen, presionar "f" borra un objeto del heap de forma segura.










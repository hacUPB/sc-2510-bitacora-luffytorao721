## Tres claves importantes 
- Vertex shading
  - renderizar solo lo que se ve en el viewport de la camara, osea llevar un objeto 3D a un espacio 2D siguiendo estos pasos (Model space, world space, camera space, view Screen), los datos de treansformaciones, materiales y vertices de los objetos que se ven en la
    view screen son pasados a la GPU para que renderice la escena, por ejemplo las de hoy en dia pueden renderizar
  - 120 frames por segundo.
- rasterization
  -Al momento de pasar todos los vertices a un plano 2D, ahora se deben tomar los 3 vertices de esos triangulos que se forman en la geometría y dar información de cada pixel dentro de ese triangulo
  con el fin de ser primordialmente desplegadas en un medio digital; por ejemplo monitor de una computadora, impresora e entre otras posibilidades.
  ### Resterizar imagen
  - Generalmente una imagen descrita con dicho proceso se encuentra compuesta por finos puntos denominados píxeles, es interesante saber que contienen un número fijo de píxeles, o sea que entre sus desventajas mayores se distingue la pérdida de calidad cuando se requiere alargar o quizás transformar la imagen.

 
- Fragment shading

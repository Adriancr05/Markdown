# Guión Realidad Aumentada  
  
## Con marcadores: Quiver  
* **Qué hace:** colorear una lámina impresa y escanearla, y el dibujo cobra vida en 3D  
* **Por qué es de marcadores:**  
    * Busca un patrón de imagen conocido en cada fotograma  
    * Sin la lámina no hay anclaje  
* **Ventaja:** muy fiable  
* **Límite:** depende de material físico concreto  
  
## Por localización: PeakVisor  
* **Qué hace:** muestra nombre y altitud de las cimas (por ejemplo, desde el Teide)  
* **Por qué es por localización:**  
    * Posición por GPS  
    * Orientación por brújula, acelerómetro y giroscopio  
    * Contenidos georreferenciados  
    * La cámara solo sirve de fondo  
* **Límite:** precisión de los sensores  
  
## Sin marcadores: Medida (Apple)  
* **Qué hace:** mide objetos reales con la cámara  
* **Por qué es sin marcadores:**  
    * Sin patrón previo  
    * SLAM para mapear el entorno  
    * VIO para seguir el movimiento  
    * Detección de planos y anclaje de puntos por el usuario  
* **Mejora:** con LiDAR es más rápida y precisa  
* **Límite:** poca luz o superficies lisas  
  
## Híbrida: Google Maps Live View  
* **Qué hace:** flechas de navegación sobre la calle real  
* **Por qué es híbrida:**  
    * GPS para la posición aproximada, que falla en ciudad  
    * Posicionamiento visual con Street View para afinar  
    * SLAM para mantener las flechas estable**s**  
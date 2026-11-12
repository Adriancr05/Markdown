# Examen  
  
## Pregunta 1: análisis y corrección del código  
## Errores clave  
**Script 1: CubeCollision (emisor)**  

| # | Error | Por qué es un problema |
| - | -------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1 | public CollisionAction OnCubeHitCylinder;sin event | Es un simple delegado público. Cualquier script externo podría invocarlo o sobrescribirlo con =, borrando a los demás suscriptores. Debe ser public event. |
| 2 | Se invoca OnCubeHitCylinder();sin comprobar null | Si nadie está suscrito, lanza NullReferenceException. Se usa ?.Invoke(). |
| 3 | Usa OnTriggerEnterpara una colisión física | El enunciado habla de colisión. OnTriggerEnter solo se dispara si uno de los colliders tiene Is Trigger activado. Con colliders normales no se ejecuta nunca. Lo coherente es OnCollisionEnter, que además necesita un Rigidbody en al menos uno de los dos objetos. |
| 4 | Compara con el nombre "Cilindro" | Es frágil: si se renombra el objeto, deja de funcionar. Es mejor una etiqueta (CompareTag). |
  
**Script 2: RedSphere (Tipo 1)**  

| # | Error | Por qué es un problema |
| - | ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 5 | FindObjectOfType<CubeCollision>() en cada esfera | Está obsoleto en las versiones recientes de Unity y es lento. Devuelve null si no existe y rompe en la línea siguiente. Con un evento static ya no hace falta buscar nada. |
| 6 | No se desuscribe | Si la esfera se destruye, el evento mantiene una referencia a un objeto muerto y da MissingReferenceException. Falta -= en OnDestroy u OnDisable. |
| 7 | Movimiento en FixedUpdate con Time.deltaTime | Mezcla dos ciclos distintos. Sin Rigidbody, el movimiento por transform va en Update con Time.deltaTime. Con Rigidbody, iría en FixedUpdate con MovePosition y Time.fixedDeltaTime. |
| 8 | No comprueba targetGreenSphere != null | Si se olvida asignarlo en el Inspector, da NullReferenceException en cada frame. |
  
**Script 3: GreenSphere (Tipo 2)**  

| # | Error | Por qué es un problema |
| -- | ------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 9 | += StartMoving(); con paréntesis | Es un error de compilación. Con paréntesis se llama al método, que devuelve void, y no se le pasa como referencia. Debe ser += StartMoving;. |
| 10 | StartMoving teletransporta(transform.position = cylinderTarget.position) | El enunciado dice que las esferas verdes se desplazan hacia el cilindro. Eso es movimiento continuo, y el teletransporte pertenece al ejercicio 4. El nombre StartMoving tampoco coincide con lo que hace. |
| 11 | Mismos fallos 5 y 6: FindObjectOfType y falta de desuscripción | Ya explicados arriba. |
| 12 | Falta speed y la bandera move | Sin ellos no se puede hacer un desplazamiento progresivo. |
  
**Fallo general**  
Ningún script tiene using UnityEngine;, así que no compilan.  
## Solución corregida  
Uso un **evento estático** (como en tu práctica), así las esferas no necesitan buscar ni referenciar al cubo.  
**Script 1: CubeCollision.cs** (en el Cubo)  
```
using UnityEngine;

public class CubeCollision : MonoBehaviour
{
    public delegate void CollisionAction();
    public static event CollisionAction OnCubeHitCylinder;

    private void OnCollisionEnter(Collision collision)
    {
        if (collision.gameObject.CompareTag("Cylinder"))
        {
            OnCubeHitCylinder?.Invoke();
        }
    }
}
```
**Script 2: RedSphere.cs** (esferas Tipo 1)  
```
using UnityEngine;

public class RedSphere : MonoBehaviour
{
    public Transform targetGreenSphere;   // la esfera verde fijada de antemano
    public float speed = 5f;
    private bool move = false;

    private void OnEnable()  { CubeCollision.OnCubeHitCylinder += EnableMovement; }
    private void OnDisable() { CubeCollision.OnCubeHitCylinder -= EnableMovement; }

    private void EnableMovement()
    {
        move = true;
    }

    private void Update()
    {
        if (move && targetGreenSphere != null)
        {
            transform.position = Vector3.MoveTowards(
                transform.position,
                targetGreenSphere.position,
                speed * Time.deltaTime);
        }
    }
}
```
**Script 3: GreenSphere.cs** (esferas Tipo 2)  
```
using UnityEngine;

public class GreenSphere : MonoBehaviour
{
    public Transform cylinderTarget;
    public float speed = 5f;
    private bool move = false;

    private void OnEnable()  { CubeCollision.OnCubeHitCylinder += EnableMovement; }
    private void OnDisable() { CubeCollision.OnCubeHitCylinder -= EnableMovement; }

    private void EnableMovement()
    {
        move = true;
    }

    private void Update()
    {
        if (move && cylinderTarget != null)
        {
            transform.position = Vector3.MoveTowards(
                transform.position,
                cylinderTarget.position,
                speed * Time.deltaTime);
        }
    }
}
```
## Configuración en la escena  
* Crea la etiqueta Cylinder y asígnala al cilindro.  
* El cubo y el cilindro tienen colliders **sin** *Is Trigger*, y el cubo lleva un Rigidbody.  
* En cada esfera roja, arrastra al Inspector la esfera verde elegida en targetGreenSphere.  
* En cada esfera verde, arrastra el cilindro en cylinderTarget.  
## Idea para explicar en el examen  
El evento solo **cambia un estado** (move = true). El desplazamiento continuo sigue en Update, porque el evento ocurre en un instante y el movimiento dura varios frames.  
¿Quieres que pase a la pregunta 2 (pseudocódigo de físicas) o a la 3 (el prompt para la IA)?  
  
## Pregunta 2: pseudocódigo de físicas y eventos  
## Decisiones de diseño (justifícalas en el examen)  

| Decisión | Razón |
| ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| El sensor es un collider normal(sin Is Trigger) | El enunciado dice "impacta por física" y "sensor por contacto". Un trigger no genera datos de impacto. |
| Se usa OnCollisionEnter | Es la única que da el objeto Collision, con la información de fuerza del impacto. |
| Intensidad del impacto = collision.impulse.magnitude | Mide el impulso real del choque, así que depende de la velocidad y de la masa de la esfera azul. Alternativa: relativeVelocity.magnitude. |
| El evento lleva un parámetro float | Las rojas necesitan saber cuánto fue el impacto, no solo que ocurrió. |
| Las rojas aplican AddForce(..., ForceMode.Impulse) | Es un salto instantáneo y se ejecuta en el ciclo de físicas. |
| Evento static | Las rojas no necesitan referencia al sensor. |
  
**Pseudocódigo**  
## Componentes de la escena  
```
ESCENA:
    Suelo              (Collider)
    Sensor             (Collider NO trigger, script ImpactSensor)
    4 esferas rojas    (Tag "Red",  Rigidbody, script RedJumper)
    2 esferas azules   (Tag "Blue", Rigidbody)
```
## Notificador: ImpactSensor  
```
CLASE ImpactSensor

    DELEGADO void ImpactDelegate(float intensidad)
    EVENTO ESTÁTICO OnBlueImpact DE TIPO ImpactDelegate

    // Unity llama a este método en el ciclo de físicas
    // cuando algo choca con el sensor
    MÉTODO OnCollisionEnter(colisión):

        SI colisión.objeto TIENE TAG "Blue" ENTONCES
            intensidad ← magnitud(colisión.impulso)
            SI OnBlueImpact TIENE suscriptores ENTONCES
                LANZAR OnBlueImpact(intensidad)
            FIN SI
        FIN SI
```
## Observador: RedJumper  
```
CLASE RedJumper

    VARIABLES:
        rb              // Rigidbody de la esfera
        factorSalto ← 1.5      // constante de proporcionalidad k
        fuerzaMáxima ← 20      // límite para evitar saltos exagerados

    MÉTODO Awake():
        rb ← obtener Rigidbody de este objeto

    MÉTODO OnEnable():
        SUSCRIBIR Jump a ImpactSensor.OnBlueImpact

    MÉTODO OnDisable():
        DESUSCRIBIR Jump de ImpactSensor.OnBlueImpact

    // Función de respuesta (callback)
    MÉTODO Jump(intensidad):

        fuerza ← intensidad × factorSalto        // proporcionalidad directa
        fuerza ← mínimo(fuerza, fuerzaMáxima)    // limitar

        APLICAR a rb una fuerza (0, fuerza, 0) como IMPULSO
```
## Flujo completo  
```
Esfera azul cae y choca con el sensor
   → Motor de físicas detecta el contacto
   → ImpactSensor.OnCollisionEnter(colisión)
   → Comprueba Tag "Blue" y calcula intensidad
   → LANZA OnBlueImpact(intensidad)
   → Las 4 RedJumper reciben el aviso
   → Cada una calcula fuerza = intensidad × k
   → Cada una se impulsa hacia arriba
```
## Puntos que el profesor suele valorar  
1. **Proporcionalidad:** a mayor impacto de la azul, mayor salto de las rojas. Se logra pasando la intensidad como parámetro del evento y multiplicándola por una constante.  
2. **Desacoplamiento:** el sensor no conoce las rojas, y las rojas no conocen a las azules. Solo comparten el evento.  
3. **Filtrado por Tag:** el sensor ignora las esferas que no sean azules. Así, si una roja cae sobre el sensor, no se lanza nada.  
4. **Ciclo correcto:** el cálculo se hace en OnCollisionEnter y AddForce se aplica en el ciclo de físicas, sin usar Update.  
5. **Desuscripción** en OnDisable, para evitar llamadas a objetos destruidos.  
6. **Requisitos de física:** para que haya colisión, la esfera azul necesita Rigidbody. Las rojas también lo necesitan para poder recibir la fuerza.  
## Mejoras opcionales (suman puntos si las mencionas)  
* **Comprobar que la roja está en el suelo** antes de saltar, para que no se impulse si ya está en el aire:SI esferaEnSuelo ENTONCES aplicar impulso  
*   
* **Umbral mínimo de impacto:** ignorar choques muy suaves para que roces leves no activen el salto.SI intensidad < umbralMínimo ENTONCES no hacer nada  
*   
* **Pasar más datos** en el evento (por ejemplo, la posición del impacto) si hiciera falta, ampliando la firma del delegado.  
¿Pasamos a la pregunta 3 (el prompt para la IA)?  
  
## Pregunta 3: prompt técnico para la IA  
## Enfoque  
El enunciado pide que el prompt **no explique qué problema de juego resolver, sino CÓMO programarlo**. Por eso el contexto de la escena se reduce a una frase, y casi todo el texto son requisitos técnicos: patrón, físicas, ciclo de vida de Unity, buenas prácticas y formato de entrega.  
## El prompt  
```
Actúa como un desarrollador senior de Unity (C#, Unity 2022 LTS o superior).
Genera el código completo de una escena compuesta por un cubo, un cilindro,
esferas del grupo 1 y esferas del grupo 2. No te centres en la lógica del juego:
céntrate en que la arquitectura sea técnicamente correcta, mantenible y
siga las buenas prácticas de Unity descritas a continuación.

## 1. Arquitectura y patrones
- Aplica el patrón Observador con delegados y eventos de C#.
- Debe haber un único notificador, que detecta el suceso y lo publica, y varios
  observadores, que solo implementan su función de respuesta (callback).
- Declara el delegado con `public delegate` y el evento con
  `public static event`, para que los observadores no necesiten referencias
  al notificador.
- Lanza el evento siempre con `?.Invoke()`.
- El notificador no debe conocer a los observadores, y los observadores no
  deben conocerse entre sí.
- Prohibido usar `FindObjectOfType`, `GameObject.Find`, `SendMessage` o
  comprobaciones por polling en `Update` para detectar el suceso.
- Evita duplicar código entre los dos grupos de esferas: crea una clase base
  abstracta para el comportamiento común y deja que cada subclase
  solo defina su destino o su respuesta.
- Una clase, una responsabilidad.

## 2. Ciclo de vida de las suscripciones
- Suscríbete en `OnEnable` y desuscríbete en `OnDisable`, para evitar
  referencias a objetos destruidos y fugas de memoria con eventos estáticos.
- El evento solo debe cambiar un estado (por ejemplo, una bandera). El movimiento
  continuo se ejecuta después en el ciclo correspondiente, nunca dentro del callback.

## 3. Físicas
- Detecta la colisión con `OnCollisionEnter`, con colliders NO trigger.
  Indica qué componentes y qué configuración necesita cada objeto
  (Rigidbody, isKinematic, Collider).
- El cubo debe ser un objeto físico con `Rigidbody`. Su input se lee en `Update`
  y su movimiento se aplica en `FixedUpdate` con `rb.MovePosition` y
  `Time.fixedDeltaTime`.
- Las esferas se mueven con `Rigidbody` cinemático desde `FixedUpdate`.
  No muevas por `transform.position` los objetos que tienen Rigidbody.
- Cachea los componentes en `Awake` y no llames a `GetComponent` en `Update`
  ni en `FixedUpdate`.
- Identifica los objetos con `CompareTag` y nunca con `gameObject.name`.

## 4. Buenas prácticas de código
- Campos configurables con `[SerializeField] private` en lugar de campos públicos.
- Usa `[RequireComponent(typeof(Rigidbody))]` donde sea necesario.
- Sin números mágicos: velocidades y distancias como campos serializados o constantes.
- Comprueba las referencias nulas del Inspector y avisa con `Debug.LogWarning`
  en lugar de dejar que se produzca un `NullReferenceException`.
- Nombres en inglés, PascalCase para métodos y camelCase para campos.
  Los eventos llevan el prefijo `On`.
- Cada clase en su propio archivo, con comentarios XML (`///`) en las clases
  y en los métodos públicos.
- Cada script lleva `using UnityEngine;` y solo los `using` que necesite.

## 5. Formato de la respuesta
1. Árbol de archivos con la lista de scripts.
2. Código completo de cada script, sin fragmentos ni "..." omitidos.
3. Tabla de configuración de la escena: qué componentes, tags y referencias
   de Inspector lleva cada GameObject.
4. Un párrafo final que justifique cada decisión de arquitectura y de físicas.
5. Antes de entregar, revisa tu propio código con esta lista y corrige lo que falle:
   suscripción simétrica, comprobación de null, ciclo de Update/FixedUpdate correcto,
   cero búsquedas en tiempo de ejecución, cero strings mágicos.
```
## Por qué funciona (para justificarlo en el examen)  

| Parte del prompt | Qué evalúa el enunciado |
| ---------------- | ---------------------------------------------------------- |
| Sección 1 | Patrones: Observador, desacoplamiento, no duplicar código |
| Sección 2 | Gestión del ciclo de vida de los eventos |
| Sección 3 | Físicas: Rigidbody, FixedUpdate, colisión frente a trigger |
| Sección 4 | Buenas prácticas de Unity |
| Sección 5 | Formato verificable y autorevisión de la IA |
  
Además, cada requisito del prompt corresponde a un error del código del simulacro:  

| Error del script original | Requisito que lo previene |
| -------------------------------- | ----------------------------------------- |
| FindObjectOfType | Prohibido buscar en tiempo de ejecución |
| Delegado sin event | Declarar static event |
| Sin comprobar null | ?.Invoke() |
| Sin desuscribirse | OnEnable / OnDisable |
| FixedUpdate con Time.deltaTime | Ciclo correcto y fixedDeltaTime |
| OnTriggerEnter para una colisión | OnCollisionEnter con colliders no trigger |
| name == "Cilindro" | CompareTag |
  
Esa correspondencia es un buen argumento si el profesor pregunta por qué has incluido cada punto.  
## Errores a evitar al redactarlo  
* **Describir la mecánica en detalle** ("las rojas van hacia la verde número 3..."). El enunciado lo prohíbe expresamente.  
* **Pedir "código limpio" sin concretar.** Una IA responde mucho mejor a requisitos medibles, como "OnEnable/OnDisable simétricos".  
* **Olvidar el formato de salida.** Sin él, la IA puede entregar fragmentos incompletos.  
Si quieres, puedo hacerte una versión resumida de este prompt (unas 10 líneas) por si el examen limita la extensión.  
# Mapa / Diccionario (Hash Map)

## 1. Qué es y cómo funciona

### Intuición

Si imaginamos una biblioteca donde el título de cada libro te dice directamente
en qué estante está. No recorrés nada, no comparás uno por uno — vas
directo. Un HashMap funciona así: le das una clave, y la estructura te
devuelve el valor asociado de forma inmediata.

La magia está en la **función hash**: toma la clave y produce siempre
la misma dirección.

**Problema que resuelve:** Cualquier situación donde necesitás encontrar
algo por su nombre, no por su posición. Dado un identificador arbitrario
— un nombre de usuario, una palabra, un código de producto — obtener su
valor asociado de forma inmediata, sin recorrer nada ni comparar
uno por uno.

### Definición y propiedades

**Definición formal:** Un HashMap mantiene un conjunto de pares
clave→valor bajo tres reglas que nunca se rompen:

- Cada clave existe a lo sumo una vez. Insertar una clave ya existente
  reemplaza el valor anterior.
- La misma clave siempre produce el mismo índice. La función hash
  es determinista.
- Todo valor almacenado es alcanzable por su clave.

**Propiedades clave:**

- **Sin orden:** las claves no se guardan alfabéticamente ni por
  orden de inserción.
- **Claves únicas:** no pueden coexistir dos entradas con la misma
  clave.
- **Sin restricción sobre valores:** cualquier dato puede ser un valor;
  la unicidad aplica solo a las claves.

### Representación

Internamente, un HashMap es un conjunto de posiciones llamadas
**buckets**. Cada clave pasa por la función hash, que determina en qué
bucket se almacena el par clave→valor. Cuando dos claves caen en el
mismo bucket — una **colisión** — hay dos estrategias:

**Chaining:** cada bucket contiene una lista de pares. Las colisiones
se encadenan.

![Chaining](adjuntos/hashmap_chaining.png)

**Open addressing:** cada par vive directamente en el array. Ante una
colisión, se busca el siguiente bucket libre.

![Open Addressing](adjuntos/hashmap_open.png)

En chaining los buckets crecen hacia afuera; en open addressing todo
convive dentro de la misma estructura. En chaining, si muchas claves
colisionan en el mismo bucket, ese bucket puede crecer hasta n elementos
— ese es el peor caso de todas las operaciones.

## 2. Operaciones y complejidad

### Operaciones principales

- **`insert(key, value)`:** Agrega un nuevo par clave→valor al mapa. Si la clave ya existe, reemplaza el valor anterior.
- **`find(key)`:** Dado una clave, retorna el valor asociado. Si la clave no existe, lanza un error.
- **`delete(key)`:** Elimina un par clave→valor del mapa. Si la clave no existe, no tiene efecto.
- **`update(key, value)`:** Reemplaza el valor asociado a una clave existente por uno nuevo.

### Complejidad

- **`insert(key, value)`:**
  - Tiempo promedio: O(1)
  - Tiempo peor: O(n)
  - Tiempo amortizado: O(1)
  - Complejidad espacial adicional: O(1)
- **`find(key)`:**
  - Tiempo promedio: O(1)
  - Tiempo peor: O(n)
  - Complejidad espacial adicional: O(1)
- **`delete(key)`:**
  - Tiempo promedio: O(1)
  - Tiempo peor: O(n)
  - Complejidad espacial adicional: O(1)
- **`update(key, value)`:**
  - Tiempo promedio: O(1)
  - Tiempo peor: O(n)
  - Complejidad espacial adicional: O(1)

> **Costo oculto — Rehash:** Cuando el HashMap supera cierto nivel de ocupación llamado _load factor_, crea un array del doble de tamaño y rehashea todas las claves existentes en sus nuevas posiciones. Este proceso cuesta O(n) ya que recorre todos los elementos, pero ocurre tan pocas veces que el costo amortizado de insertar sigue siendo O(1).

### Detalles operativos

- **Clave inexistente:** `find` lanza un error; `delete` no tiene efecto; `update` lanza un error.
- **Clave duplicada:** `insert` reemplaza el valor anterior.
- **Límite de tamaño:** el tamaño crece dinámicamente mediante rehashes.
- **Concurrencia:** el `dict` de Python no es thread-safe y no está pensado para accesos simultáneos.

---

## 3. Implementación

### Idea de implementación

- Descripción de la(s) estrategia(s) típica(s) para implementar la estructura.
- Algoritmos clave y pasos principales.

### Invariantes

- Lista de comprobaciones e invariantes que el código debe garantizar siempre (por ejemplo: punteros no nulos, tamaño consistente, heap property, ordenamiento mantenido).

### Ejemplo de código

- Proporciona 1-2 snippets claros y mínimos (en Python).
- Ejemplo de uso típico con entrada y salida esperada.

> Debe responder a: "¿cómo lo programo sin romperlo?"

---

## 4. Uso y criterio

### Casos de uso

- Situaciones y problemas donde la estructura encaja naturalmente.

### Cuándo NO usarlo

- Escenarios donde su uso es contraproducente o subóptimo.

### Comparaciones

- Alternativas comunes y cuándo elegir cada una (lista comparativa breve).

### Ventajas / desventajas

- Trade-offs prácticos en rendimiento, memoria, simplicidad, y facilidad de implementación.

### Señales de reconocimiento

- Pistas en el enunciado de un problema que indican que esta estructura es adecuada.

> Debe responder a: "¿cuándo conviene usarlo?"

---

## 5. Relaciones y extensiones

### Variantes

- Variantes y mejoras (por ejemplo: versiones balanceadas, persistentes, acotadas, indexadas, con hashing, etc.).

### Relación con otras estructuras

- Dependencias conceptuales y cómo se combina con otras estructuras.

### Notas avanzadas

- Temas avanzados como persistencia, concurrencia, paralelismo, ordenamientos aleatorios, caching, tuning de parámetros.

> Debe responder a: "¿cómo encaja en el mapa general de estructuras de datos?"

---

## 6. Referencias y recursos

- Enlaces y libros de referencia, artículos científicos.
- Visualizaciones y demostraciones.

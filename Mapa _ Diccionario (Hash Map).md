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
Un HashMap se implementa sobre un **array de buckets**, donde cada bucket contiene cero o más pares *(clave, valor)*.  

El flujo básico de cualquier operación es siempre el mismo:

1. Se aplica una función hash a la clave.
2. Se obtiene un índice dentro del array.
3. Se accede al bucket correspondiente.
4. Se opera dentro del bucket (buscar, insertar, eliminar).

Para resolver colisiones, la estrategia más simple y común es **chaining**, donde cada bucket es una lista.

**Algoritmos clave (chaining):**

- **insert(key, value):**
  1. Calcular índice: `i = hash(key) % capacidad`
  2. Recorrer el bucket:
     - Si la clave existe → reemplazar valor
     - Si no existe → agregar nuevo par
  3. Si el factor de carga supera el límite → rehash

- **find(key):**
  1. Calcular índice
  2. Recorrer bucket
  3. Si encuentra la clave → retorna valor
  4. Si no → error

- **delete(key):**
  1. Calcular índice
  2. Buscar en el bucket
  3. Si existe → eliminar
  4. Si no → no hace nada

---

### Invariantes  
Estas condiciones deben cumplirse siempre, sin excepción:

- Cada clave aparece **como máximo una vez** en toda la estructura.
- El tamaño (`size`) coincide con la cantidad real de pares almacenados.
- Todos los elementos están en el bucket que corresponde a su hash.
- No hay buckets “perdidos”: todo elemento es alcanzable desde el array.
- La función hash aplicada a una clave siempre lleva al mismo bucket (mientras no haya rehash).
- El factor de carga (`size / capacidad`) se mantiene bajo cierto umbral (ej: 0.75).

Si alguno de estos invariantes se rompe, el HashMap deja de funcionar correctamente.

---

### Ejemplo de código (Python)

Implementación mínima usando **chaining**:

```python
class HashMap:
    def __init__(self, capacity=8):
        self.capacity = capacity
        self.size = 0
        self.buckets = [[] for _ in range(capacity)]

    def _hash(self, key):
        return hash(key) % self.capacity

    def insert(self, key, value):
        index = self._hash(key)
        bucket = self.buckets[index]

        for i, (k, v) in enumerate(bucket):
            if k == key:
                bucket[i] = (key, value)
                return

        bucket.append((key, value))
        self.size += 1

        if self.size / self.capacity > 0.75:
            self._rehash()

    def find(self, key):
        index = self._hash(key)
        bucket = self.buckets[index]

        for k, v in bucket:
            if k == key:
                return v

        raise KeyError("Clave no encontrada")

    def delete(self, key):
        index = self._hash(key)
        bucket = self.buckets[index]

        for i, (k, v) in enumerate(bucket):
            if k == key:
                del bucket[i]
                self.size -= 1
                return

    def _rehash(self):
        old_buckets = self.buckets
        self.capacity *= 2
        self.buckets = [[] for _ in range(self.capacity)]
        self.size = 0

        for bucket in old_buckets:
            for k, v in bucket:
                self.insert(k, v)
```

### Ejemplo de Uso típico

```python
m = HashMap()

m.insert("usuario1", 100)
m.insert("usuario2", 200)

print(m.find("usuario1"))  # 100

m.update = m.insert  # reutilizamos insert para update
m.update("usuario1", 150)

print(m.find("usuario1"))  # 150

m.delete("usuario2")
```

### Salida esperada:
  100
  150

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

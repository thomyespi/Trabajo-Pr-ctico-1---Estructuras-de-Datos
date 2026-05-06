# Mapa / Diccionario (Map)

## 1. Qué es y cómo funciona

### Intuición

Si imaginamos una agenda telefónica, cada nombre está asociado a un número.
No buscás por posición ni por índice numérico: buscás por una **clave**
(el nombre del contacto) y obtenés el valor asociado (el número telefónico).

Un Map funciona exactamente así: mantiene asociaciones entre **claves** y **valores**.
Su objetivo es permitir recuperar información **a partir de una clave** de manera clara y eficiente.

### Definición y propiedades

**Definición formal:** Un Map es un tipo de dato abstracto que mantiene
una colección de pares **clave→valor** bajo ciertas reglas fundamentales:

- Cada clave existe como máximo una vez.
- Cada clave está asociada exactamente a un valor.
- Dada una clave válida, debe poder recuperarse su valor asociado.

**Propiedades clave:**

- **Claves únicas:** no pueden coexistir dos entradas con la misma clave.
- **Asociación directa:** cada clave referencia un único valor.
- **Actualización permitida:** si una clave ya existe, su valor puede reemplazarse.
- **Implementación independiente:** puede construirse mediante hashing, árboles, listas u otras estructuras.
- **No necesariamente ordenado:** el orden depende de la implementación concreta.

### Representación

Un Map es una abstracción lógica, por lo que puede implementarse de diferentes maneras:

- **HashMap:** Utiliza funciones hash para lograr un acceso promedio de O(1).
- **TreeMap:** Usa árboles balanceados para mantener las claves ordenadas, con operaciones O(log n).
- **Lista o array de pares:** Implementación simple, útil para conjuntos pequeños.

Logicamente, cada implementación ofrece distintos compromisos entre:

- Velocidad
- Uso de memoria
- Mantenimiento del orden
- Complejidad de implementación

Lo importante es que todas respetan la misma interfaz conceptual:
la de gestionar asociaciones **clave→valor**.

---

## 2. Operaciones y complejidad

### Operaciones principales

- **`insert(key, value)`:** Agrega un nuevo par clave→valor. Si la clave ya existe, reemplaza el valor anterior.
- **`find(key)`:** Retorna el valor asociado a una clave. Si no existe, lanza un error.
- **`delete(key)`:** Elimina un par clave→valor. Si la clave no existe, no tiene efecto.
- **`update(key, value)`:** Reemplaza el valor asociado a una clave existente.

### Complejidad

La complejidad de un Map depende de su implementación concreta.

| Implementación | Inserción                      | Búsqueda                       | Eliminación                    |
| -------------- | ------------------------------ | ------------------------------ | ------------------------------ |
| HashMap        | O(1) promedio / O(n) peor caso | O(1) promedio / O(n) peor caso | O(1) promedio / O(n) peor caso |
| TreeMap        | O(log n)                       | O(log n)                       | O(log n)                       |
| Lista / Array  | O(1) / O(n) según estrategia   | O(n)                           | O(n)                           |

> No existe una única complejidad para los Maps. El rendimiento depende de cómo se implemente internamente.

---

## 3. Implementación

### Idea de implementación

Independientemente de la implementación elegida, el flujo conceptual de las operaciones es siempre el mismo:

1. Localizar la clave.
2. Verificar si existe.
3. Operar sobre el par clave→valor correspondiente.

**Algoritmos clave:**

- **insert(key, value):**
  1. Buscar si la clave ya existe.
  2. Si existe → reemplazar valor.
  3. Si no existe → agregar nuevo par clave→valor.

- **find(key):**
  1. Buscar la clave.
  2. Si existe → retornar valor asociado.
  3. Si no → error o valor nulo.

- **delete(key):**
  1. Buscar la clave.
  2. Si existe → eliminar el par asociado.
  3. Si no → no hacer nada.

- **update(key, value):**
  1. Buscar la clave.
  2. Si existe → reemplazar valor.
  3. Si no → error o inserción según implementación.
     
---

### Invariantes

Estas condiciones deben cumplirse siempre:

- Cada clave aparece **como máximo una vez** en toda la estructura.
- Toda clave tiene asociado exactamente un valor.
- Las operaciones preservan la consistencia clave→valor.
- La estructura mantiene accesibles todos los pares almacenados.

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

    def update(self, key, value):
        index = self._hash(key)
        bucket = self.buckets[index]

        for i, (k, v) in enumerate(bucket):
            if k == key:
                bucket[i] = (key, value)
                return

        raise KeyError("Clave no encontrada")

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

m.update("usuario1", 150)

print(m.find("usuario1"))  # 150

m.delete("usuario2")
```

### Salida esperada:

```
100
150
```

---

## 4. Uso y criterio

### Casos de uso

Un HashMap encaja naturalmente cuando el problema consiste en asociar claves con valores y acceder a ellos de manera rápida.

Ejemplos típicos:

- Conteo de frecuencias: cantidad de apariciones de palabras, letras o números.
- Índices por identificador: buscar usuarios por email, productos por código o alumnos por legajo.
- Cachés y memoización: guardar resultados ya calculados para evitar recomputarlos.
- Agrupamiento: agrupar elementos por categoría, fecha, autor, tipo, etc.
- Verificación de pertenencia: saber rápidamente si una clave ya existe.
- Tablas de configuración: guardar pares clave→valor como parámetros, opciones o variables.

### Cuándo NO usarlo

No conviene usarlo cuando:

- Se necesita mantener los datos ordenados. Un HashMap no conserva orden alfabético, numérico ni de inserción.
- Se requiere recorrer los elementos en orden. Para eso suele ser mejor un árbol balanceado o un array ordenado.
- El conjunto de claves es muy pequeño. En tamaños chicos, una lista o array puede ser más simple y suficientemente rápido.
- Se necesita aprovechar memoria al máximo. Un HashMap suele consumir bastante más memoria que otras estructuras debido a buckets vacíos, punteros y espacio reservado.
- Las claves cambian constantemente. Modificar una clave implica borrar e insertar nuevamente.
- Se necesita garantizar rendimiento en el peor caso. Aunque normalmente es O(1), una mala distribución hash puede degradar las operaciones a O(n).

### Comparaciones

| Estructura       | Ventaja frente a HashMap                                    | Desventaja frente a HashMap      |
| ---------------- | ----------------------------------------------------------- | -------------------------------- |
| Array            | Menor uso de memoria y acceso por índice real               | Buscar por contenido cuesta O(n) |
| Lista enlazada   | Inserciones simples y flexibles                             | Búsqueda lineal O(n)             |
| Árbol balanceado | Mantiene elementos ordenados y permite recorrerlos en orden | Operaciones típicamente O(log n) |
| Set              | Ideal cuando solo importa saber si una clave existe         | No almacena valores asociados    |

### Ventajas / Desventajas

| Ventajas                                           | Desventajas                                   |
| -------------------------------------------------- | --------------------------------------------- |
| Inserción, búsqueda y borrado en O(1) promedio     | No mantiene orden                             |
| Muy útil para conteo, agrupamiento e indexación    | Puede consumir bastante memoria adicional     |
| Escala bien para grandes volúmenes de datos        | Depende de una buena función hash             |
| Permite modelar asociaciones naturales clave→valor | Las colisiones pueden degradar el rendimiento |
| Simple de usar desde muchos lenguajes modernos     | El rehash puede provocar pausas costosas      |

### Señales de reconocimiento

Hay varias pistas en un problema que sugieren que un HashMap puede ser la estructura adecuada:

- “Necesitamos encontrar rápidamente un dato a partir de una clave”.
- “Queremos saber cuántas veces aparece cada elemento”.
- “Hay que detectar duplicados”.
- “Se necesita agrupar elementos por alguna propiedad”.
- “Se busca acceso casi inmediato sin importar el tamaño del conjunto”.
- “Cada elemento tiene un identificador único”.

---

## 5. Relaciones y extensiones

### Variantes

- Existen múltiples formas de implementar mapas según las necesidades: los basados en tablas hash priorizan velocidad de acceso promedio constante; los basados en árboles balanceados mantienen un orden en las claves; y otros como los Linked Maps preservan el orden de inserción. Estas variantes representan distintos compromisos entre eficiencia, orden y uso de memoria.

### Relación con otras estructuras

- Respecto a su relación con otras estructuras, los mapas dependen conceptualmente de:
  - Arrays, como base para almacenar datos (especialmente en hashing).
  - Listas enlazadas, usadas en manejo de colisiones.
  - Árboles, para mantener orden y garantizar complejidad logarítmica.

Esto los convierte en una especie de “estructura compuesta”, que reutiliza ideas de otras más básicas.

### Notas avanzadas

- En términos de notas avanzadas, los maps pueden extenderse para soportar:
  - Persistencia, manteniendo versiones inmutables (útil en programación funcional).
  - Concurrencia, permitiendo accesos simultáneos seguros (Concurrent Maps).
  - Aleatoriedad, como en funciones hash diseñadas para distribuir uniformemente las claves.

---

## 6. Referencias y recursos

### Libros

- Cormen et al. — _Introduction to Algorithms_ (CLRS), Cap. 11: Hash Tables.
- Sedgewick & Wayne — _Algorithms_, Cap. 3: Searching.

### Visualizaciones

- VisuAlgo — Hash Table: `visualgo.net/en/hashtable`
- CS USF — Hash Table Visualization: `www.cs.usfca.edu/~galles/visualization/OpenHash.html`

### Documentación

- Python `dict`: `docs.python.org/3/library/stdtypes.html#dict`
- Java `HashMap`: `docs.oracle.com/en/java/docs/api/java.base/java/util/HashMap.html`

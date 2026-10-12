# POO-4B — Guía Completa

Índice general del temario. Cada unidad tiene su propio archivo con teoría, tablas y snippets en **C#**.

---

## [Unidad 1. Tipos de Datos, Operadores y Cadenas](unidad-1-tipos-datos-operadores-cadenas.md)

- 1.1. Tipos de datos en programación — datos primitivos, conversión de tipos, constantes y variables (`const` y `readonly`)
- 1.2. Operadores — aritméticos, relacionales y lógicos
- 1.3. Cadenas de caracteres — `string` y métodos principales, `StringBuilder` y manipulación eficiente

## [Unidad 2. Estructuras de Control y Bucles](unidad-2-estructuras-control-y-bucles.md)

- 2.1. Estructuras de control de flujo — secuencia, selección (`if`, `switch`, ternario) y repetición
- 2.2. Estructuras repetitivas — bucles `while`, `do-while` y `for`
- 2.3. Comparación y casos de uso de los bucles
- 2.4. Control de flujo — `break` y `continue`

## [Unidad 3. Arreglos](unidad-3-arreglos.md)

- 3.1. Declaración e inicialización — sintaxis, tamaño fijo, índices y acceso
- 3.2. Manipulación de arreglos — lectura y modificación, métodos de la clase `Array`
- 3.3. Iteración con estructuras repetitivas — recorrido con `for` y con `foreach`
- 3.4. Arreglos multidimensionales — matrices y recorrido con bucles anidados

## [Unidad 4. Colecciones](unidad-4-colecciones.md)

- 4.1. Diferencias entre arreglos y colecciones — tamaño fijo vs dinámico, criterios de elección
- 4.2. `List<T>` — declaración, agregar y eliminar, búsqueda y ordenamiento, recorrido

---

## Referencia rápida

### Bucles

| Bucle | Evalúa la condición | Mínimo de ejecuciones | Uso típico |
|---|---|---|---|
| `while` | Antes de cada vuelta | 0 | No se sabe cuántas vueltas habrá |
| `do-while` | Después de cada vuelta | 1 | Menús, validar entrada |
| `for` | Antes de cada vuelta | 0 | Vueltas conocidas, recorrer con índice |
| `foreach` | Por cada elemento | 0 | Recorrer colecciones sin índice |

### Arreglo vs List\<T\>

| Operación | Arreglo `T[]` | `List<T>` |
|---|---|---|
| Acceso por índice | O(1) | O(1) |
| Buscar (lineal) | O(n) | O(n) |
| Buscar (binaria, ordenado) | O(log n) | O(log n) |
| Agregar al final | — (tamaño fijo) | O(1) amortizado |
| Insertar / eliminar en medio | — (tamaño fijo) | O(n) |
| Ordenar | O(n log n) | O(n log n) |
| Número de elementos | `Length` | `Count` |

*Universidad Politécnica de Tecamac — Ingeniería en Software*

# Unidad 3 — Arreglos

Hasta ahora cada variable guardaba **un solo valor**. Pero los problemas reales trabajan con **grupos de datos**: las calificaciones de un grupo, los precios de un catálogo, las temperaturas de un mes. Declarar una variable por cada dato (`cal1`, `cal2`, `cal3`…) no escala y no se puede recorrer con un bucle. El **arreglo** es la primera estructura de datos que resuelve esto: guarda muchos valores bajo **un solo nombre** y permite llegar a cada uno por su **posición**. Todos los ejemplos están en **C#**.

---

## Índice

1. [3.1. Declaración e inicialización](#31-declaración-e-inicialización)
   - [3.1.1. Sintaxis de declaración y tamaño fijo](#311-sintaxis-de-declaración-y-tamaño-fijo)
   - [3.1.2. Índices y acceso a elementos](#312-índices-y-acceso-a-elementos)
2. [3.2. Manipulación de arreglos](#32-manipulación-de-arreglos)
   - [3.2.1. Lectura y modificación de elementos](#321-lectura-y-modificación-de-elementos)
   - [3.2.2. Métodos de la clase Array](#322-métodos-de-la-clase-array)
3. [3.3. Iteración con estructuras repetitivas](#33-iteración-con-estructuras-repetitivas)
   - [3.3.1. Recorrido con for](#331-recorrido-con-for)
   - [3.3.2. Recorrido con foreach](#332-recorrido-con-foreach)
4. [3.4. Arreglos multidimensionales](#34-arreglos-multidimensionales)
   - [3.4.1. Matrices (arreglos bidimensionales)](#341-matrices-arreglos-bidimensionales)
   - [3.4.2. Recorrido con bucles anidados](#342-recorrido-con-bucles-anidados)

---

## 3.1. Declaración e inicialización

Un **arreglo** (*array*) es una colección de **tamaño fijo** de elementos **del mismo tipo**, almacenados en posiciones **contiguas** de memoria. Cada elemento se identifica por un **índice** que empieza en `0`.

```
Sin arreglo:                         Con arreglo:

double cal1 = 85;                    double[] cal = { 85, 90, 78, 92, 88 };
double cal2 = 90;
double cal3 = 78;                    índice:   0     1     2     3     4
double cal4 = 92;                           ┌─────┬─────┬─────┬─────┬─────┐
double cal5 = 88;                    cal    │ 85  │ 90  │ 78  │ 92  │ 88  │
                                            └─────┴─────┴─────┴─────┴─────┘
5 variables, imposible de                    ▲                       ▲
recorrer con un bucle                      cal[0]                 cal[4]
```

Características:

| Característica | Descripción |
|---|---|
| Tamaño fijo | Se define al crearlo y **no puede cambiar** |
| Homogéneo | Todos los elementos son del mismo tipo |
| Contiguo | Los elementos están uno junto a otro en memoria |
| Acceso directo | Leer o escribir `arr[i]` toma tiempo constante, O(1), sin importar el tamaño |
| Índice base 0 | El primer elemento es `arr[0]` y el último `arr[arr.Length - 1]` |
| Tipo por referencia | La variable guarda una referencia; el arreglo vive en el heap |
| Clase base | Todos los arreglos heredan de `System.Array` |

**¿Por qué el acceso es tan rápido?** Como los elementos están contiguos y todos ocupan lo mismo, la dirección de cualquier elemento se calcula con una sola operación, sin recorrer los anteriores:

```
dirección de arr[i] = dirección de inicio + i × tamaño del elemento

int[] arr (cada int ocupa 4 bytes), inicio en la dirección 1000:

arr[0] → 1000 + 0 × 4 = 1000
arr[1] → 1000 + 1 × 4 = 1004
arr[3] → 1000 + 3 × 4 = 1012     ← directo, sin pasar por arr[0], arr[1], arr[2]
```

> Esa fórmula también explica por qué el índice empieza en **0**: el índice es el **desplazamiento** desde el inicio. El primer elemento está a 0 posiciones del inicio.

### 3.1.1. Sintaxis de declaración y tamaño fijo

La sintaxis general es:

```
tipo[] nombre = new tipo[tamaño];
 │      │       │    │     │
 │      │       │    │     └── cuántos elementos (fijo)
 │      │       │    └── tipo de los elementos
 │      │       └── reserva memoria en el heap
 │      └── nombre de la variable (referencia)
 └── los corchetes indican "arreglo de"
```

Declarar e inicializar son dos pasos distintos: **declarar** crea la variable (la referencia); **crear** con `new` reserva la memoria del arreglo.

```
int[] numeros;               int[] numeros = new int[3];

 numeros: null                numeros: ●──►  [ 0 | 0 | 0 ]
 (no hay arreglo todavía)                    (arreglo real en el heap)
```

| Forma | Sintaxis | Resultado |
|---|---|---|
| Solo declarar | `int[] numeros;` | Variable sin arreglo (sin asignar si es local, `null` si es campo) |
| Crear con tamaño | `int[] numeros = new int[5];` | 5 elementos con valor por defecto (`0`) |
| Crear con tamaño y valores | `int[] numeros = new int[3] { 1, 2, 3 };` | El tamaño debe coincidir con los valores |
| Crear con valores | `int[] numeros = new int[] { 1, 2, 3 };` | Tamaño 3 deducido |
| Forma corta | `int[] numeros = { 1, 2, 3 };` | Igual que la anterior |
| Con `var` | `var numeros = new[] { 1, 2, 3 };` | Tipo `int[]` inferido |
| Expresión de colección (C# 12) | `int[] numeros = [1, 2, 3];` | Igual, sintaxis moderna |
| Declarar y crear después | `int[] n; n = new int[5];` | En dos pasos |

> La forma corta `{ 1, 2, 3 }` solo se permite **en la declaración**. Para asignar después hay que escribir `new`: `numeros = new int[] { 4, 5, 6 };`.

**Valores por defecto.** Al crear con `new T[n]`, la memoria se llena automáticamente con el valor por defecto del tipo, así que un arreglo nunca tiene "basura":

| Tipo de elemento | Valor por defecto |
|---|---|
| Numéricos (`int`, `double`, `decimal`…) | `0` |
| `bool` | `false` |
| `char` | `'\0'` |
| `string` y cualquier tipo por referencia | `null` |

```csharp
int[] calificaciones = new int[5];                 // [0, 0, 0, 0, 0]
string[] nombres = { "Ana", "Luis", "Sofía" };     // tamaño 3
double[] precios = new double[] { 9.5, 12.0, 3.75 };
bool[] asistencia = new bool[3];                   // [false, false, false]
char[] vocales = { 'a', 'e', 'i', 'o', 'u' };
string[] vacios = new string[2];                   // [null, null]

Console.WriteLine(nombres.Length);                 // 3
```

> En un `string[]` recién creado los elementos son `null`, no `""`. Llamar `vacios[0].Length` lanza `NullReferenceException`.

**Tamaño fijo.** Una vez creado, **el tamaño de un arreglo no puede cambiar**. No hay forma de "agregar un elemento más": el bloque de memoria ya está reservado y lo que sigue después puede estar ocupado por otros datos.

```csharp
int[] numeros = new int[3];
Console.WriteLine(numeros.Length);   // 3
// numeros.Add(4);                   // ERROR: los arreglos no tienen Add
// numeros[3] = 4;                   // IndexOutOfRangeException
```

El tamaño **sí** puede venir de una variable calculada en tiempo de ejecución, pero una vez creado el arreglo, queda fijo:

```csharp
Console.Write("¿Cuántos alumnos? ");
int n = int.Parse(Console.ReadLine());
string[] alumnos = new string[n];    // el tamaño se decide al ejecutar...
                                      // ...pero ya no cambia
```

> Si necesitas que crezca, la única opción es crear **otro** arreglo más grande y copiar los elementos (eso hace `Array.Resize`). Para colecciones que cambian de tamaño seguido existe `List<T>` (Unidad 4).

**Los arreglos son tipos por referencia.** La variable no contiene los elementos, sino la **dirección** del arreglo. Por eso asignar un arreglo a otra variable **no lo copia**: ambas apuntan al mismo.

```csharp
int[] a = { 1, 2, 3 };
int[] b = a;          // b apunta al MISMO arreglo
b[0] = 99;
Console.WriteLine(a[0]);   // 99
```

```
 a: ●──┐
       ├──►  [ 99 | 2 | 3 ]
 b: ●──┘
```

### 3.1.2. Índices y acceso a elementos

El **índice** es un número entero que indica la posición de un elemento. Se escribe entre corchetes: `arreglo[índice]`.

| Regla | Descripción |
|---|---|
| Primer índice | `0` |
| Último índice | `Length - 1` |
| Índices válidos | De `0` a `Length - 1` (en total `Length` posiciones) |
| Tipo del índice | `int` (puede ser una variable o una expresión: `arr[i + 1]`) |
| Fuera de rango | Lanza `IndexOutOfRangeException` |

```csharp
string[] frutas = { "Manzana", "Pera", "Uva", "Mango" };

Console.WriteLine(frutas[0]);                  // Manzana (primero)
Console.WriteLine(frutas[2]);                  // Uva
Console.WriteLine(frutas[frutas.Length - 1]);  // Mango (último)

int i = 1;
Console.WriteLine(frutas[i]);                  // Pera
Console.WriteLine(frutas[i + 1]);              // Uva
```

**Índices desde el final (C# 8+).** El operador `^` cuenta desde el final: `^1` es el último, `^2` el penúltimo.

```
índice normal:      0          1        2        3
                ┌─────────┬────────┬───────┬─────────┐
frutas          │ Manzana │  Pera  │  Uva  │  Mango  │
                └─────────┴────────┴───────┴─────────┘
índice desde fin:  ^4         ^3       ^2       ^1
```

```csharp
Console.WriteLine(frutas[^1]);   // Mango
Console.WriteLine(frutas[^2]);   // Uva
```

**Rangos (C# 8+).** `inicio..fin` extrae una porción como un **arreglo nuevo**. El inicio se incluye y el fin **no**.

```csharp
int[] nums = { 10, 20, 30, 40, 50 };
int[] parte = nums[1..4];     // [20, 30, 40]  (índices 1, 2, 3)
int[] inicio = nums[..2];     // [10, 20]
int[] fin = nums[3..];        // [40, 50]
int[] sinExtremos = nums[1..^1]; // [20, 30, 40]
```

**Fuera de rango.** Es el error más común con arreglos. C# revisa **cada** acceso y lanza una excepción si el índice no es válido (en C o C++ el programa leería memoria ajena sin avisar).

```csharp
int[] a = new int[3];        // índices válidos: 0, 1, 2
a[3] = 10;                   // IndexOutOfRangeException
a[-1] = 10;                  // IndexOutOfRangeException
```

Si el índice viene del usuario o de un cálculo, valídalo antes de usarlo:

```csharp
int posicion = 7;
if (posicion >= 0 && posicion < frutas.Length)
    Console.WriteLine(frutas[posicion]);
else
    Console.WriteLine("Posición fuera de rango");
```

> Error típico: el usuario piensa en posiciones "humanas" (1, 2, 3…) y el arreglo usa 0, 1, 2… Si el usuario pide "el elemento 3", el índice es `2`. Convierte siempre: `indice = posicionUsuario - 1`.

---

## 3.2. Manipulación de arreglos

Manipular un arreglo es **leer** y **escribir** sus elementos, ya sea uno por uno mediante su índice o con los métodos de la clase `Array`. Como el tamaño es fijo, manipular **no** incluye agregar ni quitar posiciones: solo cambiar lo que hay en ellas.

### 3.2.1. Lectura y modificación de elementos

Un elemento de un arreglo (`arr[i]`) se comporta exactamente como una **variable** normal de su tipo: se puede leer, asignar, usar en expresiones, incrementar…

```csharp
int[] stock = { 15, 8, 0, 23 };

// Lectura
int primero = stock[0];              // 15
int total = stock[0] + stock[1];     // 23

// Modificación
stock[2] = 30;                       // [15, 8, 30, 23]
stock[1] += 5;                       // [15, 13, 30, 23]
stock[3]--;                          // [15, 13, 30, 22]
```

**Llenar desde consola.** Un bucle recorre cada posición y asigna el valor leído:

```csharp
double[] notas = new double[5];
for (int i = 0; i < notas.Length; i++)
{
    Console.Write($"Calificación {i + 1}: ");   // i + 1 para mostrar posiciones "humanas"
    notas[i] = double.Parse(Console.ReadLine());
}
```

**Algoritmos básicos.** Estos algoritmos son la base para trabajar con colecciones; conviene saber hacerlos a mano antes de usar métodos de la biblioteca.

```csharp
int[] datos = { 7, 3, 9, 1, 5 };

// Suma y promedio (acumulador)
int suma = 0;
for (int i = 0; i < datos.Length; i++)
    suma += datos[i];
double promedio = (double)suma / datos.Length;     // 5

// Máximo y mínimo: se parte del primer elemento, no de 0
int max = datos[0], min = datos[0];
for (int i = 1; i < datos.Length; i++)
{
    if (datos[i] > max) max = datos[i];
    if (datos[i] < min) min = datos[i];
}
Console.WriteLine($"Max: {max}, Min: {min}");      // Max: 9, Min: 1

// Contar los que cumplen una condición (contador)
int mayoresQue4 = 0;
for (int i = 0; i < datos.Length; i++)
    if (datos[i] > 4) mayoresQue4++;               // 3
```

> ¿Por qué `max` empieza en `datos[0]` y no en `0`? Si todos los valores fueran negativos, `max = 0` daría un resultado que ni siquiera está en el arreglo.

**Intercambiar dos elementos.** Para intercambiar se necesita una variable temporal; si no, el primer valor se pierde al sobrescribirlo.

```csharp
int temp = datos[0];
datos[0] = datos[4];
datos[4] = temp;

// Forma moderna con tuplas (C# 7+)
(datos[0], datos[4]) = (datos[4], datos[0]);
```

```
Sin temporal (MAL):          Con temporal (BIEN):
a = 7, b = 5                 a = 7, b = 5
a = b;   → a = 5, b = 5      temp = a;  → temp = 7
b = a;   → a = 5, b = 5 ✗    a = b;     → a = 5
                             b = temp;  → b = 7  ✓
```

**Búsqueda lineal.** Revisa los elementos uno por uno hasta encontrar el buscado. Regresa el índice o `-1` si no está.

```csharp
string[] nombres = { "Ana", "Luis", "Marta", "Pedro" };
string buscado = "Marta";
int posicion = -1;                   // -1 significa "no encontrado"

for (int i = 0; i < nombres.Length; i++)
{
    if (nombres[i] == buscado)
    {
        posicion = i;
        break;                       // ya se encontró
    }
}

Console.WriteLine(posicion >= 0 ? $"Encontrado en {posicion}" : "No encontrado");   // Encontrado en 2
```

> En el peor caso (el elemento no está o es el último) la búsqueda lineal revisa **todos** los elementos: es O(n).

**Copiar vs asignar.** Como el arreglo es un tipo por referencia, asignar no copia. Para una copia real hay que crear otro arreglo:

```csharp
int[] original = { 1, 2, 3 };

int[] alias = original;                     // NO es copia: mismo arreglo
alias[0] = 100;
Console.WriteLine(original[0]);             // 100

int[] copia = (int[])original.Clone();      // arreglo nuevo con los mismos valores
copia[0] = 1;
Console.WriteLine(original[0]);             // 100 (no se afectó)
```

```
 original: ●──┐
              ├──►  [ 100 | 2 | 3 ]
 alias:    ●──┘

 copia:    ●─────►  [  1  | 2 | 3 ]   (otro arreglo)
```

**Comparar arreglos.** `==` compara referencias, no contenido. Para comparar elemento por elemento hay que recorrerlos (o usar `SequenceEqual` de LINQ).

```csharp
int[] x = { 1, 2, 3 };
int[] y = { 1, 2, 3 };
Console.WriteLine(x == y);               // False (objetos distintos)
Console.WriteLine(x.SequenceEqual(y));   // True  (using System.Linq)
```

### 3.2.2. Métodos de la clase Array

Todos los arreglos heredan de `System.Array`, que ofrece **métodos estáticos** (se llaman como `Array.Metodo(arreglo, …)`) para operaciones comunes ya implementadas y optimizadas.

| Método / propiedad | Qué hace | Ejemplo |
|---|---|---|
| `Length` | Número total de elementos | `arr.Length` |
| `Array.Sort(arr)` | Ordena ascendentemente | `Array.Sort(arr);` |
| `Array.Reverse(arr)` | Invierte el orden | `Array.Reverse(arr);` |
| `Array.IndexOf(arr, x)` | Índice de la primera aparición de `x` o `-1` | `Array.IndexOf(arr, 9)` |
| `Array.LastIndexOf(arr, x)` | Índice de la última aparición o `-1` | `Array.LastIndexOf(arr, 9)` |
| `Array.BinarySearch(arr, x)` | Búsqueda binaria (**arreglo ordenado**) | `Array.BinarySearch(arr, 9)` |
| `Array.Exists(arr, cond)` | ¿Algún elemento cumple? | `Array.Exists(arr, x => x > 8)` |
| `Array.Find(arr, cond)` | Primer elemento que cumple | `Array.Find(arr, x => x % 2 == 0)` |
| `Array.FindAll(arr, cond)` | Todos los que cumplen (arreglo nuevo) | `Array.FindAll(arr, x => x > 3)` |
| `Array.FindIndex(arr, cond)` | Índice del primero que cumple o `-1` | `Array.FindIndex(arr, x => x < 0)` |
| `Array.TrueForAll(arr, cond)` | ¿Todos cumplen? | `Array.TrueForAll(arr, x => x > 0)` |
| `Array.Copy(origen, destino, n)` | Copia `n` elementos | `Array.Copy(a, b, a.Length);` |
| `Array.Resize(ref arr, n)` | Crea un arreglo nuevo de tamaño `n` y copia | `Array.Resize(ref arr, 10);` |
| `Array.Clear(arr)` | Pone todo en el valor por defecto | `Array.Clear(arr);` |
| `Array.Fill(arr, valor)` | Llena todo con un valor | `Array.Fill(arr, -1);` |
| `arr.Clone()` | Copia superficial | `int[] c = (int[])arr.Clone();` |

> Los métodos que reciben una **condición** (`Exists`, `Find`, `FindAll`…) usan una **expresión lambda**: `x => x > 8` se lee "dado un elemento `x`, regresa si `x > 8`". El método la aplica a cada elemento.

**Ordenar e invertir**

```csharp
int[] valores = { 42, 7, 19, 3, 25 };

Array.Sort(valores);                                  // [3, 7, 19, 25, 42]
Array.Reverse(valores);                               // [42, 25, 19, 7, 3] (descendente)

string[] nombres = { "Pedro", "ana", "Luis", "Beatriz" };
Array.Sort(nombres);                                  // orden según la cultura del equipo
Array.Sort(nombres, StringComparer.OrdinalIgnoreCase); // ana, Beatriz, Luis, Pedro
```

> `Sort` y `Reverse` **modifican el arreglo original** (no regresan uno nuevo). Si necesitas conservar el orden original, ordena una copia: `int[] ordenado = (int[])valores.Clone(); Array.Sort(ordenado);`.

**Buscar**

```csharp
int[] valores = { 42, 25, 19, 7, 3 };

Console.WriteLine(Array.IndexOf(valores, 19));          // 2
Console.WriteLine(Array.IndexOf(valores, 100));         // -1
Console.WriteLine(Array.Exists(valores, v => v > 40));  // True
Console.WriteLine(Array.Find(valores, v => v < 10));    // 7
int[] mayores = Array.FindAll(valores, v => v > 10);    // [42, 25, 19]
Console.WriteLine(Array.TrueForAll(valores, v => v > 0)); // True
```

**Búsqueda lineal vs binaria**

| | Lineal (`IndexOf`) | Binaria (`BinarySearch`) |
|---|---|---|
| Requisito | Ninguno | El arreglo **debe estar ordenado** |
| Cómo funciona | Revisa uno por uno | Compara con el centro y descarta la mitad en cada paso |
| Pasos en el peor caso (1,000 elementos) | 1,000 | ~10 |
| Complejidad | O(n) | O(log n) |

```
Buscar 25 en [3, 7, 19, 25, 42, 50, 61] con búsqueda binaria:

paso 1:  [3, 7, 19, (25), 42, 50, 61]    centro = 25 → ¡encontrado!

Buscar 50:
paso 1:  [3, 7, 19, (25), 42, 50, 61]    25 < 50 → descarta la mitad izquierda
paso 2:                  [42, (50), 61]  centro = 50 → ¡encontrado!
```

```csharp
int[] ordenado = { 3, 7, 19, 25, 42, 50, 61 };
Console.WriteLine(Array.BinarySearch(ordenado, 50));   // 5
Console.WriteLine(Array.BinarySearch(ordenado, 20) < 0); // True (no está: regresa un negativo)
```

> Si el arreglo **no** está ordenado, `BinarySearch` no lanza error: simplemente regresa resultados incorrectos.

**Copiar, redimensionar y llenar**

```csharp
int[] origen = { 1, 2, 3, 4, 5 };

int[] destino = new int[5];
Array.Copy(origen, destino, 3);        // destino = [1, 2, 3, 0, 0]

int[] datos = { 1, 2, 3 };
Array.Resize(ref datos, 5);            // datos = [1, 2, 3, 0, 0]
datos[3] = 4;

int[] marcas = new int[4];
Array.Fill(marcas, -1);                // [-1, -1, -1, -1]
Array.Clear(marcas);                   // [0, 0, 0, 0]
```

> `Array.Resize` **no** agranda el arreglo: crea uno nuevo del tamaño pedido, copia los elementos y hace que la variable apunte al nuevo (por eso recibe `ref`). Es costoso si se hace seguido; para eso existe `List<T>`.

```
Array.Resize(ref datos, 5):

antes:    datos ●──►  [ 1 | 2 | 3 ]
después:  datos ●──┐  [ 1 | 2 | 3 ]               ← el viejo queda sin uso
                   └► [ 1 | 2 | 3 | 0 | 0 ]       ← arreglo NUEVO
```

---

## 3.3. Iteración con estructuras repetitivas

**Iterar** (o recorrer) un arreglo es visitar sus elementos uno por uno con un bucle. Es la operación más frecuente con arreglos: casi cualquier procesamiento (sumar, buscar, imprimir, transformar) consiste en recorrerlo. C# ofrece dos formas principales: `for`, que trabaja con el **índice**, y `foreach`, que trabaja directamente con el **elemento**.

### 3.3.1. Recorrido con for

El bucle `for` usa una variable (normalmente `i`) que toma los valores de los índices, de `0` a `Length - 1`, y accede a cada elemento con `arr[i]`.

```csharp
int[] numeros = { 4, 8, 15, 16, 23, 42 };

for (int i = 0; i < numeros.Length; i++)
    Console.WriteLine($"numeros[{i}] = {numeros[i]}");
```

```
i = 0 → numeros[0] = 4
i = 1 → numeros[1] = 8
...
i = 5 → numeros[5] = 42
i = 6 → 6 < 6 es false → termina
```

> Usa siempre `i < arreglo.Length`, **no** `i <= arreglo.Length`. Con `<=` la última vuelta intenta `arr[Length]`, que está fuera de rango. Y usa `Length` en vez de escribir el número a mano: si el arreglo cambia de tamaño, el bucle sigue funcionando.

**Ventaja principal: el índice.** Con el índice puedes hacer cosas que `foreach` no permite:

| Necesidad | Ejemplo |
|---|---|
| Modificar elementos | `numeros[i] *= 2;` |
| Recorrer al revés | `for (int i = arr.Length - 1; i >= 0; i--)` |
| Saltar posiciones | `for (int i = 0; i < arr.Length; i += 2)` |
| Comparar con el vecino | `arr[i] > arr[i - 1]` |
| Recorrer dos arreglos en paralelo | `nombres[i]` y `edades[i]` |
| Saber la posición | `Console.WriteLine($"Posición {i}")` |

```csharp
int[] numeros = { 4, 8, 15, 16, 23, 42 };

// Modificar: duplicar todos
for (int i = 0; i < numeros.Length; i++)
    numeros[i] *= 2;                          // [8, 16, 30, 32, 46, 84]

// Recorrido inverso
for (int i = numeros.Length - 1; i >= 0; i--)
    Console.Write(numeros[i] + " ");          // 84 46 32 30 16 8

// Solo posiciones pares
for (int i = 0; i < numeros.Length; i += 2)
    Console.Write(numeros[i] + " ");          // 8 30 46
```

**Comparar elementos vecinos.** El bucle empieza en `1` para que `i - 1` siempre sea válido.

```csharp
int[] temperaturas = { 18, 21, 19, 24, 26 };
for (int i = 1; i < temperaturas.Length; i++)
{
    int cambio = temperaturas[i] - temperaturas[i - 1];
    Console.WriteLine($"Día {i + 1}: {cambio:+0;-0;0}°");
}
// Día 2: +3°   Día 3: -2°   Día 4: +5°   Día 5: +2°
```

**Arreglos paralelos.** Dos arreglos del mismo tamaño donde el índice `i` relaciona sus datos:

```csharp
string[] productos = { "Lápiz", "Cuaderno", "Borrador" };
decimal[] precios = { 8.50m, 45.00m, 6.00m };

for (int i = 0; i < productos.Length; i++)
    Console.WriteLine($"{productos[i],-10} {precios[i],8:C}");
```

> Los arreglos paralelos funcionan, pero son frágiles: si ordenas uno y no el otro, los datos se desalinean. En POO esto se resuelve creando una clase `Producto` con nombre y precio, y un solo arreglo de productos.

**Invertir en el mismo arreglo.** Dos índices avanzan desde los extremos hacia el centro, intercambiando:

```csharp
int[] datos = { 1, 2, 3, 4, 5 };
for (int i = 0, j = datos.Length - 1; i < j; i++, j--)
    (datos[i], datos[j]) = (datos[j], datos[i]);
// [5, 4, 3, 2, 1]
```

```
[ 1 | 2 | 3 | 4 | 5 ]    i=0, j=4 → intercambia 1 y 5
[ 5 | 2 | 3 | 4 | 1 ]    i=1, j=3 → intercambia 2 y 4
[ 5 | 4 | 3 | 2 | 1 ]    i=2, j=2 → i < j es false, termina
```

**Ordenamiento burbuja.** Para entender qué hace `Array.Sort` por dentro: en cada pasada se comparan pares vecinos y se intercambian si están en desorden; el mayor "sube" al final como una burbuja.

```csharp
int[] arr = { 5, 2, 9, 1, 6 };
for (int i = 0; i < arr.Length - 1; i++)
    for (int j = 0; j < arr.Length - 1 - i; j++)
        if (arr[j] > arr[j + 1])
            (arr[j], arr[j + 1]) = (arr[j + 1], arr[j]);
Console.WriteLine(string.Join(" ", arr));   // 1 2 5 6 9
```

```
Pasada 1: [5 2 9 1 6] → [2 5 9 1 6] → [2 5 9 1 6] → [2 5 1 9 6] → [2 5 1 6 9]   9 en su lugar
Pasada 2: [2 5 1 6 9] → [2 5 1 6 9] → [2 1 5 6 9] → [2 1 5 6 9]                 6 en su lugar
Pasada 3: [2 1 5 6 9] → [1 2 5 6 9] → [1 2 5 6 9]                              5 en su lugar
Pasada 4: [1 2 5 6 9] → [1 2 5 6 9]                                           ordenado
```

### 3.3.2. Recorrido con foreach

`foreach` ("para cada") recorre **todos los elementos** de principio a fin, entregando en cada vuelta **el elemento** (no su índice). No hay variable de control que inicializar, comparar ni incrementar, así que no hay forma de salirse del rango.

```
foreach (tipo elemento in arreglo)
{
    // usar elemento
}
```

```csharp
string[] colores = { "Rojo", "Verde", "Azul" };

foreach (string color in colores)
    Console.WriteLine(color.ToUpper());
// ROJO
// VERDE
// AZUL
```

```
vuelta 1:  color = colores[0] = "Rojo"
vuelta 2:  color = colores[1] = "Verde"
vuelta 3:  color = colores[2] = "Azul"
           (no hay más elementos → termina)
```

```csharp
double[] notas = { 8.5, 9.0, 7.5, 10 };
double suma = 0;
foreach (double n in notas)
    suma += n;
Console.WriteLine($"Promedio: {suma / notas.Length:F2}");   // Promedio: 8.75

// También funciona con var
foreach (var n in notas)
    Console.Write(n + " ");
```

**Limitaciones de `foreach`.** La variable de iteración es una **copia de solo lectura** del elemento actual:

```csharp
int[] nums = { 1, 2, 3 };
foreach (int n in nums)
{
    // n = n * 2;            // ERROR: no se puede asignar a la variable de iteración
}
```

| Limitación | Consecuencia |
|---|---|
| No da el índice | No sabes en qué posición estás (salvo llevando un contador aparte) |
| La variable es de solo lectura | No puedes reemplazar elementos del arreglo |
| Siempre de inicio a fin | No puedes recorrer al revés ni saltar posiciones |
| Un solo arreglo a la vez | No sirve para arreglos paralelos |

> Si los elementos son **objetos** (tipos por referencia), sí puedes modificar sus **propiedades** dentro de un `foreach` (`p.Precio *= 1.1m;`), porque la copia es de la referencia y apunta al mismo objeto. Lo que no puedes es reemplazar el objeto completo.

**`for` vs `foreach`**

| Necesidad | `for` | `foreach` |
|---|---|---|
| Solo leer todos los elementos | ✓ | ✓ (**preferido**: más claro) |
| Conocer la posición (índice) | ✓ | ✗ |
| Modificar elementos | ✓ | ✗ |
| Recorrer al revés o con saltos | ✓ | ✗ |
| Comparar con el elemento anterior/siguiente | ✓ | ✗ |
| Recorrer arreglos paralelos | ✓ | ✗ |
| Riesgo de salirse del rango | Sí, si la condición está mal | No |

---

## 3.4. Arreglos multidimensionales

Un arreglo puede tener más de una dimensión. Uno de **una** dimensión es una fila de datos; uno de **dos** dimensiones organiza los datos en **filas y columnas**, como una tabla. Se necesita un índice por cada dimensión para llegar a un elemento.

C# tiene dos tipos de arreglos de varias dimensiones:

| Característica | Rectangular `T[,]` | Escalonado (jagged) `T[][]` |
|---|---|---|
| Qué es | Un solo bloque con filas y columnas | Un arreglo **de arreglos** |
| Tamaño de filas | Todas iguales | Cada fila puede tener distinto tamaño |
| Declaración | `int[,] m = new int[3, 4];` | `int[][] m = new int[3][];` |
| Acceso | `m[fila, col]` | `m[fila][col]` |
| Tamaño de una dimensión | `m.GetLength(0)` filas, `m.GetLength(1)` columnas | `m.Length` filas, `m[i].Length` columnas |
| `Length` | Total de elementos (filas × columnas) | Número de filas |
| Uso típico | Matrices, tableros, tablas de datos | Datos irregulares (alumnos con distinto número de materias) |

### 3.4.1. Matrices (arreglos bidimensionales)

Una **matriz** es un arreglo de dos dimensiones: cada elemento se identifica con **dos índices**, `[fila, columna]`, ambos empezando en `0`. La coma dentro de los corchetes indica cuántas dimensiones hay: `[,]` son dos, `[,,]` son tres.

```
tipo[,] nombre = new tipo[filas, columnas];
```

```
int[,] m = new int[3, 4];      →  3 filas × 4 columnas = 12 elementos

            col 0   col 1   col 2   col 3
          ┌───────┬───────┬───────┬───────┐
  fila 0  │ [0,0] │ [0,1] │ [0,2] │ [0,3] │
          ├───────┼───────┼───────┼───────┤
  fila 1  │ [1,0] │ [1,1] │ [1,2] │ [1,3] │
          ├───────┼───────┼───────┼───────┤
  fila 2  │ [2,0] │ [2,1] │ [2,2] │ [2,3] │
          └───────┴───────┴───────┴───────┘
```

> El **primer** índice siempre es la **fila** y el segundo la **columna**. `m[1, 3]` es fila 1, columna 3.

**Declaración e inicialización**

```csharp
int[,] matriz = new int[2, 3];          // 2 filas, 3 columnas, todo en 0

int[,] m =
{
    { 1, 2, 3 },                        // fila 0
    { 4, 5, 6 }                         // fila 1
};

string[,] tablero = new string[3, 3];   // 9 elementos en null
```

**Acceso y dimensiones**

```csharp
Console.WriteLine(m[0, 0]);             // 1
Console.WriteLine(m[1, 2]);             // 6
m[0, 1] = 20;                           // modifica el 2

Console.WriteLine(m.Length);            // 6 (total de elementos)
Console.WriteLine(m.Rank);              // 2 (número de dimensiones)
Console.WriteLine(m.GetLength(0));      // 2 (filas)
Console.WriteLine(m.GetLength(1));      // 3 (columnas)
```

> En una matriz, `Length` es el **total** de elementos, no el número de filas. Para filas y columnas usa `GetLength(0)` y `GetLength(1)`. Es un error frecuente usar `m.Length` como límite de filas.

**¿Cómo se guarda en memoria?** Aunque la pensamos como tabla, la memoria es lineal: una matriz rectangular se guarda **fila por fila** en un solo bloque contiguo.

```
Vista lógica:              En memoria (contiguo, fila por fila):
┌───┬───┬───┐
│ 1 │ 2 │ 3 │              [ 1 | 2 | 3 | 4 | 5 | 6 ]
├───┼───┼───┤                └─ fila 0 ─┘ └─ fila 1 ─┘
│ 4 │ 5 │ 6 │
└───┴───┴───┘              posición de m[i, j] = i × columnas + j
                           m[1, 2] → 1 × 3 + 2 = 5 → el 6
```

**Usos típicos**

| Problema | Filas | Columnas |
|---|---|---|
| Calificaciones | Alumnos | Materias |
| Ventas | Meses | Sucursales |
| Tablero de gato o ajedrez | Filas del tablero | Columnas del tablero |
| Imagen | Filas de píxeles | Columnas de píxeles |
| Horario escolar | Horas | Días |

**Arreglos de tres dimensiones**

```csharp
int[,,] cubo = new int[2, 3, 4];   // 2 × 3 × 4 = 24 elementos
cubo[1, 2, 3] = 99;
Console.WriteLine(cubo.Rank);      // 3
```

**Arreglos escalonados (jagged).** Un arreglo cuyos elementos son **otros arreglos**. Cada "fila" es un arreglo independiente y puede tener su propio tamaño.

```
calif[0] ──► [ 9 | 8 | 10 ]
calif[1] ──► [ 7 | 6 ]
calif[2] ──► [ 10 | 9 | 8 | 9 ]
```

```csharp
int[][] calif = new int[3][];          // 3 filas, todavía sin crear
calif[0] = new int[] { 9, 8, 10 };
calif[1] = new int[] { 7, 6 };
calif[2] = new int[] { 10, 9, 8, 9 };

Console.WriteLine(calif[2][1]);        // 9
Console.WriteLine(calif.Length);       // 3 (filas)
Console.WriteLine(calif[2].Length);    // 4 (elementos de la fila 2)
```

> Al crear `new int[3][]` solo se crea el arreglo **externo**; cada fila vale `null` hasta que le asignes su propio arreglo. Acceder a `calif[0][0]` antes de eso lanza `NullReferenceException`.

### 3.4.2. Recorrido con bucles anidados

Para visitar todos los elementos de una matriz se usan **dos bucles anidados**: el **externo** recorre las filas y el **interno** recorre las columnas de la fila actual. Por cada vuelta del externo, el interno da todas sus vueltas.

```csharp
int[,] m =
{
    { 3, 8, 1 },
    { 6, 2, 9 },
    { 4, 7, 5 }
};

int filas = m.GetLength(0);
int columnas = m.GetLength(1);

for (int i = 0; i < filas; i++)            // externo: filas
{
    for (int j = 0; j < columnas; j++)     // interno: columnas
        Console.Write($"{m[i, j],4}");
    Console.WriteLine();                   // salto de línea al terminar cada fila
}
//    3   8   1
//    6   2   9
//    4   7   5
```

```
Orden de visita:

i = 0:  j = 0 → m[0,0]   j = 1 → m[0,1]   j = 2 → m[0,2]   → salto de línea
i = 1:  j = 0 → m[1,0]   j = 1 → m[1,1]   j = 2 → m[1,2]   → salto de línea
i = 2:  j = 0 → m[2,0]   j = 1 → m[2,1]   j = 2 → m[2,2]   → salto de línea

Total: 3 × 3 = 9 visitas
```

**Recorrido por columnas.** Si se invierten los bucles (externo = columnas), se recorre la matriz de arriba hacia abajo:

```csharp
for (int j = 0; j < columnas; j++)
    for (int i = 0; i < filas; i++)
        Console.Write(m[i, j] + " ");   // 3 6 4 8 2 7 1 9 5
```

**`foreach` sobre una matriz** recorre todos los elementos fila por fila, pero sin índices:

```csharp
int total = 0;
foreach (int valor in m)
    total += valor;
Console.WriteLine(total);               // 45
```

**Llenar desde consola**

```csharp
int[,] datos = new int[2, 3];
for (int i = 0; i < datos.GetLength(0); i++)
    for (int j = 0; j < datos.GetLength(1); j++)
    {
        Console.Write($"Valor [{i},{j}]: ");
        datos[i, j] = int.Parse(Console.ReadLine());
    }
```

**Suma por fila y por columna.** El acumulador se reinicia en `0` **dentro** del bucle externo, para que cada fila (o columna) tenga su propia suma.

```csharp
for (int i = 0; i < filas; i++)
{
    int sumaFila = 0;                   // se reinicia por cada fila
    for (int j = 0; j < columnas; j++)
        sumaFila += m[i, j];
    Console.WriteLine($"Fila {i}: {sumaFila}");      // 12, 17, 16
}

for (int j = 0; j < columnas; j++)
{
    int sumaCol = 0;
    for (int i = 0; i < filas; i++)
        sumaCol += m[i, j];
    Console.WriteLine($"Columna {j}: {sumaCol}");    // 13, 17, 15
}
```

**Diagonales.** En una matriz cuadrada, la diagonal principal tiene `i == j`; la secundaria tiene `i + j == n - 1`. Basta **un** bucle.

```
diagonal principal (i == j)     diagonal secundaria (i + j == 2)
┌───┬───┬───┐                   ┌───┬───┬───┐
│[3]│ 8 │ 1 │                   │ 3 │ 8 │[1]│
├───┼───┼───┤                   ├───┼───┼───┤
│ 6 │[2]│ 9 │                   │ 6 │[2]│ 9 │
├───┼───┼───┤                   ├───┼───┼───┤
│ 4 │ 7 │[5]│                   │[4]│ 7 │ 5 │
└───┴───┴───┘                   └───┴───┴───┘
```

```csharp
int n = m.GetLength(0);
int principal = 0, secundaria = 0;
for (int i = 0; i < n; i++)
{
    principal += m[i, i];               // 3 + 2 + 5 = 10
    secundaria += m[i, n - 1 - i];      // 1 + 2 + 4 = 7
}
```

**Transpuesta.** Las filas se vuelven columnas: el elemento `[i, j]` pasa a `[j, i]`. La matriz resultado tiene las dimensiones invertidas.

```csharp
int[,] a = { { 1, 2, 3 }, { 4, 5, 6 } };            // 2 × 3
int[,] t = new int[a.GetLength(1), a.GetLength(0)]; // 3 × 2

for (int i = 0; i < a.GetLength(0); i++)
    for (int j = 0; j < a.GetLength(1); j++)
        t[j, i] = a[i, j];
// t = { {1, 4}, {2, 5}, {3, 6} }
```

**Recorrer un arreglo escalonado.** El límite del bucle interno es el tamaño de **cada fila**, que puede variar:

```csharp
for (int i = 0; i < calif.Length; i++)
{
    Console.Write($"Alumno {i}: ");
    for (int j = 0; j < calif[i].Length; j++)    // calif[i].Length cambia por fila
        Console.Write(calif[i][j] + " ");
    Console.WriteLine();
}
```

**Ejemplo integrador: tabla de calificaciones**

```csharp
string[] alumnos = { "Ana", "Luis", "Marta" };
string[] materias = { "Mate", "Física", "Prog" };
double[,] cal =
{
    { 9.0, 8.5, 10 },
    { 7.0, 6.5, 8.0 },
    { 8.5, 9.5, 9.0 }
};

Console.Write($"{"Alumno",-8}");
foreach (string mat in materias)
    Console.Write($"{mat,8}");
Console.WriteLine($"{"Prom",8}");

for (int i = 0; i < alumnos.Length; i++)
{
    Console.Write($"{alumnos[i],-8}");
    double suma = 0;
    for (int j = 0; j < materias.Length; j++)
    {
        Console.Write($"{cal[i, j],8:F1}");
        suma += cal[i, j];
    }
    Console.WriteLine($"{suma / materias.Length,8:F2}");
}
// Alumno      Mate  Física    Prog    Prom
// Ana          9.0     8.5    10.0    9.17
// Luis         7.0     6.5     8.0    7.17
// Marta        8.5     9.5     9.0    9.00
```

---

## Resumen de la unidad

- **Arreglo:** tamaño fijo, elementos del mismo tipo y contiguos en memoria; por eso el acceso por índice es O(1).
- **Índices:** van de `0` a `Length - 1`; fuera de ese rango se lanza `IndexOutOfRangeException`. `^1` es el último y `a..b` extrae un rango (sin incluir `b`).
- **Tipo por referencia:** asignar un arreglo a otra variable **no** lo copia; usa `Clone()` o `Array.Copy`. `==` compara referencias.
- **Clase `Array`:** `Sort`, `Reverse`, `IndexOf`, `BinarySearch` (solo con arreglo ordenado), `Find`, `FindAll`, `Exists`, `Copy`, `Resize` (crea uno nuevo), `Fill`, `Clear`.
- **`for` vs `foreach`:** `for` da el índice y permite modificar, recorrer al revés o comparar vecinos; `foreach` es más claro para solo leer y nunca se sale del rango.
- **Matrices `T[,]`:** se accede con `[fila, columna]`; usa `GetLength(0)` y `GetLength(1)`, porque `Length` es el total. Se recorren con dos bucles anidados: externo filas, interno columnas.
- **Escalonados `T[][]`:** arreglo de arreglos; cada fila tiene su propio `Length`.

*Universidad Politécnica de Tecamac — Ingeniería en Software*

# Unidad 3. Arreglos

## 3.1. Declaración e inicialización

Un **arreglo** (*array*) es una estructura que almacena **varios valores del mismo tipo** en posiciones **contiguas** de memoria, bajo un solo nombre.

Sin arreglos:

```csharp
double cal1 = 85, cal2 = 90, cal3 = 78, cal4 = 92, cal5 = 88;   // ⚠️ poco práctico
```

Con arreglos:

```csharp
double[] calificaciones = { 85, 90, 78, 92, 88 };
```

```
calificaciones
 índice:   [0]   [1]   [2]   [3]   [4]
         ┌─────┬─────┬─────┬─────┬─────┐
 valor:  │ 85  │ 90  │ 78  │ 92  │ 88  │
         └─────┴─────┴─────┴─────┴─────┘
```

### 3.1.1. Sintaxis de declaración y tamaño fijo

```csharp
tipo[] nombre = new tipo[tamaño];
```

#### Formas de declarar e inicializar

```csharp
// 1. Solo tamaño: los elementos toman su valor por defecto
int[] edades = new int[5];               // [0, 0, 0, 0, 0]

// 2. Tamaño y valores
int[] pares = new int[4] { 2, 4, 6, 8 };

// 3. Valores sin indicar tamaño (se deduce)
int[] impares = new int[] { 1, 3, 5, 7 };

// 4. Forma abreviada
string[] dias = { "Lun", "Mar", "Mié", "Jue", "Vie" };

// 5. Expresión de colección (C# 12+)
int[] primos = [2, 3, 5, 7, 11];

// 6. Declarar primero, crear después
double[] precios;
precios = new double[10];
```

#### Valores por defecto

| Tipo de elemento | Valor por defecto |
|---|---|
| `int`, `long`, `double`, `decimal`… | `0` |
| `bool` | `false` |
| `char` | `'\0'` |
| `string` y otros tipos por referencia | `null` |

#### Tamaño fijo

Una vez creado, **el tamaño de un arreglo no puede cambiar**. Si necesitas más espacio hay que crear un arreglo nuevo (o usar una colección como `List<T>`, ver Unidad 4).

```csharp
int[] numeros = new int[3];
Console.WriteLine(numeros.Length);   // → 3

// numeros.Add(4);   ⚠️ no existe: los arreglos no crecen
```

El tamaño puede venir de una variable, pero se fija al momento de crear el arreglo:

```csharp
Console.Write("¿Cuántos alumnos? ");
int n = int.Parse(Console.ReadLine()!);
string[] alumnos = new string[n];
```

> Los arreglos son **tipos por referencia**: la variable guarda la dirección del arreglo, no los datos.
>
> ```csharp
> int[] a = { 1, 2, 3 };
> int[] b = a;      // b apunta al MISMO arreglo
> b[0] = 99;
> Console.WriteLine(a[0]);   // → 99
> ```

### 3.1.2. Índices y acceso a elementos

- Los índices empiezan en **0**.
- El último índice es **`Length - 1`**.
- Se accede con corchetes: `arreglo[índice]`.

```csharp
string[] frutas = { "Manzana", "Pera", "Uva", "Mango" };

Console.WriteLine(frutas[0]);                  // → Manzana (primero)
Console.WriteLine(frutas[2]);                  // → Uva
Console.WriteLine(frutas[frutas.Length - 1]);  // → Mango (último)
Console.WriteLine(frutas[^1]);                 // → Mango (índice desde el final, C# 8+)
Console.WriteLine(frutas[^2]);                 // → Uva
```

#### Fuera de rango

```csharp
Console.WriteLine(frutas[4]);   // ⚠️ IndexOutOfRangeException
Console.WriteLine(frutas[-1]);  // ⚠️ IndexOutOfRangeException
```

Valida antes de acceder:

```csharp
int indice = 7;
if (indice >= 0 && indice < frutas.Length)
    Console.WriteLine(frutas[indice]);
else
    Console.WriteLine("Índice fuera de rango");
```

#### Rangos (C# 8+)

```csharp
int[] nums = { 10, 20, 30, 40, 50 };
int[] parte = nums[1..4];   // → [20, 30, 40] (el final no se incluye)
int[] inicio = nums[..2];   // → [10, 20]
int[] fin = nums[3..];      // → [40, 50]
```

---

## 3.2. Manipulación de arreglos

### 3.2.1. Lectura y modificación de elementos

```csharp
int[] stock = { 15, 8, 0, 23 };

// Lectura
int primero = stock[0];

// Modificación
stock[2] = 30;          // [15, 8, 30, 23]
stock[1] += 5;          // [15, 13, 30, 23]
stock[3]--;             // [15, 13, 30, 22]
```

#### Llenar un arreglo con datos del usuario

```csharp
double[] notas = new double[5];

for (int i = 0; i < notas.Length; i++)
{
    Console.Write($"Calificación {i + 1}: ");
    notas[i] = double.Parse(Console.ReadLine()!);
}
```

#### Operaciones típicas

```csharp
int[] datos = { 7, 3, 9, 1, 5 };

// Suma y promedio
int suma = 0;
for (int i = 0; i < datos.Length; i++)
    suma += datos[i];
double promedio = (double)suma / datos.Length;

// Máximo y mínimo
int max = datos[0], min = datos[0];
for (int i = 1; i < datos.Length; i++)
{
    if (datos[i] > max) max = datos[i];
    if (datos[i] < min) min = datos[i];
}

// Intercambiar dos elementos
(datos[0], datos[4]) = (datos[4], datos[0]);   // tupla (C# 7+)

// Equivalente clásico
int temp = datos[0];
datos[0] = datos[4];
datos[4] = temp;
```

#### Búsqueda lineal

```csharp
string[] nombres = { "Ana", "Luis", "Marta", "Pedro" };
string buscado = "Marta";
int posicion = -1;

for (int i = 0; i < nombres.Length; i++)
{
    if (nombres[i] == buscado)
    {
        posicion = i;
        break;
    }
}

Console.WriteLine(posicion >= 0
    ? $"Encontrado en la posición {posicion}"
    : "No encontrado");
```

### 3.2.2. Métodos de la clase `Array`

`System.Array` es la clase base de todos los arreglos y ofrece métodos estáticos útiles.

| Método | Descripción | Ejemplo |
|---|---|---|
| `Length` (propiedad) | Número total de elementos | `arr.Length` |
| `Array.Sort(arr)` | Ordena de forma ascendente | `Array.Sort(nums);` |
| `Array.Reverse(arr)` | Invierte el orden | `Array.Reverse(nums);` |
| `Array.IndexOf(arr, x)` | Primer índice de `x` (−1 si no está) | `Array.IndexOf(nums, 5)` |
| `Array.LastIndexOf(arr, x)` | Último índice de `x` | `Array.LastIndexOf(nums, 5)` |
| `Array.BinarySearch(arr, x)` | Búsqueda binaria (**requiere arreglo ordenado**) | `Array.BinarySearch(nums, 5)` |
| `Array.Exists(arr, cond)` | ¿Algún elemento cumple la condición? | `Array.Exists(nums, n => n > 10)` |
| `Array.Find(arr, cond)` | Primer elemento que cumple | `Array.Find(nums, n => n % 2 == 0)` |
| `Array.FindAll(arr, cond)` | Todos los que cumplen (nuevo arreglo) | `Array.FindAll(nums, n => n > 3)` |
| `Array.FindIndex(arr, cond)` | Índice del primero que cumple | `Array.FindIndex(nums, n => n < 0)` |
| `Array.Copy(origen, destino, n)` | Copia `n` elementos | `Array.Copy(a, b, 3);` |
| `Array.Resize(ref arr, n)` | Crea un arreglo nuevo de tamaño `n` y copia | `Array.Resize(ref nums, 10);` |
| `Array.Clear(arr)` | Pone los elementos en su valor por defecto | `Array.Clear(nums);` |
| `Array.Fill(arr, x)` | Llena todo con `x` | `Array.Fill(nums, -1);` |
| `arr.Clone()` | Copia superficial | `int[] copia = (int[])nums.Clone();` |

#### Ejemplos

```csharp
int[] nums = { 42, 7, 19, 3, 25 };

Array.Sort(nums);
Console.WriteLine(string.Join(", ", nums));   // → 3, 7, 19, 25, 42

Array.Reverse(nums);
Console.WriteLine(string.Join(", ", nums));   // → 42, 25, 19, 7, 3

Console.WriteLine(Array.IndexOf(nums, 19));   // → 2
Console.WriteLine(Array.IndexOf(nums, 100));  // → -1

int[] mayores = Array.FindAll(nums, n => n > 10);
Console.WriteLine(string.Join(", ", mayores)); // → 42, 25, 19

bool hayPar = Array.Exists(nums, n => n % 2 == 0);   // → true
```

> `n => n > 10` es una **expresión lambda**: una función corta que recibe `n` y devuelve si cumple la condición.

#### Copiar correctamente

```csharp
int[] original = { 1, 2, 3 };

int[] mismaReferencia = original;           // ⚠️ NO es copia
int[] copia1 = (int[])original.Clone();     // copia real
int[] copia2 = new int[original.Length];
Array.Copy(original, copia2, original.Length);

copia1[0] = 100;
Console.WriteLine(original[0]);   // → 1 (no se afectó)
```

#### "Agrandar" un arreglo

```csharp
int[] datos = { 1, 2, 3 };
Array.Resize(ref datos, 5);   // → [1, 2, 3, 0, 0]
datos[3] = 4;
datos[4] = 5;
```

> `Resize` no agranda el arreglo original: crea uno nuevo y copia. Si necesitas agregar elementos con frecuencia, usa `List<T>`.

#### Ordenar cadenas y en orden descendente

```csharp
string[] nombres = { "Pedro", "ana", "Luis", "Beatriz" };
Array.Sort(nombres, StringComparer.OrdinalIgnoreCase);
// → ana, Beatriz, Luis, Pedro

int[] valores = { 5, 1, 4 };
Array.Sort(valores);
Array.Reverse(valores);   // → 5, 4, 1 (descendente)
```

---

## 3.3. Iteración con estructuras repetitivas

### 3.3.1. Recorrido con `for`

Usa `for` cuando necesitas **el índice**: para modificar elementos, recorrer en reversa, saltar posiciones o comparar elementos vecinos.

```csharp
int[] numeros = { 4, 8, 15, 16, 23, 42 };

// Recorrido normal
for (int i = 0; i < numeros.Length; i++)
    Console.WriteLine($"[{i}] = {numeros[i]}");

// Recorrido inverso
for (int i = numeros.Length - 1; i >= 0; i--)
    Console.Write(numeros[i] + " ");

// Solo posiciones pares
for (int i = 0; i < numeros.Length; i += 2)
    Console.Write(numeros[i] + " ");

// Modificar: duplicar todos
for (int i = 0; i < numeros.Length; i++)
    numeros[i] *= 2;
```

> ⚠️ Usa siempre `i < arreglo.Length`, no `i <= arreglo.Length` (el último índice válido es `Length - 1`).

#### Comparar elementos vecinos

```csharp
int[] temperaturas = { 18, 21, 19, 24, 26 };

for (int i = 1; i < temperaturas.Length; i++)
{
    int cambio = temperaturas[i] - temperaturas[i - 1];
    Console.WriteLine($"Día {i + 1}: {(cambio >= 0 ? "+" : "")}{cambio}°");
}
```

#### Ordenamiento burbuja (para entender cómo funciona `Sort`)

```csharp
int[] arr = { 5, 2, 9, 1, 6 };

for (int i = 0; i < arr.Length - 1; i++)
{
    for (int j = 0; j < arr.Length - 1 - i; j++)
    {
        if (arr[j] > arr[j + 1])
            (arr[j], arr[j + 1]) = (arr[j + 1], arr[j]);
    }
}

Console.WriteLine(string.Join(" ", arr));   // → 1 2 5 6 9
```

### 3.3.2. Recorrido con `foreach`

`foreach` recorre **todos los elementos** de principio a fin sin manejar índices. Es más legible y evita errores de rango.

```csharp
foreach (tipo elemento in arreglo)
{
    // usar elemento
}
```

```csharp
string[] colores = { "Rojo", "Verde", "Azul" };

foreach (string color in colores)
{
    Console.WriteLine(color.ToUpper());
}
```

```csharp
double[] notas = { 8.5, 9.0, 7.2, 10 };
double suma = 0;

foreach (double n in notas)
    suma += n;

Console.WriteLine($"Promedio: {suma / notas.Length:F2}");
```

#### Limitaciones de `foreach`

```csharp
int[] nums = { 1, 2, 3 };

foreach (int n in nums)
{
    // n = n * 2;   ⚠️ error: la variable de iteración es de solo lectura
}
```

- No da acceso directo al índice.
- No permite modificar el elemento (la variable de iteración es de solo lectura).
- Siempre recorre de inicio a fin.

### `for` vs `foreach`

| Necesidad | `for` | `foreach` |
|---|---|---|
| Solo leer todos los elementos | ✓ | ✓ (**preferido**) |
| Conocer la posición (índice) | ✓ | ✗ |
| Modificar elementos | ✓ | ✗ |
| Recorrer al revés o con saltos | ✓ | ✗ |
| Comparar con el elemento anterior/siguiente | ✓ | ✗ |
| Menos riesgo de errores de índice | | ✓ |

---

## 3.4. Arreglos multidimensionales

### 3.4.1. Matrices (arreglos bidimensionales)

Una **matriz** organiza los datos en **filas y columnas**. Se accede con dos índices: `[fila, columna]`.

```csharp
tipo[,] nombre = new tipo[filas, columnas];
```

```csharp
int[,] matriz = new int[3, 4];   // 3 filas, 4 columnas, todo en 0

int[,] tablero =
{
    { 1, 2, 3 },
    { 4, 5, 6 },
    { 7, 8, 9 }
};
```

```
tablero         col 0  col 1  col 2
              ┌──────┬──────┬──────┐
     fila 0   │  1   │  2   │  3   │
              ├──────┼──────┼──────┤
     fila 1   │  4   │  5   │  6   │
              ├──────┼──────┼──────┤
     fila 2   │  7   │  8   │  9   │
              └──────┴──────┴──────┘
```

#### Acceso y dimensiones

```csharp
Console.WriteLine(tablero[0, 0]);   // → 1
Console.WriteLine(tablero[1, 2]);   // → 6
tablero[2, 1] = 80;                 // modifica el 8

Console.WriteLine(tablero.Length);        // → 9 (total de elementos)
Console.WriteLine(tablero.Rank);          // → 2 (número de dimensiones)
Console.WriteLine(tablero.GetLength(0));  // → 3 (filas)
Console.WriteLine(tablero.GetLength(1));  // → 3 (columnas)
```

> ⚠️ En una matriz `Length` es el **total** de elementos, no el número de filas. Para filas y columnas usa `GetLength(0)` y `GetLength(1)`.

#### Usos típicos

- Calificaciones: filas = alumnos, columnas = materias.
- Tableros de juego (gato, ajedrez, buscaminas).
- Tablas de ventas: filas = meses, columnas = sucursales.
- Imágenes (píxeles), mapas, horarios.

#### Arreglos escalonados (*jagged*)

Un **arreglo de arreglos**: cada fila puede tener un tamaño distinto.

```csharp
int[][] escalonado = new int[3][];
escalonado[0] = new int[] { 1, 2 };
escalonado[1] = new int[] { 3, 4, 5, 6 };
escalonado[2] = new int[] { 7 };

Console.WriteLine(escalonado[1][2]);      // → 5
Console.WriteLine(escalonado[1].Length);  // → 4
```

| | Matriz `[,]` | Escalonado `[][]` |
|---|---|---|
| Forma | Rectangular | Cada fila con su tamaño |
| Acceso | `m[i, j]` | `m[i][j]` |
| Filas / columnas | `GetLength(0)` / `GetLength(1)` | `m.Length` / `m[i].Length` |

### 3.4.2. Recorrido con bucles anidados

El bucle **externo** recorre las filas y el **interno** las columnas.

```csharp
int[,] m =
{
    { 3, 8, 1 },
    { 6, 2, 9 },
    { 4, 7, 5 }
};

int filas = m.GetLength(0);
int columnas = m.GetLength(1);

for (int i = 0; i < filas; i++)
{
    for (int j = 0; j < columnas; j++)
    {
        Console.Write($"{m[i, j],4}");
    }
    Console.WriteLine();
}
```

`foreach` también recorre una matriz, pero visita los elementos fila por fila sin darte los índices:

```csharp
int total = 0;
foreach (int valor in m)
    total += valor;
Console.WriteLine(total);   // → 45
```

#### Suma por fila y por columna

```csharp
for (int i = 0; i < filas; i++)
{
    int sumaFila = 0;
    for (int j = 0; j < columnas; j++)
        sumaFila += m[i, j];
    Console.WriteLine($"Fila {i}: {sumaFila}");
}

for (int j = 0; j < columnas; j++)
{
    int sumaCol = 0;
    for (int i = 0; i < filas; i++)
        sumaCol += m[i, j];
    Console.WriteLine($"Columna {j}: {sumaCol}");
}
```

#### Diagonal principal

```csharp
int diagonal = 0;
for (int i = 0; i < filas; i++)
    diagonal += m[i, i];
Console.WriteLine($"Diagonal: {diagonal}");   // → 3 + 2 + 5 = 10
```

#### Transpuesta

```csharp
int[,] t = new int[columnas, filas];

for (int i = 0; i < filas; i++)
    for (int j = 0; j < columnas; j++)
        t[j, i] = m[i, j];
```

#### Ejemplo integrador: calificaciones

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
```

---

## Resumen

- Un arreglo guarda varios valores del mismo tipo; su tamaño es **fijo** y sus índices van de `0` a `Length - 1`.
- Acceder fuera del rango lanza `IndexOutOfRangeException`.
- Los arreglos son tipos por referencia: asignar uno a otro **no** lo copia; usa `Clone()` o `Array.Copy`.
- La clase `Array` ofrece `Sort`, `Reverse`, `IndexOf`, `BinarySearch`, `Find`, `Copy`, `Resize`, entre otros.
- Usa `for` cuando necesitas el índice o modificar elementos; `foreach` para solo leer.
- Las matrices `[,]` se recorren con bucles anidados usando `GetLength(0)` y `GetLength(1)`.

---

## Ejercicios

> Resuélvelos en `practica/Unidad3/`.

1. **Estadísticas.** Lee 10 números en un arreglo y muestra la suma, el promedio, el máximo, el mínimo y cuántos están por encima del promedio.
2. **Invertir sin `Reverse`.** Invierte un arreglo en el mismo lugar intercambiando extremos.
3. **Buscar y contar.** Pide un valor y muestra en qué posiciones aparece y cuántas veces.
4. **Eliminar duplicados.** A partir de `{ 3, 1, 3, 7, 1, 9 }` genera un arreglo nuevo sin repetidos.
5. **Rotar.** Rota los elementos de un arreglo una posición a la derecha: `[1,2,3,4] → [4,1,2,3]`.
6. **Comparar métodos.** Ordena un arreglo con burbuja y con `Array.Sort`; luego busca un valor con `IndexOf` y con `BinarySearch`.
7. **Matriz identidad.** Genera e imprime una matriz identidad de `n × n`.
8. **Suma de matrices.** Lee dos matrices de 3 × 3 y muestra su suma.
9. **Ventas.** Matriz de 4 sucursales × 12 meses con valores aleatorios; muestra el total por sucursal, el total por mes y la sucursal con más ventas.
10. **Gato (tic-tac-toe).** Usa una matriz `char[3,3]` para jugar entre dos personas y detecta el ganador revisando filas, columnas y diagonales.

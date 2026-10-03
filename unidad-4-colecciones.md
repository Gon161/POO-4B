# Unidad 4. Colecciones

## 4.1. Diferencias entre arreglos y colecciones

Una **colección** es una clase que agrupa objetos y administra su almacenamiento por nosotros. En .NET las colecciones genéricas están en el espacio de nombres `System.Collections.Generic` (incluido por defecto en los proyectos modernos).

| Colección | Descripción |
|---|---|
| `List<T>` | Lista de elementos con índice, de tamaño dinámico (**tema de esta unidad**) |
| `Dictionary<TKey, TValue>` | Pares clave-valor |
| `Queue<T>` | Cola (primero en entrar, primero en salir) |
| `Stack<T>` | Pila (último en entrar, primero en salir) |
| `HashSet<T>` | Conjunto sin elementos repetidos |

> `T` es un **parámetro de tipo** (genérico): indica qué tipo de elementos guarda la colección. `List<int>` guarda enteros, `List<string>` guarda cadenas, `List<Alumno>` guarda objetos `Alumno`.

### 4.1.1. Tamaño fijo frente a tamaño dinámico

#### Arreglo: tamaño fijo

```csharp
string[] nombres = new string[3];
nombres[0] = "Ana";
nombres[1] = "Luis";
nombres[2] = "Marta";
// nombres[3] = "Pedro";   ⚠️ IndexOutOfRangeException

// Para agregar uno más hay que crear otro arreglo
Array.Resize(ref nombres, 4);
nombres[3] = "Pedro";
```

#### `List<T>`: tamaño dinámico

```csharp
List<string> nombres = new List<string>();
nombres.Add("Ana");
nombres.Add("Luis");
nombres.Add("Marta");
nombres.Add("Pedro");      // crece sola
nombres.Remove("Luis");    // y también se reduce

Console.WriteLine(nombres.Count);   // → 3
```

#### ¿Cómo crece una lista? `Count` vs `Capacity`

Internamente, `List<T>` usa un **arreglo**. Cuando se llena, crea uno nuevo del **doble** de tamaño y copia los elementos.

- `Count`: elementos que realmente contiene.
- `Capacity`: espacio reservado en el arreglo interno.

```csharp
var lista = new List<int>();
for (int i = 1; i <= 9; i++)
{
    lista.Add(i);
    Console.WriteLine($"Count = {lista.Count}, Capacity = {lista.Capacity}");
}
// Capacity: 4 → 4 → 4 → 4 → 8 → 8 → 8 → 8 → 16
```

Si sabes aproximadamente cuántos elementos tendrás, puedes reservar la capacidad desde el inicio para evitar copias:

```csharp
var lista = new List<int>(1000);
```

```
Arreglo (fijo)                       List<T> (dinámica)
┌───┬───┬───┐                        ┌───┬───┬───┬───┐
│ A │ B │ C │  ← lleno, no crece     │ A │ B │ C │ D │  Count = 4, Capacity = 4
└───┴───┴───┘                        └───┴───┴───┴───┘
                                          Add("E") ↓  (copia a un arreglo del doble)
                                     ┌───┬───┬───┬───┬───┬───┬───┬───┐
                                     │ A │ B │ C │ D │ E │   │   │   │  Count = 5, Capacity = 8
                                     └───┴───┴───┴───┴───┴───┴───┴───┘
```

#### Comparación general

| Característica | Arreglo `T[]` | `List<T>` |
|---|---|---|
| Tamaño | Fijo al crearlo | Dinámico |
| Número de elementos | `Length` | `Count` |
| Agregar elementos | No (solo con `Array.Resize`) | `Add`, `AddRange`, `Insert` |
| Eliminar elementos | No | `Remove`, `RemoveAt`, `RemoveAll`, `Clear` |
| Acceso por índice | `arr[i]` | `lista[i]` |
| Métodos de búsqueda/orden | Estáticos en `Array` (`Array.Sort(arr)`) | De instancia (`lista.Sort()`) |
| Multidimensional | Sí (`[,]`) | No directamente (se usa `List<List<T>>`) |
| Rendimiento | Ligeramente más rápido y ligero | Muy cercano; pequeño costo extra al crecer |

### 4.1.2. Criterios para elegir entre arreglo y colección

**Usa un arreglo cuando:**

- El número de elementos es **conocido y no cambia** (días de la semana, meses, 3 × 3 de un tablero).
- Trabajas con **matrices** o datos multidimensionales.
- Buscas el máximo rendimiento en código muy crítico.
- Un método o API exige recibir un arreglo.

**Usa `List<T>` cuando:**

- **No sabes cuántos elementos** habrá (datos que ingresa el usuario, registros de una base de datos).
- Vas a **agregar o eliminar** elementos con frecuencia.
- Quieres aprovechar métodos como `Contains`, `Find`, `RemoveAll`, `Sort`.
- Estás modelando conjuntos de objetos en POO (alumnos de un grupo, productos de un carrito).

```
¿Cambia la cantidad de elementos durante el programa?
 ├── Sí ─────────────────────────────────────────► List<T>
 └── No
      └── ¿Necesitas filas y columnas?
           ├── Sí ─────────────────────────────────► arreglo [,]
           └── No ─────────────────────────────────► arreglo T[]  (o List<T> si prefieres sus métodos)
```

#### Convertir entre ambos

```csharp
int[] arreglo = { 1, 2, 3 };
List<int> lista = new List<int>(arreglo);   // arreglo → lista
List<int> lista2 = arreglo.ToList();        // con LINQ

int[] deVuelta = lista.ToArray();           // lista → arreglo
```

---

## 4.2. `List<T>`: uso y métodos principales

### 4.2.1. Declaración e inicialización

```csharp
// Lista vacía
List<int> numeros = new List<int>();

// Con tipo inferido
var precios = new List<decimal>();

// Sintaxis abreviada (C# 9+)
List<string> ciudades = new();

// Con valores iniciales (inicializador de colección)
List<string> frutas = new List<string> { "Manzana", "Pera", "Uva" };

// Expresión de colección (C# 12+)
List<int> pares = [2, 4, 6, 8];

// Con capacidad inicial
List<double> lecturas = new List<double>(500);

// A partir de un arreglo u otra colección
string[] arr = { "a", "b", "c" };
List<string> letras = new List<string>(arr);
```

#### Listas de objetos

```csharp
public class Producto
{
    public string Nombre { get; set; }
    public decimal Precio { get; set; }
    public int Stock { get; set; }

    public Producto(string nombre, decimal precio, int stock)
    {
        Nombre = nombre;
        Precio = precio;
        Stock = stock;
    }

    public override string ToString() => $"{Nombre} - {Precio:C} ({Stock} pzas)";
}
```

```csharp
List<Producto> inventario = new List<Producto>
{
    new Producto("Teclado", 450m, 10),
    new Producto("Mouse", 220m, 25),
    new Producto("Monitor", 3200m, 4)
};
```

#### Acceso por índice

```csharp
Console.WriteLine(frutas[0]);              // → Manzana
frutas[1] = "Mango";                       // modifica
Console.WriteLine(frutas[^1]);             // → Uva (último)
Console.WriteLine(frutas.Count);           // → 3

// Console.WriteLine(frutas[5]);   ⚠️ ArgumentOutOfRangeException
```

### 4.2.2. Agregar y eliminar elementos

#### Agregar

| Método | Descripción |
|---|---|
| `Add(x)` | Agrega `x` al final |
| `AddRange(colección)` | Agrega varios elementos al final |
| `Insert(i, x)` | Inserta `x` en la posición `i` (desplaza los demás) |
| `InsertRange(i, colección)` | Inserta varios a partir de `i` |

```csharp
var tareas = new List<string>();

tareas.Add("Estudiar");                                // [Estudiar]
tareas.AddRange(new[] { "Lavar", "Comprar" });         // [Estudiar, Lavar, Comprar]
tareas.Insert(0, "Desayunar");                         // [Desayunar, Estudiar, Lavar, Comprar]
tareas.InsertRange(2, new[] { "Correr", "Leer" });     // [Desayunar, Estudiar, Correr, Leer, Lavar, Comprar]
```

#### Eliminar

| Método | Descripción | Devuelve |
|---|---|---|
| `Remove(x)` | Elimina la **primera** aparición de `x` | `bool` (si lo encontró) |
| `RemoveAt(i)` | Elimina el elemento en la posición `i` | — |
| `RemoveRange(i, n)` | Elimina `n` elementos desde `i` | — |
| `RemoveAll(cond)` | Elimina todos los que cumplen la condición | `int` (cuántos eliminó) |
| `Clear()` | Vacía la lista | — |

```csharp
var nums = new List<int> { 5, 3, 8, 3, 10, 1, 7 };

nums.Remove(3);                        // [5, 8, 3, 10, 1, 7]  (solo el primero)
nums.RemoveAt(0);                      // [8, 3, 10, 1, 7]
int quitados = nums.RemoveAll(n => n < 5);   // [8, 10, 7]   quitados = 2
nums.RemoveRange(0, 2);                // [7]
nums.Clear();                          // []
```

```csharp
bool eliminado = tareas.Remove("Nadar");
if (!eliminado)
    Console.WriteLine("Esa tarea no existe");
```

> ⚠️ **No elimines elementos dentro de un `foreach` sobre la misma lista**: lanza `InvalidOperationException` ("Collection was modified").
>
> ```csharp
> foreach (int n in nums)
>     if (n < 0) nums.Remove(n);   // ⚠️ error en tiempo de ejecución
> ```
>
> Soluciones:
>
> ```csharp
> nums.RemoveAll(n => n < 0);                // la más simple
>
> for (int i = nums.Count - 1; i >= 0; i--)  // for recorriendo al revés
>     if (nums[i] < 0) nums.RemoveAt(i);
> ```

### 4.2.3. Búsqueda y ordenamiento

#### Búsqueda

| Método | Descripción | Devuelve |
|---|---|---|
| `Contains(x)` | ¿Existe `x`? | `bool` |
| `IndexOf(x)` | Índice de la primera aparición | `int` (−1 si no está) |
| `LastIndexOf(x)` | Índice de la última aparición | `int` |
| `Exists(cond)` | ¿Algún elemento cumple? | `bool` |
| `Find(cond)` | Primer elemento que cumple | `T` (o valor por defecto/`null`) |
| `FindLast(cond)` | Último elemento que cumple | `T` |
| `FindAll(cond)` | Todos los que cumplen | `List<T>` |
| `FindIndex(cond)` | Índice del primero que cumple | `int` |
| `TrueForAll(cond)` | ¿Todos cumplen? | `bool` |
| `BinarySearch(x)` | Búsqueda binaria (**lista ordenada**) | `int` |

```csharp
var edades = new List<int> { 17, 22, 15, 30, 22, 41 };

edades.Contains(30);                       // → true
edades.IndexOf(22);                        // → 1
edades.LastIndexOf(22);                    // → 4
edades.Exists(e => e >= 40);               // → true
edades.Find(e => e > 20);                  // → 22
edades.FindAll(e => e < 18);               // → [17, 15]
edades.FindIndex(e => e > 25);             // → 3
edades.TrueForAll(e => e > 10);            // → true
```

Con objetos:

```csharp
Producto? mouse = inventario.Find(p => p.Nombre == "Mouse");
if (mouse != null)
    Console.WriteLine(mouse);   // → Mouse - $220.00 (25 pzas)

List<Producto> pocos = inventario.FindAll(p => p.Stock < 5);
bool hayCaros = inventario.Exists(p => p.Precio > 3000);
```

> ⚠️ `Find` devuelve `null` (para objetos) o el valor por defecto (por ejemplo `0` para `int`) si no encuentra nada. Comprueba el resultado antes de usarlo.

#### Ordenamiento

| Método | Descripción |
|---|---|
| `Sort()` | Ordena ascendentemente (orden natural del tipo) |
| `Sort(comparación)` | Ordena con un criterio personalizado |
| `Reverse()` | Invierte el orden actual |

```csharp
var nums = new List<int> { 42, 7, 19, 3 };

nums.Sort();                             // [3, 7, 19, 42]
nums.Reverse();                          // [42, 19, 7, 3]
nums.Sort((a, b) => b.CompareTo(a));     // descendente directo: [42, 19, 7, 3]

var nombres = new List<string> { "pedro", "Ana", "luis" };
nombres.Sort(StringComparer.OrdinalIgnoreCase);   // [Ana, luis, pedro]
```

`CompareTo` devuelve un número negativo si `a < b`, `0` si son iguales y positivo si `a > b`.

Ordenar objetos por una propiedad:

```csharp
inventario.Sort((a, b) => a.Precio.CompareTo(b.Precio));       // por precio ascendente
inventario.Sort((a, b) => b.Stock.CompareTo(a.Stock));         // por stock descendente
inventario.Sort((a, b) => a.Nombre.CompareTo(b.Nombre));       // alfabético
```

> `Sort()` sin argumentos sobre una lista de objetos propios lanza una excepción, porque .NET no sabe cómo compararlos. Usa una lambda como en el ejemplo, o implementa `IComparable<T>` en la clase (tema de POO más adelante).

#### Alternativa con LINQ

LINQ (`using System.Linq;`) ofrece métodos que **no modifican** la lista original sino que devuelven una nueva secuencia:

```csharp
var baratos = inventario.Where(p => p.Precio < 1000).ToList();
var ordenados = inventario.OrderBy(p => p.Nombre).ToList();
var porPrecioDesc = inventario.OrderByDescending(p => p.Precio).ToList();
decimal totalInventario = inventario.Sum(p => p.Precio * p.Stock);
double promedioEdad = edades.Average();
int maxEdad = edades.Max();
```

| `List<T>` (modifica la lista) | LINQ (crea una nueva) |
|---|---|
| `lista.Sort(...)` | `lista.OrderBy(...)` |
| `lista.FindAll(...)` | `lista.Where(...)` |
| `lista.RemoveAll(...)` | `lista.Where(x => !cond)` |

### 4.2.4. Recorrido de listas

#### Con `foreach` (solo lectura)

```csharp
var frutas = new List<string> { "Manzana", "Pera", "Uva" };

foreach (string f in frutas)
    Console.WriteLine(f);
```

#### Con `for` (cuando necesitas el índice o modificar)

```csharp
for (int i = 0; i < frutas.Count; i++)        // ⚠️ Count, no Length
    Console.WriteLine($"{i + 1}. {frutas[i]}");

for (int i = 0; i < frutas.Count; i++)
    frutas[i] = frutas[i].ToUpper();          // modificar elementos
```

#### Con el método `ForEach`

```csharp
frutas.ForEach(f => Console.WriteLine($"- {f}"));
```

#### Modificar objetos dentro de un `foreach`

Con listas de objetos sí puedes cambiar sus **propiedades** dentro de un `foreach` (lo que no puedes es agregar/eliminar elementos ni reemplazar la variable):

```csharp
foreach (Producto p in inventario)
{
    p.Precio *= 1.10m;   // ✓ aumenta 10 % a todos
}
```

#### Comparación de formas de recorrer

| Forma | Índice | Modificar elementos | Eliminar mientras recorre | Legibilidad |
|---|---|---|---|---|
| `foreach` | ✗ | Solo propiedades de objetos | ✗ | Alta |
| `for` | ✓ | ✓ | ✓ (recorriendo al revés) | Media |
| `lista.ForEach(...)` | ✗ | Solo propiedades de objetos | ✗ | Alta (una línea) |

#### Ejemplo integrador: gestor de alumnos

```csharp
var alumnos = new List<string>();
int opcion;

do
{
    Console.WriteLine("\n1. Agregar  2. Eliminar  3. Buscar  4. Listar ordenado  0. Salir");
    Console.Write("Opción: ");
    int.TryParse(Console.ReadLine(), out opcion);

    switch (opcion)
    {
        case 1:
            Console.Write("Nombre: ");
            string nuevo = Console.ReadLine()!.Trim();
            if (string.IsNullOrWhiteSpace(nuevo))
                Console.WriteLine("Nombre vacío");
            else if (alumnos.Contains(nuevo))
                Console.WriteLine("Ya existe");
            else
                alumnos.Add(nuevo);
            break;

        case 2:
            Console.Write("Nombre a eliminar: ");
            if (alumnos.Remove(Console.ReadLine()!.Trim()))
                Console.WriteLine("Eliminado");
            else
                Console.WriteLine("No encontrado");
            break;

        case 3:
            Console.Write("Texto a buscar: ");
            string texto = Console.ReadLine()!.ToLower();
            var resultados = alumnos.FindAll(a => a.ToLower().Contains(texto));
            Console.WriteLine(resultados.Count > 0
                ? string.Join(", ", resultados)
                : "Sin coincidencias");
            break;

        case 4:
            alumnos.Sort();
            for (int i = 0; i < alumnos.Count; i++)
                Console.WriteLine($"{i + 1}. {alumnos[i]}");
            Console.WriteLine($"Total: {alumnos.Count}");
            break;
    }
} while (opcion != 0);
```

---

## Resumen

- Los arreglos tienen tamaño **fijo**; las colecciones como `List<T>` crecen y se reducen según se necesite.
- Usa arreglos para cantidades conocidas y matrices; usa `List<T>` cuando la cantidad de elementos cambia.
- En una lista el número de elementos es `Count` (no `Length`); `Capacity` es el espacio reservado.
- Agregar: `Add`, `AddRange`, `Insert`. Eliminar: `Remove`, `RemoveAt`, `RemoveAll`, `Clear`.
- Buscar: `Contains`, `IndexOf`, `Find`, `FindAll`, `Exists`. Ordenar: `Sort` (con lambda para objetos) y `Reverse`.
- Recorre con `foreach` para leer y con `for` para usar índices; nunca agregues ni elimines dentro de un `foreach` sobre la misma lista.

---

## Ejercicios

> Resuélvelos en `practica/Unidad4/`.

1. **Arreglo vs lista.** Lee números hasta que el usuario escriba `0`. Hazlo primero con un arreglo (usando `Array.Resize`) y luego con `List<int>`. Compara ambos códigos.
2. **Lista de compras.** Menú para agregar, eliminar, marcar como comprado y mostrar los artículos ordenados alfabéticamente.
3. **Filtrar pares.** A partir de una lista de 20 números aleatorios, crea otra solo con los pares (`FindAll`) y elimina de la original los menores que 10 (`RemoveAll`).
4. **Sin duplicados.** Lee palabras y guárdalas en una lista solo si no existen (`Contains`), sin distinguir mayúsculas.
5. **Inventario.** Con la clase `Producto`, implementa: alta, baja por nombre, búsqueda por nombre, listado ordenado por precio y valor total del inventario.
6. **Top 3.** Dada una lista de calificaciones, muestra las tres más altas sin modificar la lista original.
7. **Fusionar listas.** Une dos listas ordenadas en una tercera también ordenada, sin usar `Sort` al final.
8. **Registro de alumnos.** Clase `Alumno` (nombre, matrícula, promedio). Muestra los aprobados, el alumno con mejor promedio y la lista ordenada por promedio descendente.
9. **Eliminación segura.** Demuestra el error de eliminar dentro de un `foreach` y corrígelo de dos formas distintas.

# Unidad 4 — Colecciones

El arreglo resuelve el problema de guardar muchos datos bajo un mismo nombre, pero tiene una limitación fuerte: su **tamaño es fijo**. En la mayoría de los programas reales no se sabe de antemano cuántos datos habrá: alumnos que se inscriben y se dan de baja, productos que se agregan a un carrito, registros que llegan de una base de datos. Las **colecciones** son clases de .NET que administran la memoria por nosotros y permiten **crecer y reducirse** según se necesite. La más usada es `List<T>`. Todos los ejemplos están en **C#**.

---

## Índice

1. [4.1. Diferencias entre arreglos y colecciones](#41-diferencias-entre-arreglos-y-colecciones)
   - [4.1.1. Tamaño fijo frente a tamaño dinámico](#411-tamaño-fijo-frente-a-tamaño-dinámico)
   - [4.1.2. Criterios para elegir entre arreglo y colección](#412-criterios-para-elegir-entre-arreglo-y-colección)
2. [4.2. List\<T\>: uso y métodos principales](#42-listt-uso-y-métodos-principales)
   - [4.2.1. Declaración e inicialización](#421-declaración-e-inicialización)
   - [4.2.2. Agregar y eliminar elementos](#422-agregar-y-eliminar-elementos)
   - [4.2.3. Búsqueda y ordenamiento](#423-búsqueda-y-ordenamiento)
   - [4.2.4. Recorrido de listas](#424-recorrido-de-listas)

---

## 4.1. Diferencias entre arreglos y colecciones

Una **colección** es un **objeto** cuyo trabajo es agrupar otros objetos y ofrecer operaciones para administrarlos: agregar, quitar, buscar, ordenar, recorrer. A diferencia del arreglo, que es una estructura del lenguaje con muy pocas operaciones propias, una colección es una **clase** con métodos, y es ella quien decide cómo guardar internamente los datos.

```
Arreglo: estructura básica               Colección: objeto que administra datos

int[] arr = new int[4];                  List<int> lista = new List<int>();

[ 0 | 0 | 0 | 0 ]                        ┌─────────────── List<int> ───────────────┐
 tú controlas cada posición              │ datos internos: [ 5 | 8 |   |   ]       │
                                         │ Count = 2                               │
                                         │ Add() Remove() Contains() Sort() ...    │
                                         └─────────────────────────────────────────┘
                                          tú pides operaciones; la lista se encarga
```

**Colecciones genéricas.** Las colecciones modernas de .NET están en `System.Collections.Generic` (incluido por defecto en los proyectos actuales) y son **genéricas**: se escriben con un **parámetro de tipo** entre `< >` que indica qué tipo de elementos guardan.

```
List<T>
     └── T = "tipo": se reemplaza por el tipo real al usarla

List<int>       → lista de enteros
List<string>    → lista de cadenas
List<Alumno>    → lista de objetos de la clase Alumno
```

Gracias a los genéricos, el compilador **garantiza** que solo entren elementos del tipo indicado y no hace falta convertir al leerlos.

**Colecciones no genéricas vs genéricas.** Antes de los genéricos existía `ArrayList`, que guarda todo como `object`. Hoy **no se recomienda**:

| Aspecto | `ArrayList` (no genérica) | `List<T>` (genérica) |
|---|---|---|
| Tipo de elementos | `object` (cualquier cosa) | Solo `T` |
| Seguridad de tipos | Errores en tiempo de ejecución | Errores en compilación |
| Conversión al leer | Necesita casting: `(int)lista[0]` | No necesita |
| Boxing con tipos por valor | Sí (lento) | No |

```csharp
using System.Collections;

ArrayList vieja = new ArrayList();
vieja.Add(10);
vieja.Add("texto");           // compila: mezcla tipos sin avisar
int x = (int)vieja[1];        // InvalidCastException al ejecutar

List<int> nueva = new List<int>();
nueva.Add(10);
// nueva.Add("texto");        // ERROR de compilación: el error se detecta antes
```

**Familia de colecciones genéricas de .NET**

| Colección | Qué es | Cómo se accede |
|---|---|---|
| `List<T>` | Lista dinámica (arreglo redimensionable) | Por índice: `lista[i]` |
| `Dictionary<TKey, TValue>` | Pares clave-valor | Por clave: `dic["ana"]` |
| `Stack<T>` | Pila (último en entrar, primero en salir) | Solo el tope |
| `Queue<T>` | Cola (primero en entrar, primero en salir) | Solo el frente |
| `HashSet<T>` | Conjunto sin elementos repetidos | Por pertenencia: `set.Contains(x)` |
| `LinkedList<T>` | Lista doblemente enlazada | Recorriendo nodos |

> En esta unidad se estudia `List<T>`, que es la colección de uso general y el reemplazo natural del arreglo. Las demás resuelven problemas más específicos.

### 4.1.1. Tamaño fijo frente a tamaño dinámico

**Arreglo: tamaño fijo.** El tamaño se decide al crear el arreglo y la memoria reservada es exactamente esa. Si hace falta un lugar más, hay que crear un arreglo nuevo y copiar todo a mano.

```csharp
string[] nombres = new string[3];
nombres[0] = "Ana";
nombres[1] = "Luis";
nombres[2] = "Marta";
// nombres[3] = "Pedro";        // IndexOutOfRangeException: no hay lugar

Array.Resize(ref nombres, 4);   // crea un arreglo nuevo de 4 y copia
nombres[3] = "Pedro";
```

Además, el arreglo no lleva la cuenta de cuántas posiciones están "en uso": si creas un arreglo de 100 y solo llenas 30, `Length` sigue siendo 100, y eres tú quien debe recordar cuántos elementos válidos hay.

**`List<T>`: tamaño dinámico.** La lista crece al agregar y se reduce al eliminar. Lleva la cuenta de sus elementos en `Count`.

```csharp
List<string> nombres = new List<string>();
nombres.Add("Ana");
nombres.Add("Luis");
nombres.Add("Marta");
nombres.Add("Pedro");          // crece sola
nombres.Remove("Luis");        // y se reduce

Console.WriteLine(nombres.Count);   // 3
```

**¿Cómo crece una lista por dentro?** `List<T>` no hace magia: internamente guarda los elementos en un **arreglo** común. La diferencia es que ese arreglo tiene **espacio de sobra**, y cuando se llena, la lista crea otro arreglo del **doble de capacidad**, copia los elementos y desecha el viejo. Todo esto ocurre automáticamente dentro de `Add`.

Por eso la lista tiene dos medidas:

| Propiedad | Significado |
|---|---|
| `Count` | Cuántos elementos **tiene** la lista (los que tú agregaste) |
| `Capacity` | Cuántos **caben** en el arreglo interno antes de tener que crecer |

```
Count = 4, Capacity = 4                     Add(50) → no cabe → se duplica

[ 10 | 20 | 30 | 40 ]            ──►        [ 10 | 20 | 30 | 40 | 50 |    |    |    ]
                                             Count = 5, Capacity = 8

1. se crea un arreglo nuevo de capacidad 8
2. se copian los 4 elementos
3. se agrega el 50
4. el arreglo viejo queda sin uso (el recolector de basura lo libera)
```

```csharp
var lista = new List<int>();
Console.WriteLine($"{lista.Count} / {lista.Capacity}");   // 0 / 0
lista.Add(1);
Console.WriteLine($"{lista.Count} / {lista.Capacity}");   // 1 / 4
for (int i = 2; i <= 5; i++) lista.Add(i);
Console.WriteLine($"{lista.Count} / {lista.Capacity}");   // 5 / 8
for (int i = 6; i <= 9; i++) lista.Add(i);
Console.WriteLine($"{lista.Count} / {lista.Capacity}");   // 9 / 16
```

| Elementos agregados | Capacity | ¿Hubo que copiar? |
|---|---|---|
| 1 | 4 | Sí (se crea el primer arreglo) |
| 2 – 4 | 4 | No |
| 5 | 8 | Sí (copia 4) |
| 6 – 8 | 8 | No |
| 9 | 16 | Sí (copia 8) |
| 10 – 16 | 16 | No |

> **¿Por qué duplicar y no crecer de uno en uno?** Si creciera de uno en uno, cada `Add` copiaría todos los elementos: agregar `n` elementos costaría ~n²/2 copias (el mismo problema que concatenar `string` en un ciclo). Al duplicar, las copias son cada vez más raras y en total suman menos de `2n`. Por eso se dice que `Add` es **O(1) amortizado**: casi siempre es instantáneo, y el costo de las copias ocasionales, repartido entre todos los `Add`, es constante.

Si sabes aproximadamente cuántos elementos tendrás, puedes reservar la capacidad desde el inicio y evitar todas las copias:

```csharp
var lista = new List<int>(1000);     // Capacity = 1000, Count = 0
```

> `Capacity` no son elementos reales. Aunque la capacidad sea 8, si `Count` es 5, acceder a `lista[6]` lanza `ArgumentOutOfRangeException`.

**Comparación general**

| Aspecto | Arreglo `T[]` | `List<T>` |
|---|---|---|
| Tamaño | Fijo al crearlo | Dinámico, crece solo |
| Número de elementos | `Length` (siempre el total reservado) | `Count` (los elementos reales) |
| Agregar / eliminar | No se puede (hay que crear otro arreglo) | `Add`, `Insert`, `Remove`, `RemoveAt` |
| Acceso por índice | `arr[i]`, O(1) | `lista[i]`, O(1) |
| Métodos | Pocos, estáticos en `Array` (`Array.Sort(arr)`) | Muchos, de instancia (`lista.Sort()`) |
| Memoria | Exacta | Puede tener espacio extra reservado (`Capacity`) |
| Rendimiento | Ligeramente más rápido | Casi igual; costo extra al redimensionar |
| Multidimensional | Sí (`T[,]`) | No directamente (se usa `List<List<T>>`) |

### 4.1.2. Criterios para elegir entre arreglo y colección

La pregunta principal es: **¿la cantidad de elementos cambia mientras el programa se ejecuta?**

```
¿Cambia la cantidad de elementos durante la ejecución?
 ├── Sí ─────────────────────────────────────────────► List<T>
 └── No
      └── ¿Necesitas filas y columnas?
           ├── Sí ───────────────────────────────────► arreglo T[,]
           └── No
                └── ¿Necesitas buscar, ordenar, filtrar seguido?
                     ├── Sí ─────────────────────────► List<T> (por sus métodos)
                     └── No ─────────────────────────► arreglo T[]
```

**Usa un arreglo cuando:**

| Criterio | Ejemplo |
|---|---|
| La cantidad es **conocida y no cambia** | Días de la semana (7), meses (12), las 5 calificaciones de un parcial |
| Trabajas con datos en **filas y columnas** | Tablero 3 × 3, matriz de ventas, imagen |
| El rendimiento es crítico y el tamaño es fijo | Cálculos numéricos intensivos |
| Un método o API **exige** un arreglo | `string.Split` regresa `string[]`; `Main(string[] args)` |

**Usa `List<T>` cuando:**

| Criterio | Ejemplo |
|---|---|
| **No sabes** cuántos elementos habrá | Datos que captura el usuario hasta que escriba "fin" |
| Vas a **agregar o eliminar** con frecuencia | Carrito de compras, lista de tareas, inscripciones |
| Quieres usar **métodos** de búsqueda y filtrado | `Contains`, `Find`, `FindAll`, `RemoveAll` |
| Modelas un **conjunto de objetos** en POO | Los alumnos de un grupo, los productos de un inventario |

**Ejemplo: el mismo problema con ambos.** Leer números hasta que el usuario escriba `0`:

```csharp
// Con arreglo: hay que adivinar el tamaño o redimensionar a mano
int[] arr = new int[0];
int n;
while ((n = int.Parse(Console.ReadLine())) != 0)
{
    Array.Resize(ref arr, arr.Length + 1);   // copia TODO el arreglo en cada número
    arr[^1] = n;
}

// Con lista: natural
var lista = new List<int>();
while ((n = int.Parse(Console.ReadLine())) != 0)
    lista.Add(n);
```

> Con el arreglo, cada número nuevo crea un arreglo nuevo y copia todos los anteriores. Con la lista, las copias solo ocurren al duplicar la capacidad.

**Convertir entre ambos.** Es común recibir un arreglo y necesitar una lista (o al revés):

```csharp
int[] arreglo = { 5, 3, 1 };
List<int> lista = new List<int>(arreglo);   // arreglo → lista (copia)
List<int> otra = arreglo.ToList();          // con LINQ (using System.Linq)
int[] deVuelta = lista.ToArray();           // lista → arreglo (copia)
```

> Las conversiones **copian** los elementos: modificar la lista después no afecta al arreglo original.

---

## 4.2. List\<T\>: uso y métodos principales

`List<T>` (en `System.Collections.Generic`) es una **colección genérica dinámica**: funciona como un arreglo —acceso por índice, elementos en orden— pero **crece y se reduce automáticamente** y ofrece decenas de métodos para administrar sus elementos.

Características:

| Característica | Descripción |
|---|---|
| Genérica | Solo acepta elementos del tipo `T`, verificado al compilar |
| Dinámica | Crece al agregar (duplica su capacidad) y se reduce al eliminar |
| Ordenada | Los elementos mantienen el orden en que se agregaron (o insertaron) |
| Indexada | Acceso directo por posición `lista[i]`, de `0` a `Count - 1`, en O(1) |
| Permite duplicados | El mismo valor puede aparecer varias veces |
| Permite `null` | Si `T` es un tipo por referencia |
| Tipo por referencia | La variable guarda una referencia al objeto lista |

### 4.2.1. Declaración e inicialización

Una lista es un **objeto**, así que hay que **crearla** con `new` antes de usarla. Declarar la variable sin crearla deja una referencia vacía.

```
List<int> numeros;                    List<int> numeros = new List<int>();

 numeros: null                         numeros: ●──►  [ ]  Count = 0
 numeros.Add(5) → NullReferenceException              (lista vacía, lista para usar)
```

| Forma | Sintaxis | Resultado |
|---|---|---|
| Vacía | `List<int> numeros = new List<int>();` | `Count = 0` |
| Con `var` | `var nombres = new List<string>();` | Tipo inferido |
| `new()` con tipo objetivo (C# 9) | `List<double> precios = new();` | El tipo se toma de la declaración |
| Con capacidad inicial | `new List<int>(100)` | `Count = 0`, `Capacity = 100` |
| Con inicializador | `new List<string> { "Manzana", "Pera" }` | `Count = 2` |
| Expresión de colección (C# 12) | `List<int> pares = [2, 4, 6, 8];` | `Count = 4` |
| Desde otra colección | `new List<int>(arreglo)` | Copia los elementos |

```csharp
using System.Collections.Generic;   // ya incluido por defecto en proyectos modernos

List<int> numeros = new List<int>();
var nombres = new List<string>();
List<double> precios = new();
List<int> conCapacidad = new List<int>(100);
List<string> frutas = new List<string> { "Manzana", "Pera", "Uva" };
List<int> pares = [2, 4, 6, 8];

int[] arreglo = { 5, 3, 1 };
List<int> desdeArreglo = new List<int>(arreglo);
```

> El inicializador `{ "Manzana", "Pera", "Uva" }` no es magia: el compilador lo traduce a una lista vacía seguida de un `Add` por cada elemento.

**Acceso por índice.** Igual que en un arreglo, con la diferencia de que el límite es `Count`:

```csharp
Console.WriteLine(frutas[0]);          // Manzana
frutas[1] = "Mango";                   // reemplaza Pera por Mango
Console.WriteLine(frutas[^1]);         // Uva (último)
Console.WriteLine(frutas.Count);       // 3

// Console.WriteLine(frutas[3]);       // ArgumentOutOfRangeException (en listas no es IndexOutOfRange)
```

> El indexador **solo reemplaza** elementos existentes; no agrega. `frutas[3] = "Kiwi"` en una lista de 3 elementos lanza excepción. Para agregar se usa `Add`.

**Listas de objetos.** En POO, el uso más común de `List<T>` es guardar objetos de una clase propia:

```csharp
class Producto
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

var inventario = new List<Producto>
{
    new Producto("Teclado", 450m, 10),
    new Producto("Mouse", 220m, 25),
    new Producto("Monitor", 3200m, 4)
};

Console.WriteLine(inventario[1]);          // Mouse - $220.00 (25 pzas)
Console.WriteLine(inventario[1].Precio);   // 220
```

```
inventario: ●──► [ ● | ● | ● ]          la lista guarda REFERENCIAS
                   │   │   │
                   │   │   └──► Producto { Monitor, 3200, 4 }
                   │   └──────► Producto { Mouse, 220, 25 }
                   └──────────► Producto { Teclado, 450, 10 }
```

> Como la lista guarda referencias, si sacas un producto de la lista a una variable y lo modificas (`var p = inventario[1]; p.Stock = 0;`), el cambio se ve en la lista: es el **mismo** objeto.

### 4.2.2. Agregar y eliminar elementos

**Agregar**

| Método | Qué hace | Complejidad |
|---|---|---|
| `Add(x)` | Agrega `x` al **final** | O(1) amortizado |
| `AddRange(col)` | Agrega todos los elementos de otra colección al final | O(k) |
| `Insert(i, x)` | Inserta `x` en la posición `i`; los demás se recorren | O(n) |
| `InsertRange(i, col)` | Inserta varios a partir de `i` | O(n + k) |

```csharp
var frutas = new List<string> { "Manzana", "Pera" };

frutas.Add("Uva");                               // [Manzana, Pera, Uva]
frutas.Insert(0, "Kiwi");                        // [Kiwi, Manzana, Pera, Uva]
frutas.Insert(2, "Mango");                       // [Kiwi, Manzana, Mango, Pera, Uva]
frutas.AddRange(new[] { "Fresa", "Piña" });      // [..., Uva, Fresa, Piña]
```

**¿Por qué `Insert` es más lento que `Add`?** Como internamente es un arreglo contiguo, para abrir un hueco en medio hay que **recorrer una posición a la derecha** todos los elementos que están después. `Add` escribe al final, donde ya hay espacio libre, sin mover nada.

```
Insert(2, "Mango"): los elementos desde el índice 2 se recorren una posición

antes:   [ Kiwi | Manzana | Pera  | Uva  |      ]
                            ──►     ──►
después: [ Kiwi | Manzana | Mango | Pera | Uva  ]
```

> Insertar al **inicio** (`Insert(0, x)`) es el peor caso: hay que mover **todos** los elementos. Si lo haces seguido con listas grandes, considera otra estructura (por ejemplo, agregar al final y luego invertir).

**Eliminar**

| Método | Qué hace | Retorna | Complejidad |
|---|---|---|---|
| `Remove(x)` | Elimina la **primera** aparición del valor `x` | `bool` (si lo encontró) | O(n) |
| `RemoveAt(i)` | Elimina el elemento en la **posición** `i` | `void` | O(n) |
| `RemoveRange(i, n)` | Elimina `n` elementos desde `i` | `void` | O(n) |
| `RemoveAll(cond)` | Elimina **todos** los que cumplan la condición | `int` (cuántos eliminó) | O(n) |
| `Clear()` | Elimina todo (`Count = 0`; `Capacity` se conserva) | `void` | O(n) |

```csharp
var nums = new List<int> { 5, 3, 8, 3, 1, 9 };

bool quitado = nums.Remove(3);             // true → [5, 8, 3, 1, 9] (solo el primer 3)
bool otro = nums.Remove(100);              // false, no lanza excepción
nums.RemoveAt(0);                          // [8, 3, 1, 9]
int cuantos = nums.RemoveAll(n => n < 5);  // 2 → [8, 9]
nums.Clear();                              // []
```

Al eliminar ocurre lo contrario que al insertar: los elementos posteriores se **recorren a la izquierda** para cerrar el hueco, y sus índices disminuyen en 1.

```
RemoveAt(1):

antes:   [ 8 | 3 | 1 | 9 ]
                 ◄── ◄──
después: [ 8 | 1 | 9 ]          el 1 pasó del índice 2 al 1; el 9 del 3 al 2
```

> **`Remove(x)` recibe el valor; `RemoveAt(i)` recibe la posición.** Con `List<int>` es fácil confundirlos, porque ambos reciben un `int`: en `[10, 20, 30]`, `Remove(1)` no hace nada (no hay un 1), mientras que `RemoveAt(1)` quita el 20.

**Usar el valor de retorno.** `Remove` regresa `bool`, lo que permite saber si el elemento existía sin buscarlo antes:

```csharp
var tareas = new List<string> { "Estudiar", "Lavar", "Comprar" };

if (tareas.Remove("Nadar"))
    Console.WriteLine("Tarea eliminada");
else
    Console.WriteLine("Esa tarea no existe");     // ← se imprime esto
```

**Eliminar objetos con una condición**

```csharp
int agotados = inventario.RemoveAll(p => p.Stock == 0);
Console.WriteLine($"Se eliminaron {agotados} productos agotados");
```

### 4.2.3. Búsqueda y ordenamiento

**Buscar**

| Método | Qué hace | Retorna | Complejidad |
|---|---|---|---|
| `Contains(x)` | ¿Está `x` en la lista? | `bool` | O(n) |
| `IndexOf(x)` | Posición de la primera aparición o `-1` | `int` | O(n) |
| `LastIndexOf(x)` | Posición de la última aparición o `-1` | `int` | O(n) |
| `Exists(cond)` | ¿Algún elemento cumple? | `bool` | O(n) |
| `Find(cond)` | Primer elemento que cumple (o `default`) | `T` | O(n) |
| `FindLast(cond)` | Último elemento que cumple | `T` | O(n) |
| `FindAll(cond)` | Todos los que cumplen (lista nueva) | `List<T>` | O(n) |
| `FindIndex(cond)` | Índice del primero que cumple o `-1` | `int` | O(n) |
| `TrueForAll(cond)` | ¿Todos cumplen? | `bool` | O(n) |
| `BinarySearch(x)` | Búsqueda binaria (**lista ordenada**) | `int` | O(log n) |

> Todos los métodos de búsqueda, excepto `BinarySearch`, hacen una **búsqueda lineal** internamente: recorren la lista desde el inicio. No son "más rápidos" que un `for` escrito a mano; solo son más cortos y menos propensos a errores.

**Buscar por valor vs buscar por condición.** `Contains` e `IndexOf` buscan un **valor exacto**. `Exists`, `Find`, `FindAll` y `FindIndex` reciben una **condición** (expresión lambda) que se aplica a cada elemento:

```
nums.Find(n => n > 26)
          │    └───┬───┘
          │        └── condición: se evalúa con cada elemento
          └── n: el elemento actual

Para [10, 25, 30, 25, 40]:
n = 10 → 10 > 26 false
n = 25 → 25 > 26 false
n = 30 → 30 > 26 true  → regresa 30 y deja de buscar
```

```csharp
var nums = new List<int> { 10, 25, 30, 25, 40 };

Console.WriteLine(nums.Contains(30));                  // True
Console.WriteLine(nums.IndexOf(25));                   // 1
Console.WriteLine(nums.LastIndexOf(25));               // 3
Console.WriteLine(nums.IndexOf(99));                   // -1
Console.WriteLine(nums.Exists(n => n % 2 != 0));       // True (25 es impar)
Console.WriteLine(nums.Find(n => n > 26));             // 30
Console.WriteLine(nums.FindIndex(n => n > 26));        // 2
List<int> mayores = nums.FindAll(n => n >= 25);        // [25, 30, 25, 40]
Console.WriteLine(nums.TrueForAll(n => n > 0));        // True
```

**Buscar objetos.** Con objetos propios, la condición casi siempre revisa una **propiedad**:

```csharp
Producto mouse = inventario.Find(p => p.Nombre == "Mouse");
if (mouse != null)
    Console.WriteLine(mouse);                  // Mouse - $220.00 (25 pzas)

List<Producto> pocos = inventario.FindAll(p => p.Stock < 5);
bool hayCaros = inventario.Exists(p => p.Precio > 3000);
int posMonitor = inventario.FindIndex(p => p.Nombre == "Monitor");   // 2
```

> **`Find` no lanza excepción si no encuentra nada**: regresa el valor por defecto del tipo (`null` para objetos, `0` para `int`). Con objetos, revisa `!= null` antes de usar el resultado. Con `List<int>`, un `0` puede ser "no encontrado" o un `0` real: en ese caso usa `FindIndex` y revisa `-1`.

> `Contains(x)` e `IndexOf(x)` con **objetos de tus clases** comparan **referencias**, no contenido: `inventario.Contains(new Producto("Mouse", 220m, 25))` da `false`, porque es otro objeto aunque tenga los mismos datos. Para buscar por datos usa `Exists` o `Find` con una condición.

**Ordenar**

| Método | Qué hace | Complejidad |
|---|---|---|
| `Sort()` | Ordena ascendentemente según el orden natural del tipo | O(n log n) |
| `Sort(comparación)` | Ordena con un criterio propio | O(n log n) |
| `Reverse()` | Invierte el orden actual | O(n) |

> `Sort` y `Reverse` **modifican la lista original**; no regresan una lista nueva.

```csharp
var nums = new List<int> { 42, 7, 19, 3 };
nums.Sort();                                // [3, 7, 19, 42]
nums.Reverse();                             // [42, 19, 7, 3]
nums.Sort((a, b) => b.CompareTo(a));        // descendente directo: [42, 19, 7, 3]

var nombres = new List<string> { "Luis", "Ana", "Sofía", "Bo" };
nombres.Sort();                                         // alfabético: Ana, Bo, Luis, Sofía
nombres.Sort((a, b) => a.Length.CompareTo(b.Length));  // por largo: Bo, Ana, Luis, Sofía
```

**¿Cómo funciona el criterio de comparación?** `Sort` necesita saber, para cualquier par de elementos `a` y `b`, cuál va primero. La comparación es una función que recibe los dos y regresa un número:

| Resultado de la comparación | Significado |
|---|---|
| Negativo (`< 0`) | `a` va **antes** que `b` |
| Cero (`0`) | Son equivalentes para el orden |
| Positivo (`> 0`) | `a` va **después** que `b` |

`CompareTo` ya regresa exactamente eso, por eso se usa casi siempre:

```csharp
Console.WriteLine(3.CompareTo(7));    // -1 → 3 va antes que 7
Console.WriteLine(7.CompareTo(7));    //  0
Console.WriteLine(9.CompareTo(7));    //  1 → 9 va después que 7
```

```
(a, b) => a.CompareTo(b)    → ascendente
(a, b) => b.CompareTo(a)    → descendente (se invierten los papeles)
```

**Ordenar objetos.** Una lista de objetos propios **no sabe** cómo ordenarse: ¿por nombre?, ¿por precio? Hay que indicarle el criterio comparando una propiedad.

```csharp
inventario.Sort((a, b) => a.Precio.CompareTo(b.Precio));   // precio ascendente
inventario.Sort((a, b) => b.Stock.CompareTo(a.Stock));     // stock descendente
inventario.Sort((a, b) => a.Nombre.CompareTo(b.Nombre));   // alfabético

// Criterio compuesto: por stock, y si empatan, por nombre
inventario.Sort((a, b) =>
{
    int c = a.Stock.CompareTo(b.Stock);
    return c != 0 ? c : a.Nombre.CompareTo(b.Nombre);
});
```

> `inventario.Sort()` **sin** criterio lanza `InvalidOperationException`, porque .NET no sabe comparar dos `Producto`. La alternativa a pasar una lambda es que la clase implemente la interfaz `IComparable<T>`, tema de POO.

**Búsqueda binaria.** Una vez ordenada, la lista permite usar `BinarySearch`, que descarta la mitad de los elementos en cada paso:

```csharp
var ordenada = new List<int> { 3, 7, 19, 25, 42, 50, 61 };
Console.WriteLine(ordenada.BinarySearch(42));   // 4
```

**Alternativa con LINQ.** LINQ (`using System.Linq;`) ofrece métodos que **no modifican** la lista original: regresan una secuencia nueva que se convierte en lista con `ToList()`.

| `List<T>` (modifica la lista) | LINQ (crea una nueva) |
|---|---|
| `lista.Sort(...)` | `lista.OrderBy(x => ...)` / `OrderByDescending` |
| `lista.FindAll(...)` | `lista.Where(x => ...)` |
| `lista.RemoveAll(...)` | `lista.Where(x => !condición)` |

```csharp
var baratos = inventario.Where(p => p.Precio < 1000).ToList();
var porNombre = inventario.OrderBy(p => p.Nombre).ToList();
var porPrecioDesc = inventario.OrderByDescending(p => p.Precio).ToList();
decimal valorTotal = inventario.Sum(p => p.Precio * p.Stock);
int maximo = nums.Max();
double promedio = nums.Average();
```

### 4.2.4. Recorrido de listas

Recorrer una lista funciona igual que con arreglos, con dos diferencias: el límite es **`Count`** (no `Length`) y, como la lista puede cambiar de tamaño, hay que tener cuidado al **agregar o eliminar mientras se recorre**.

| Forma | Ventaja | Limitación |
|---|---|---|
| `for` con índice | Conoces la posición y puedes modificar `lista[i]` | Más verboso |
| `foreach` | Simple y legible | **No** puedes agregar ni eliminar elementos dentro |
| `lista.ForEach(x => …)` | Muy compacto | Mismas limitaciones que `foreach` |
| `for` en reversa | Permite **eliminar** elementos mientras recorres | Hay que cuidar los índices |

```csharp
var nums = new List<int> { 1, 2, 3, 4, 5 };

// for: con índice
for (int i = 0; i < nums.Count; i++)          // Count, no Length
    Console.WriteLine($"[{i}] = {nums[i]}");

// for: modificando elementos
for (int i = 0; i < nums.Count; i++)
    nums[i] = nums[i] * 10;                    // [10, 20, 30, 40, 50]

// foreach: solo lectura
foreach (int n in nums)
    Console.Write(n + " ");

// ForEach: una acción por elemento
nums.ForEach(n => Console.Write(n + " "));
```

**Modificar objetos dentro de un `foreach`.** Con listas de objetos sí puedes cambiar sus **propiedades**, porque la variable de iteración es una copia de la **referencia** y apunta al mismo objeto. Lo que no puedes es reemplazar el objeto ni cambiar la lista.

```csharp
foreach (Producto p in inventario)
    p.Precio *= 1.10m;          // ✓ aumenta 10 % a todos los productos

foreach (Producto p in inventario)
{
    // p = new Producto("X", 0, 0);   // ERROR: no se puede asignar a la variable de iteración
}
```

**El error clásico: modificar la lista dentro de `foreach`.** Mientras un `foreach` recorre una lista, la lista lleva un control interno de "versión". Si se agrega o elimina un elemento durante el recorrido, la versión cambia y el `foreach` lo detecta en la siguiente vuelta, porque ya no puede garantizar que el recorrido sea correcto.

```csharp
var nums = new List<int> { 1, 2, 3, 4, 5 };
foreach (int n in nums)
{
    if (n % 2 == 0)
        nums.Remove(n);   // InvalidOperationException: Collection was modified
}
```

**¿Y con `for` hacia adelante?** No lanza excepción, pero **se salta elementos**: al eliminar en la posición `i`, el siguiente elemento se recorre a la posición `i`, y el `i++` lo brinca.

```csharp
var nums = new List<int> { 2, 4, 5, 6 };
for (int i = 0; i < nums.Count; i++)
    if (nums[i] % 2 == 0)
        nums.RemoveAt(i);
// Resultado: [4, 5]  ✗  (el 4 nunca se revisó)
```

```
i = 0: nums[0] = 2 → par, RemoveAt(0) → [ 4 | 5 | 6 ]   el 4 pasó al índice 0
i = 1: nums[1] = 5 → impar                              ¡el 4 se saltó!
i = 2: nums[2] = 6 → par, RemoveAt(2) → [ 4 | 5 ]
```

**Soluciones correctas**

```csharp
var nums = new List<int> { 1, 2, 3, 4, 5 };

// 1) RemoveAll (la más simple y eficiente)
nums.RemoveAll(n => n % 2 == 0);

// 2) for en reversa: al eliminar, solo se recorren elementos ya revisados
for (int i = nums.Count - 1; i >= 0; i--)
    if (nums[i] % 2 == 0)
        nums.RemoveAt(i);

// 3) Recorrer una copia y modificar la original
foreach (int n in nums.ToList())
    if (n % 2 == 0) nums.Remove(n);
```

> ¿Por qué en reversa sí funciona? Al eliminar en la posición `i`, solo se recorren los elementos **a la derecha** de `i`, y esos ya fueron revisados. Los que faltan por revisar (a la izquierda) no cambian de posición.

**Lista de listas.** El equivalente dinámico de un arreglo escalonado: cada fila es una lista que puede crecer por su cuenta.

```csharp
var tabla = new List<List<int>>
{
    new List<int> { 1, 2, 3 },
    new List<int> { 4, 5 }
};
tabla[1].Add(6);                       // la fila 1 crece
tabla.Add(new List<int> { 7 });        // se agrega una fila nueva

for (int i = 0; i < tabla.Count; i++)
{
    for (int j = 0; j < tabla[i].Count; j++)
        Console.Write(tabla[i][j] + " ");
    Console.WriteLine();
}
// 1 2 3
// 4 5 6
// 7
```

**Ejemplo integrador: gestor de alumnos**

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
            string nuevo = Console.ReadLine().Trim();
            if (string.IsNullOrWhiteSpace(nuevo))
                Console.WriteLine("Nombre vacío");
            else if (alumnos.Contains(nuevo))
                Console.WriteLine("Ya existe");
            else
                alumnos.Add(nuevo);
            break;

        case 2:
            Console.Write("Nombre a eliminar: ");
            Console.WriteLine(alumnos.Remove(Console.ReadLine().Trim()) ? "Eliminado" : "No encontrado");
            break;

        case 3:
            Console.Write("Texto a buscar: ");
            string texto = Console.ReadLine().ToLower();
            var resultados = alumnos.FindAll(a => a.ToLower().Contains(texto));
            Console.WriteLine(resultados.Count > 0 ? string.Join(", ", resultados) : "Sin coincidencias");
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

## Resumen de la unidad

- **Colección:** objeto que agrupa elementos y ofrece operaciones para administrarlos. Las genéricas (`List<T>`) verifican el tipo al compilar y evitan el boxing; las no genéricas (`ArrayList`) ya no se recomiendan.
- **Tamaño fijo vs dinámico:** el arreglo no puede crecer; `List<T>` usa un arreglo interno que **duplica** su capacidad al llenarse, por eso `Add` es O(1) amortizado.
- **`Count` vs `Capacity`:** `Count` son los elementos reales; `Capacity`, el espacio reservado. Los índices válidos van de `0` a `Count - 1`.
- **Elegir:** arreglo para cantidades conocidas y fijas o matrices; `List<T>` cuando la cantidad cambia o se necesitan sus métodos.
- **Agregar y eliminar:** `Add` al final es barato; `Insert` y `RemoveAt` en medio cuestan O(n) porque recorren elementos. `Remove(x)` recibe el valor; `RemoveAt(i)`, la posición.
- **Buscar:** `Contains`/`IndexOf` por valor; `Find`/`FindAll`/`Exists`/`FindIndex` por condición. `Find` regresa `default` si no encuentra nada.
- **Ordenar:** `Sort()` con el orden natural; con objetos, `Sort((a, b) => a.Prop.CompareTo(b.Prop))`. `Sort` y `Reverse` modifican la lista; LINQ (`OrderBy`, `Where`) crea una nueva.
- **Recorrer:** `for` con `Count` para índices o modificar; `foreach` para leer. Nunca agregues ni elimines dentro de `foreach`: usa `RemoveAll` o `for` en reversa.

*Universidad Politécnica de Tecamac — Ingeniería en Software*

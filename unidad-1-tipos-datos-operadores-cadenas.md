# Unidad 1 — Tipos de Datos, Operadores y Cadenas

Todo programa trabaja con **datos**: números, textos, valores de verdad. Antes de hablar de objetos y clases hay que tener claro cómo se **guardan** esos datos en memoria, cómo se **convierten** de un tipo a otro, cómo se **operan** para producir nuevos valores y cómo se **manipula** el texto, que es el dato más usado en cualquier aplicación. Todos los ejemplos están en **C#**.

---

## Índice

1. [1.1. Tipos de datos en programación](#11-tipos-de-datos-en-programación)
   - [1.1.1. Datos primitivos](#111-datos-primitivos)
   - [1.1.2. Conversión de tipos](#112-conversión-de-tipos)
   - [1.1.3. Constantes y variables (const y readonly)](#113-constantes-y-variables-const-y-readonly)
2. [1.2. Operadores](#12-operadores)
   - [1.2.1. Operadores aritméticos](#121-operadores-aritméticos)
   - [1.2.2. Operadores relacionales](#122-operadores-relacionales)
   - [1.2.3. Operadores lógicos](#123-operadores-lógicos)
3. [1.3. Cadenas de caracteres](#13-cadenas-de-caracteres)
   - [1.3.1. String y métodos principales](#131-string-y-métodos-principales)
   - [1.3.2. StringBuilder y manipulación eficiente de cadenas](#132-stringbuilder-y-manipulación-eficiente-de-cadenas)

---

## 1.1. Tipos de datos en programación

Un **tipo de dato** define tres cosas sobre una variable:

1. **Qué valores** puede guardar (enteros, decimales, texto, verdadero/falso…).
2. **Cuánta memoria** ocupa (un `int` siempre ocupa 32 bits).
3. **Qué operaciones** se pueden hacer con ella (puedes multiplicar dos `int`, pero no dos `bool`).

En memoria todo son bits (`0` y `1`). El tipo es lo que le dice al programa **cómo interpretar** esos bits: la misma secuencia `01000001` puede ser el número `65` si es `byte` o la letra `'A'` si es `char`.

C# es un lenguaje **fuertemente tipado** y de **tipado estático**: cada variable tiene un tipo fijo que el compilador revisa **antes** de ejecutar. Si intentas guardar un texto en un `int`, el programa ni siquiera compila.

```csharp
int edad = 20;
// edad = "veinte";   // ERROR de compilación: no se puede convertir string a int
```

| Tipado | Cuándo se revisan los tipos | Ejemplo de lenguaje | Ventaja |
|---|---|---|---|
| **Estático** | Al compilar | C#, Java, C++ | Los errores aparecen antes de ejecutar |
| **Dinámico** | Al ejecutar | Python, JavaScript | Código más corto, pero errores en ejecución |

En C# los tipos se dividen en dos grandes familias:

| Familia | Qué guarda la variable | Ejemplos | Dónde vive (normalmente) |
|---|---|---|---|
| **Tipos por valor** | El dato en sí | `int`, `double`, `bool`, `char`, `decimal`, `struct` | Stack (pila de ejecución) |
| **Tipos por referencia** | Una referencia (dirección) al dato | `string`, arreglos, `List<T>`, clases | Heap (memoria dinámica) |

```
Tipo por valor                       Tipo por referencia

int a = 5;                           int[] x = { 1, 2, 3 };
int b = a;      ← se copia el 5      int[] y = x;   ← se copia la DIRECCIÓN

 STACK                                STACK              HEAP
┌───────┐                            ┌───────┐
│ a: 5  │                            │ x: ●──┼──┐
├───────┤                            ├───────┤  ├──►  [ 1 | 2 | 3 ]
│ b: 5  │  (independientes)          │ y: ●──┼──┘     (un solo arreglo)
└───────┘                            └───────┘
```

> Esta diferencia es clave en POO: cuando copias un tipo por valor se copia el dato y las dos variables son independientes; cuando copias un tipo por referencia se copia **la dirección**, y ambas variables apuntan al **mismo objeto**. Cambiarlo desde una se ve desde la otra.

```csharp
int a = 5;
int b = a;
b = 10;
Console.WriteLine(a);      // 5  (a no cambió)

int[] x = { 1, 2, 3 };
int[] y = x;
y[0] = 99;
Console.WriteLine(x[0]);   // 99 (x y y son el mismo arreglo)
```

### 1.1.1. Datos primitivos

Los tipos primitivos (o "integrados") son los que el lenguaje trae de fábrica. En C# cada uno es un **alias** de una estructura de .NET: `int` es exactamente lo mismo que `System.Int32`, por eso tiene métodos como `int.Parse` o `int.MaxValue`.

| Tipo C# | Tipo .NET | Tamaño | Rango / Precisión | Literal de ejemplo | Valor por defecto |
|---|---|---|---|---|---|
| `sbyte` | `System.SByte` | 8 bits | -128 a 127 | `sbyte s = -5;` | `0` |
| `byte` | `System.Byte` | 8 bits | 0 a 255 | `byte b = 200;` | `0` |
| `short` | `System.Int16` | 16 bits | -32,768 a 32,767 | `short s = 1000;` | `0` |
| `ushort` | `System.UInt16` | 16 bits | 0 a 65,535 | `ushort u = 60000;` | `0` |
| `int` | `System.Int32` | 32 bits | -2,147,483,648 a 2,147,483,647 | `int i = 42;` | `0` |
| `uint` | `System.UInt32` | 32 bits | 0 a 4,294,967,295 | `uint u = 42u;` | `0` |
| `long` | `System.Int64` | 64 bits | ±9.2 × 10¹⁸ | `long l = 42L;` | `0` |
| `ulong` | `System.UInt64` | 64 bits | 0 a 1.8 × 10¹⁹ | `ulong u = 42UL;` | `0` |
| `float` | `System.Single` | 32 bits | ~6-9 dígitos de precisión | `float f = 3.14f;` | `0.0f` |
| `double` | `System.Double` | 64 bits | ~15-17 dígitos de precisión | `double d = 3.14;` | `0.0` |
| `decimal` | `System.Decimal` | 128 bits | 28-29 dígitos, exacto en base 10 | `decimal m = 3.14m;` | `0.0m` |
| `char` | `System.Char` | 16 bits | Un carácter Unicode (UTF-16) | `char c = 'A';` | `'\0'` |
| `bool` | `System.Boolean` | 8 bits | `true` o `false` | `bool ok = true;` | `false` |

**¿De dónde sale el rango?** Con `n` bits se pueden formar `2ⁿ` combinaciones. Un `byte` (8 bits) tiene `2⁸ = 256` combinaciones: del `0` al `255`. Un tipo **con signo** reparte esas combinaciones entre negativos y positivos: `sbyte` va de `-128` a `127` (siguen siendo 256 valores). Por eso `int` (32 bits) llega a poco más de 2 mil millones.

```
byte (8 bits):     0000 0000 = 0
                   0000 0001 = 1
                   ...
                   1111 1111 = 255      → 2⁸ = 256 valores posibles
```

**Enteros, reales, carácter y lógico**

| Categoría | Tipos | Cuándo usarlos |
|---|---|---|
| Enteros | `byte`, `short`, `int`, `long` (y sus versiones sin signo) | Conteos, edades, índices, cantidades sin decimales. **`int` es la opción por defecto** |
| Reales binarios | `float`, `double` | Mediciones, cálculos científicos, gráficos. **`double` es la opción por defecto** |
| Real decimal | `decimal` | **Dinero** y cualquier cálculo donde no se toleren errores de redondeo |
| Carácter | `char` | Una sola letra, dígito o símbolo, entre comillas simples |
| Lógico | `bool` | Resultados de comparaciones, banderas (`activo`, `encontrado`) |

```csharp
int edad = 20;
long poblacion = 8_000_000_000L;   // el guion bajo solo mejora la lectura
double promedio = 8.75;
float temperatura = 36.5f;          // sin la f, 36.5 sería double y no compila
decimal precio = 199.99m;           // sin la m tampoco compila
char inicial = 'N';
bool esActivo = true;

Console.WriteLine(int.MaxValue);    // 2147483647
Console.WriteLine(int.MinValue);    // -2147483648
Console.WriteLine(sizeof(double));  // 8 (bytes)
```

**Literales y sufijos.** Un literal es un valor escrito directamente en el código. Por defecto, un número entero es `int` y uno con punto es `double`; los sufijos indican otro tipo:

| Sufijo | Tipo | Ejemplo |
|---|---|---|
| (ninguno, sin punto) | `int` | `42` |
| (ninguno, con punto) | `double` | `3.14` |
| `L` | `long` | `42L` |
| `u` / `UL` | `uint` / `ulong` | `42u` |
| `f` | `float` | `3.14f` |
| `m` | `decimal` | `3.14m` |

**¿`double` o `decimal`?** `double` guarda los números en **binario**. Igual que `1/3` no se puede escribir exacto en decimal (`0.3333…`), `0.1` no se puede escribir exacto en binario (`0.000110011001100…`), así que se guarda una aproximación. `decimal` guarda los números en **base 10**, por eso es exacto con cantidades como `0.10`, aunque es más lento y ocupa el doble.

```csharp
Console.WriteLine(0.1 + 0.2);     // 0.30000000000000004
Console.WriteLine(0.1m + 0.2m);   // 0.3
```

> Regla práctica: **dinero → `decimal`**; medidas físicas y cálculos científicos → `double`.

**`char` y Unicode.** Un `char` ocupa 16 bits y guarda el **código Unicode** del carácter. Por eso se puede convertir a número y hacer aritmética con él:

```csharp
char letra = 'A';
int codigo = letra;                   // 65
char siguiente = (char)(letra + 1);   // 'B'
Console.WriteLine('a' < 'b');         // True (compara los códigos)
```

**Inferencia con `var`.** El compilador deduce el tipo a partir del valor inicial. No es un tipo "dinámico": el tipo queda **fijo** desde la compilación.

```csharp
var cantidad = 10;        // int
var precio = 9.99m;       // decimal
var nombre = "Ana";       // string
// cantidad = "diez";     // ERROR: cantidad es int
// var x;                 // ERROR: var necesita un valor inicial
```

**Tipos que aceptan `null`.** Los tipos por valor no pueden ser `null`, salvo que se declaren como **anulables** con `?`. Útil cuando un dato puede "no existir" (una calificación que aún no se captura).

```csharp
int? calificacion = null;
Console.WriteLine(calificacion.HasValue);          // False
calificacion = 9;
Console.WriteLine(calificacion.Value);             // 9
int final = calificacion ?? 0;                     // 0 si fuera null
```

### 1.1.2. Conversión de tipos

Convertir es pasar un valor de un tipo a otro. La pregunta clave es: **¿puede perderse información?** Si el tipo destino puede representar todos los valores del tipo origen, la conversión es segura (**ampliación**); si no, puede perderse información (**reducción**).

```
Ampliación (segura)                      Reducción (riesgosa)

int  ──►  long                            double ──► int
┌────┐    ┌────────┐                      ┌────────┐   ┌────┐
│ 42 │ ─► │   42   │   cabe completo      │  3.99  │ ─►│ 3  │   se pierde .99
└────┘    └────────┘                      └────────┘   └────┘
```

| Tipo de conversión | Sintaxis | Cuándo se usa | ¿Puede fallar? |
|---|---|---|---|
| **Implícita** (ampliación) | `long l = miInt;` | De un tipo "chico" a uno "grande" | No, el compilador la hace sola |
| **Explícita** (casting) | `int i = (int)miDouble;` | De un tipo "grande" a uno "chico" | Puede perder datos (trunca) o desbordarse |
| **Parse** | `int.Parse("10")` | De `string` a número | Sí, lanza `FormatException` |
| **TryParse** | `int.TryParse(texto, out int n)` | De `string` a número sin excepciones | No, regresa `false` si falla |
| **Convert** | `Convert.ToInt32(valor)` | Entre tipos base; acepta `null` (da `0`) | Sí, con texto inválido |
| **ToString** | `numero.ToString()` | De cualquier tipo a texto | No |

**Conversión implícita.** El compilador la permite sin pedir nada porque no hay riesgo. Orden de conversiones implícitas entre numéricos:

```
sbyte → short → int → long → float → double
byte  → ushort → uint → ulong ↗
              char ↗
```

```csharp
int entero = 100;
long grande = entero;      // int → long
double real = entero;      // int → double
char c = 'A';
int codigo = c;            // char → int (65)
```

> `decimal` no tiene conversión implícita desde `float` ni `double` (ni hacia ellos), porque usan sistemas de representación distintos (base 10 vs base 2). Siempre requiere cast.

**Conversión explícita (casting).** Se escribe el tipo destino entre paréntesis. Con eso le dices al compilador "sé que puede perderse información, hazlo de todos modos".

```csharp
double pi = 3.99;
int truncado = (int)pi;              // 3  → TRUNCA, no redondea

double negativo = -3.99;
int t2 = (int)negativo;              // -3 → trunca hacia cero

decimal precio = 19.99m;
double aprox = (double)precio;       // decimal → double requiere cast
```

**Desbordamiento (overflow).** Si el valor no cabe en el tipo destino, por defecto C# **no avisa**: los bits que sobran se descartan y el número "da la vuelta".

```csharp
int grande = 300;
byte chico = (byte)grande;           // 44  (300 - 256)

int maximo = int.MaxValue;
int desbordado = maximo + 1;         // -2147483648 (da la vuelta al mínimo)

checked
{
    int error = maximo + 1;          // OverflowException: ahora sí avisa
}
```

```
300 en binario (int):   0000 0001 0010 1100
(byte) se queda con los 8 bits bajos:   0010 1100  = 44
```

**De texto a número: `Parse`, `TryParse` y `Convert`.** Lo que el usuario escribe en consola **siempre es texto** (`string`), aunque teclee dígitos. Para operar con él hay que convertirlo.

```csharp
int edad = int.Parse("25");                 // 25
// int error = int.Parse("hola");           // FormatException
// int error2 = int.Parse("99999999999");   // OverflowException

// TryParse: la forma recomendada al leer de consola
Console.Write("Ingresa un número: ");
string entrada = Console.ReadLine();
if (int.TryParse(entrada, out int numero))
    Console.WriteLine($"Leíste {numero}");
else
    Console.WriteLine("Eso no es un número válido");

// Convert: acepta null y redondea los reales
int r1 = Convert.ToInt32(3.5);       // 4
int r2 = Convert.ToInt32(2.5);       // 2  (redondeo "bancario": al par más cercano)
int r3 = Convert.ToInt32(null);      // 0
bool b = Convert.ToBoolean("true");  // True
```

| Método | Con `"abc"` | Con `null` | Con `3.7` (double) |
|---|---|---|---|
| `(int)x` | No compila | No compila | `3` (trunca) |
| `int.Parse(s)` | `FormatException` | `ArgumentNullException` | — (solo acepta texto) |
| `int.TryParse(s, out n)` | `false`, `n = 0` | `false`, `n = 0` | — (solo acepta texto) |
| `Convert.ToInt32(x)` | `FormatException` | `0` | `4` (redondea) |

> `double.Parse("1.75")` depende de la **configuración regional** del equipo: en algunos países el separador decimal es la coma. Si el programa debe funcionar igual en cualquier equipo, usa `double.Parse("1.75", CultureInfo.InvariantCulture)`.

**De número a texto.** `ToString()` acepta un **formato**:

```csharp
string texto = 42.ToString();                     // "42"
string moneda = 1234.5m.ToString("C");            // "$1,234.50"
string dosDecimales = 3.14159.ToString("F2");     // "3.14"
string miles = 1234567.ToString("N0");            // "1,234,567"
string porcentaje = 0.256.ToString("P1");         // "25.6 %"
string ceros = 7.ToString("D3");                  // "007"
```

**Boxing y unboxing.** Todos los tipos en .NET derivan de `object`. Convertir un tipo por valor a `object` se llama **boxing**: el valor se "mete en una caja" en el heap. Sacarlo es **unboxing** y requiere cast. Es una conversión costosa, y es la razón por la que hoy se prefieren las colecciones genéricas (`List<int>`) sobre las antiguas (`ArrayList`, que guardaba todo como `object`).

```csharp
int valor = 10;
object caja = valor;        // boxing: se copia el 10 al heap
int deVuelta = (int)caja;   // unboxing
// double mal = (double)caja;   // InvalidCastException: la caja tiene un int
```

### 1.1.3. Constantes y variables (const y readonly)

Una **variable** es un espacio de memoria con **nombre** y **tipo** cuyo valor puede cambiar durante la ejecución. Tiene tres momentos:

| Momento | Ejemplo | Qué pasa |
|---|---|---|
| **Declaración** | `int contador;` | Se reserva el espacio y se fija el tipo |
| **Inicialización** | `contador = 0;` | Se le da el primer valor |
| **Asignación** | `contador = contador + 1;` | Se reemplaza el valor anterior |

```csharp
int contador = 0;           // declaración + inicialización
contador = contador + 1;    // asignación: ahora vale 1

int sinValor;
// Console.WriteLine(sinValor);   // ERROR: variable local sin asignar
```

> C# no permite leer una variable **local** que no ha sido asignada. Los **campos** de una clase, en cambio, sí reciben automáticamente el valor por defecto de su tipo (`0`, `false`, `null`…).

**Ámbito (scope).** Una variable solo existe dentro del bloque `{ }` donde se declaró. Al salir del bloque, deja de existir.

```csharp
if (true)
{
    int dentro = 5;
    Console.WriteLine(dentro);   // 5
}
// Console.WriteLine(dentro);    // ERROR: dentro no existe aquí
```

Una **constante** es un valor que **no cambia** una vez definido. Sirve para dar nombre a valores fijos (en lugar de "números mágicos" sueltos en el código) y para que el compilador impida modificarlos por error. C# tiene dos mecanismos: `const` y `readonly`.

| Concepto | Sintaxis | Se asigna en | ¿Puede cambiar? |
|---|---|---|---|
| Variable | `int x = 5;` | Cualquier momento | Sí |
| Variable con inferencia | `var x = 5;` | Al declararla (obligatorio) | Sí, pero el tipo queda fijo |
| `const` | `const int Max = 100;` | Al declararla, **en tiempo de compilación** | Nunca |
| `readonly` | `readonly int max;` | Al declararla **o en el constructor** (tiempo de ejecución) | No, después del constructor |

**`const` — constante de compilación.** Su valor debe conocerse **al escribir el código**. El compilador sustituye cada uso de la constante por su valor literal, como si hubieras escrito el número directamente. Por eso solo admite tipos simples cuyo valor es un literal.

```csharp
const double Iva = 0.16;
const int DiasSemana = 7;
const string NombreApp = "Inventario";

double total = 100 * (1 + Iva);             // el compilador lo trata como 100 * (1 + 0.16)

// Iva = 0.18;                              // ERROR: no se puede asignar a una constante
// const DateTime Hoy = DateTime.Now;       // ERROR: DateTime.Now no se conoce al compilar
```

**`readonly` — constante de ejecución.** Es un **campo de clase** cuyo valor se fija una sola vez, ya sea en la declaración o en el **constructor**, y a partir de ahí no puede cambiar. El valor puede calcularse en tiempo de ejecución, por eso cada objeto puede tener el suyo.

```csharp
class Factura
{
    public const decimal Iva = 0.16m;                       // igual para todas las facturas, siempre
    public static readonly DateTime InicioSistema = DateTime.Now; // se calcula al ejecutar, compartido
    public readonly string Folio;                           // cada factura tiene el suyo
    public readonly DateTime FechaEmision;

    public Factura(string folio)
    {
        Folio = folio;                 // válido: estamos en el constructor
        FechaEmision = DateTime.Now;
    }

    public void Cambiar()
    {
        // Folio = "otro";             // ERROR: readonly fuera del constructor
        // Iva = 0.18m;                // ERROR: const nunca cambia
    }
}
```

**Diferencias entre `const` y `readonly`:**

| Característica | `const` | `readonly` |
|---|---|---|
| Momento en que se conoce el valor | Compilación | Ejecución |
| Dónde se puede asignar | Solo en la declaración | En la declaración o en el constructor |
| Tipos permitidos | Primitivos, `string`, `enum` | Cualquier tipo (incluye objetos, arreglos, `List<T>`) |
| Es `static` | Implícitamente sí (pertenece a la clase) | Solo si se declara `static readonly` |
| Puede ser variable local | Sí | No, solo campo de clase |
| Valor por objeto | No, es uno solo | Sí, cada objeto puede tener uno distinto |
| Ejemplo típico | `const double Pi = 3.1416;` | `readonly DateTime creado = DateTime.Now;` |

> **Ojo con `readonly` y tipos por referencia:** `readonly List<int> datos = new();` impide hacer `datos = otraLista`, pero **sí** permite `datos.Add(5)`. Lo que es de solo lectura es **la referencia**, no el contenido del objeto.

```
readonly List<int> datos

 datos: ●──────►  [ 1 | 2 | 3 ]
        ▲               ▲
  NO se puede      SÍ se puede modificar
  reasignar        (Add, Remove, datos[0] = 9)
```

**Convenciones de nombres en C#:**

| Elemento | Convención | Ejemplo |
|---|---|---|
| Variables locales y parámetros | camelCase | `totalVentas` |
| Constantes | PascalCase | `MaxIntentos` |
| Métodos, clases, propiedades | PascalCase | `CalcularPromedio()` |
| Campos privados | `_camelCase` | `_contador` |

---

## 1.2. Operadores

Un **operador** es un símbolo que realiza una operación sobre uno o más valores (**operandos**) y produce un resultado. Una combinación de operandos y operadores que produce un valor se llama **expresión**.

```
     operando   operador   operando
         ▼         ▼          ▼
         7         +          2        →   9  (resultado)
         └─────────┬──────────┘
               expresión
```

Según cuántos operandos reciben:

| Clase | Operandos | Ejemplos |
|---|---|---|
| **Unario** | 1 | `-x`, `!activo`, `x++` |
| **Binario** | 2 | `a + b`, `a > b`, `a && b` |
| **Ternario** | 3 | `condicion ? a : b` |

El **tipo** de los operandos determina cómo se comporta el operador: `+` suma números pero **concatena** cadenas; `/` hace división entera con enteros y división real con reales.

### 1.2.1. Operadores aritméticos

Realizan cálculos matemáticos sobre operandos numéricos.

| Operador | Operación | Ejemplo | Resultado |
|---|---|---|---|
| `+` | Suma (y concatenación con `string`) | `7 + 2` | `9` |
| `-` | Resta (o negación unaria `-x`) | `7 - 2` | `5` |
| `*` | Multiplicación | `7 * 2` | `14` |
| `/` | División | `7 / 2` | `3` (entera si ambos son enteros) |
| `%` | Módulo (residuo de la división) | `7 % 2` | `1` |
| `++` | Incremento en 1 | `x++` / `++x` | `x + 1` |
| `--` | Decremento en 1 | `x--` / `--x` | `x - 1` |

**El tipo del resultado.** En una operación binaria, ambos operandos se llevan al tipo "más grande" de los dos, y el resultado es de ese tipo. Por eso `int / int` da `int` y se pierde la parte decimal.

| Operación | Tipos | Tipo del resultado | Resultado |
|---|---|---|---|
| `7 / 2` | `int / int` | `int` | `3` |
| `7 / 2.0` | `int / double` | `double` | `3.5` |
| `7.0 / 2` | `double / int` | `double` | `3.5` |
| `(double)7 / 2` | `double / int` | `double` | `3.5` |
| `(double)(7 / 2)` | primero `int / int` | `double` | `3.0` ← ya se perdió |

```csharp
int a = 7, b = 2;
Console.WriteLine(a / b);             // 3   (división entera)
Console.WriteLine((double)a / b);     // 3.5 (basta con convertir un operando)

// Error clásico al calcular un promedio
int suma = 15, n = 4;
double malo = suma / n;               // 3    (se dividió como int y luego se convirtió)
double bueno = (double)suma / n;      // 3.75
```

**Módulo `%`.** Devuelve el **residuo** de la división entera. Es de los operadores más útiles en programación:

| Uso | Expresión | Ejemplo |
|---|---|---|
| ¿Es par? | `n % 2 == 0` | `8 % 2 == 0` → `true` |
| ¿Es múltiplo de k? | `n % k == 0` | `15 % 5 == 0` → `true` |
| Último dígito | `n % 10` | `1234 % 10` → `4` |
| Valores cíclicos | `(i + 1) % tamaño` | índice que regresa a `0` al llegar al final |
| Convertir unidades | `segundos % 60` | `135 % 60` → `15` segundos sobrantes |

```csharp
int totalSegundos = 3725;
int horas = totalSegundos / 3600;            // 1
int minutos = totalSegundos % 3600 / 60;     // 2
int segundos = totalSegundos % 60;           // 5
Console.WriteLine($"{horas}h {minutos}m {segundos}s");   // 1h 2m 5s
```

> El signo del residuo es el del **dividendo**: `-7 % 3` da `-1`, no `2`. Por eso para revisar impares es más seguro `n % 2 != 0` que `n % 2 == 1`.

**División entre cero.** El comportamiento depende del tipo:

```csharp
int x = 10;
// int r = x / 0;              // DivideByZeroException (enteros y decimal)
Console.WriteLine(10.0 / 0);   // ∞  (double.PositiveInfinity)
Console.WriteLine(0.0 / 0);    // NaN (Not a Number)
```

**Incremento y decremento: prefijo vs sufijo.** Ambos suman (o restan) 1 a la variable; la diferencia es **qué valor devuelve la expresión**.

| Forma | Nombre | Qué hace | Valor que devuelve |
|---|---|---|---|
| `x++` | Postincremento | Usa `x`, **luego** incrementa | El valor **anterior** |
| `++x` | Preincremento | Incrementa, **luego** usa `x` | El valor **nuevo** |

```csharp
int x = 5;
int y = x++;   // y = 5, x = 6  (primero usa, luego incrementa)
int z = ++x;   // z = 7, x = 7  (primero incrementa, luego usa)
```

```
Paso a paso:        x     y     z
int x = 5;          5     -     -
int y = x++;        6     5     -     ← y recibió el 5 viejo
int z = ++x;        7     5     7     ← z recibió el 7 nuevo
```

> Si el incremento va solo en su propia línea (`contador++;`), las dos formas dan lo mismo. La diferencia solo importa cuando la expresión se usa dentro de otra.

**Asignación compuesta.** Abrevian "operar y guardar en la misma variable":

| Operador | Equivale a | Ejemplo con `x = 10` | Resultado |
|---|---|---|---|
| `x += 3` | `x = x + 3` | `x += 3` | `13` |
| `x -= 3` | `x = x - 3` | `x -= 3` | `7` |
| `x *= 3` | `x = x * 3` | `x *= 3` | `30` |
| `x /= 3` | `x = x / 3` | `x /= 3` | `3` |
| `x %= 3` | `x = x % 3` | `x %= 3` | `1` |

**Clase `Math`.** C# no tiene operador de potencia (`**` no existe); las operaciones matemáticas avanzadas están en `Math`:

| Método | Qué hace | Ejemplo | Resultado |
|---|---|---|---|
| `Math.Pow(b, e)` | Potencia | `Math.Pow(2, 10)` | `1024` |
| `Math.Sqrt(x)` | Raíz cuadrada | `Math.Sqrt(81)` | `9` |
| `Math.Abs(x)` | Valor absoluto | `Math.Abs(-5)` | `5` |
| `Math.Round(x, d)` | Redondea a `d` decimales | `Math.Round(3.456, 2)` | `3.46` |
| `Math.Floor(x)` | Redondea hacia abajo | `Math.Floor(3.9)` | `3` |
| `Math.Ceiling(x)` | Redondea hacia arriba | `Math.Ceiling(3.1)` | `4` |
| `Math.Max(a, b)` / `Math.Min(a, b)` | Mayor / menor | `Math.Max(4, 9)` | `9` |
| `Math.PI` | Constante π | `Math.PI` | `3.14159…` |

```csharp
double radio = 3;
double area = Math.PI * Math.Pow(radio, 2);
Console.WriteLine($"Área: {area:F2}");      // Área: 28.27
```

### 1.2.2. Operadores relacionales

Comparan dos valores y **siempre** devuelven un `bool` (`true` o `false`). Son la base de toda decisión en un programa: cada `if` y cada bucle evalúa una expresión relacional.

| Operador | Significado | Ejemplo | Resultado |
|---|---|---|---|
| `==` | Igual a | `5 == 5` | `true` |
| `!=` | Distinto de | `5 != 3` | `true` |
| `>` | Mayor que | `5 > 3` | `true` |
| `<` | Menor que | `5 < 3` | `false` |
| `>=` | Mayor o igual | `5 >= 5` | `true` |
| `<=` | Menor o igual | `3 <= 2` | `false` |

```csharp
int calificacion = 75;
bool aprobo = calificacion >= 70;       // true
bool perfecto = calificacion == 100;    // false

Console.WriteLine(aprobo);              // True
```

> **`=` no es `==`.** `=` **asigna** un valor; `==` **compara** dos valores. En C#, `if (x = 5)` no compila porque el resultado de una asignación `int` no es `bool`, lo que evita muchos errores que sí ocurren en C.

**Comparar reales.** Como `double` guarda aproximaciones, comparar con `==` puede fallar aunque "matemáticamente" sean iguales. Se compara con una **tolerancia**:

```csharp
double a = 0.1 + 0.2;
Console.WriteLine(a == 0.3);                       // False
Console.WriteLine(Math.Abs(a - 0.3) < 1e-9);       // True
```

**Comparar caracteres.** Se comparan por su código Unicode, así que `'A' < 'a'` (65 < 97).

**Comparar cadenas.** En C#, `==` entre dos `string` compara el **contenido** (a diferencia de Java). Distingue mayúsculas de minúsculas; para ignorarlas se usa `Equals` con `StringComparison`. Para saber cuál va primero alfabéticamente se usa `string.Compare` (los operadores `<` y `>` no funcionan con cadenas).

```csharp
string a = "Hola";
string b = "hola";

Console.WriteLine(a == b);                                           // False
Console.WriteLine(a.Equals(b, StringComparison.OrdinalIgnoreCase));  // True
Console.WriteLine(string.Compare("Ana", "Luis"));                    // -1 (Ana va antes)
// Console.WriteLine("Ana" < "Luis");                                // ERROR: no compila
```

**Igualdad de valor vs igualdad de referencia.** Con tipos por valor, `==` compara los datos. Con la mayoría de los tipos por referencia (arreglos, objetos de tus clases), `==` compara si son **el mismo objeto**, no si tienen el mismo contenido. `string` es la excepción porque redefine `==`.

```csharp
int[] x = { 1, 2, 3 };
int[] y = { 1, 2, 3 };
int[] z = x;

Console.WriteLine(x == y);   // False (mismo contenido, pero objetos distintos)
Console.WriteLine(x == z);   // True  (misma referencia)
```

### 1.2.3. Operadores lógicos

Combinan expresiones booleanas para formar condiciones compuestas. Se basan en el **álgebra de Boole**.

| Operador | Nombre | Devuelve `true` cuando… |
|---|---|---|
| `&&` | AND (Y) | **Ambas** condiciones son verdaderas |
| `\|\|` | OR (O) | **Al menos una** es verdadera |
| `!` | NOT (NO) | La condición es **falsa** (invierte el valor) |
| `^` | XOR (O exclusivo) | **Exactamente una** es verdadera |

**Tabla de verdad:**

| A | B | `A && B` | `A \|\| B` | `!A` | `A ^ B` |
|---|---|---|---|---|---|
| `true` | `true` | `true` | `true` | `false` | `false` |
| `true` | `false` | `false` | `true` | `false` | `true` |
| `false` | `true` | `false` | `true` | `true` | `true` |
| `false` | `false` | `false` | `false` | `true` | `false` |

```csharp
int edad = 20;
bool tieneCredencial = true;

bool puedeEntrar = edad >= 18 && tieneCredencial;    // true
bool descuento = edad < 12 || edad >= 60;            // false
bool esMenor = !(edad >= 18);                        // false
bool rango = edad >= 18 && edad <= 25;               // true (está entre 18 y 25)
```

> Para expresar un rango no se puede escribir `18 <= edad <= 25` como en matemáticas; hay que unir dos comparaciones con `&&`.

**Cortocircuito.** `&&` y `||` dejan de evaluar en cuanto conocen el resultado:

| Operador | Si el primer operando es… | El segundo… | Porque… |
|---|---|---|---|
| `&&` | `false` | **No** se evalúa | `false && cualquier cosa` siempre es `false` |
| `\|\|` | `true` | **No** se evalúa | `true \|\| cualquier cosa` siempre es `true` |

Esto no es solo una optimización: permite escribir condiciones que protegen a la siguiente.

```csharp
string nombre = null;

// Si nombre es null, NO se evalúa nombre.Length y no truena
if (nombre != null && nombre.Length > 3)
    Console.WriteLine("Nombre largo");

int divisor = 0;
// Si divisor es 0, no se intenta dividir
if (divisor != 0 && 10 / divisor > 2)
    Console.WriteLine("Mayor que 2");
```

> El **orden** importa: `nombre.Length > 3 && nombre != null` truena con `NullReferenceException`, porque revisa la longitud antes de comprobar el `null`.

**`&` y `|` sin cortocircuito.** Con `bool`, `&` y `|` dan el mismo resultado que `&&` y `||`, pero **siempre** evalúan ambos lados. Casi nunca se necesitan; en la práctica usa `&&` y `||`.

**Leyes de De Morgan.** Sirven para simplificar o invertir condiciones:

| Expresión | Equivale a |
|---|---|
| `!(A && B)` | `!A \|\| !B` |
| `!(A \|\| B)` | `!A && !B` |

```csharp
// "No es fin de semana"
bool noFinDeSemana = !(dia == 6 || dia == 7);
bool igual        = dia != 6 && dia != 7;   // misma condición con De Morgan
```

**Otros operadores útiles**

| Operador | Equivale a | Ejemplo |
|---|---|---|
| `? :` | Condicional ternario | `string r = edad >= 18 ? "Mayor" : "Menor";` |
| `??` | Valor por defecto si es `null` | `string n = nombre ?? "Anónimo";` |
| `??=` | Asigna solo si es `null` | `lista ??= new List<int>();` |
| `?.` | Acceso seguro (null-conditional) | `int? largo = texto?.Length;` |

**Precedencia** (de mayor a menor). Determina qué operación se hace primero cuando no hay paréntesis:

| Nivel | Operadores |
|---|---|
| 1 | `x++` `x--` `.` `()` `[]` `?.` |
| 2 | `!` `++x` `--x` `-x` (unarios), casting `(T)x` |
| 3 | `*` `/` `%` |
| 4 | `+` `-` |
| 5 | `<` `>` `<=` `>=` |
| 6 | `==` `!=` |
| 7 | `^` |
| 8 | `&&` |
| 9 | `\|\|` |
| 10 | `??` |
| 11 | `? :` |
| 12 | `=` `+=` `-=` … |

```csharp
bool resultado = 5 + 3 * 2 > 10 && !false;
```

```
Evaluación paso a paso:
5 + 3 * 2 > 10 && !false
5 + 6     > 10 && !false     ← * antes que +
11        > 10 && !false     ← + antes que >
true           && !false     ← > antes que &&
true           && true       ← ! es unario (alta precedencia)
true
```

> Ante la duda, usa paréntesis: hacen el código más claro aunque no sean necesarios. `(edad >= 18) && (tieneCredencial)` se lee mejor que sin ellos.

---

## 1.3. Cadenas de caracteres

Una **cadena** es una secuencia ordenada de caracteres que representa texto: nombres, mensajes, correos, contenido de archivos. Es probablemente el tipo de dato más usado en cualquier aplicación real, porque toda la entrada y salida con el usuario es texto.

```
string s = "Hola";

índice:   0     1     2     3
       ┌─────┬─────┬─────┬─────┐
       │ 'H' │ 'o' │ 'l' │ 'a' │      s.Length = 4
       └─────┴─────┴─────┴─────┘
```

### 1.3.1. String y métodos principales

`string` (alias de `System.String`) representa una secuencia de caracteres `char` en UTF-16.

Características:

| Característica | Descripción |
|---|---|
| Tipo por referencia | La variable guarda una referencia; el texto vive en el heap |
| Se compara por contenido | `==` compara los caracteres, no las referencias |
| **Inmutable** | Una vez creada, una cadena **nunca** cambia; los métodos regresan una cadena **nueva** |
| Indexable | `s[i]` lee el carácter en la posición `i` (solo lectura) |
| Valor por defecto | `null` (no es lo mismo que la cadena vacía `""`) |

**Inmutabilidad.** Es la característica más importante de `string`. Ningún método modifica la cadena original: `ToUpper()`, `Replace()`, `Trim()`… todos crean y devuelven **otra** cadena. Si no guardas el resultado, se pierde.

```csharp
string saludo = "hola";
saludo.ToUpper();                 // crea "HOLA" pero nadie la guarda
Console.WriteLine(saludo);        // "hola"
saludo = saludo.ToUpper();        // ahora saludo apunta a la nueva cadena
Console.WriteLine(saludo);        // "HOLA"

// saludo[0] = 'h';               // ERROR: el indexador es de solo lectura
```

```
saludo = saludo.ToUpper();

 antes:   saludo ●──►  "hola"
 después: saludo ●──┐  "hola"   ← queda sin referencias (el recolector la libera)
                    └► "HOLA"   ← cadena NUEVA
```

> **¿Por qué inmutable?** Porque así varias variables pueden compartir la misma cadena sin riesgo de que una la cambie "por debajo" a las demás; además es segura entre hilos y su hash nunca cambia (importante para usarla como clave en diccionarios). El costo es que modificar texto muchas veces crea muchos objetos; para eso existe `StringBuilder` (1.3.2).

**`null` vs cadena vacía.** `null` significa "no hay cadena"; `""` (o `string.Empty`) es una cadena que existe pero tiene 0 caracteres. Llamar un método sobre `null` lanza `NullReferenceException`.

```csharp
string a = null;
string b = "";
Console.WriteLine(b.Length);                       // 0
// Console.WriteLine(a.Length);                    // NullReferenceException
Console.WriteLine(string.IsNullOrEmpty(a));        // True
Console.WriteLine(string.IsNullOrWhiteSpace("  ")); // True
```

**Formas de crear cadenas**

| Forma | Ejemplo | Uso |
|---|---|---|
| Literal normal | `"Línea\nNueva"` | Interpreta secuencias de escape |
| Verbatim `@` | `@"C:\Users\Gon161"` | Ignora escapes; útil para rutas |
| Interpolada `$` | `$"Hola {nombre}"` | Inserta expresiones dentro de `{}` |
| Raw (C# 11) | `"""texto "con" comillas"""` | Texto con comillas sin escapar |
| Concatenación | `"Hola " + nombre` | Une cadenas con `+` |

**Secuencias de escape**

| Escape | Significado |
|---|---|
| `\n` | Salto de línea |
| `\t` | Tabulación |
| `\"` | Comilla doble |
| `\\` | Barra invertida |
| `\'` | Comilla simple (en `char`) |

```csharp
string nombre = "Ana";
int edad = 21;

string c1 = "Hola " + nombre + ", tienes " + edad + " años";   // concatenación
string c2 = string.Format("Hola {0}, tienes {1} años", nombre, edad);
string c3 = $"Hola {nombre}, tienes {edad} años";               // interpolación (recomendada)
string ruta = @"C:\Users\Ana\Documentos";                       // sin escapar las \
```

> Con `+`, si uno de los operandos es `string`, el otro se convierte a texto: `"Total: " + 5 + 3` da `"Total: 53"`, porque se evalúa de izquierda a derecha. Para sumar primero: `"Total: " + (5 + 3)` → `"Total: 8"`.

**Interpolación con formato y alineación:**

```csharp
string nombre = "Ana";
double promedio = 9.456;
Console.WriteLine($"{nombre} tiene promedio {promedio:F2}");   // Ana tiene promedio 9.46
Console.WriteLine($"|{nombre,10}|");                           // |       Ana| (derecha)
Console.WriteLine($"|{nombre,-10}|");                          // |Ana       | (izquierda)
Console.WriteLine($"Total: {1234.5m:C}");                      // Total: $1,234.50
```

**Métodos y propiedades principales**

| Miembro | Qué hace | Ejemplo | Resultado |
|---|---|---|---|
| `Length` | Número de caracteres | `"Hola".Length` | `4` |
| `[i]` | Carácter en la posición `i` | `"Hola"[0]` | `'H'` |
| `ToUpper()` / `ToLower()` | Mayúsculas / minúsculas | `"Hola".ToUpper()` | `"HOLA"` |
| `Trim()` | Quita espacios al inicio y final | `"  hi  ".Trim()` | `"hi"` |
| `TrimStart()` / `TrimEnd()` | Quita espacios de un lado | `"  hi".TrimStart()` | `"hi"` |
| `Substring(inicio, largo)` | Extrae una parte | `"Programar".Substring(0, 3)` | `"Pro"` |
| `Substring(inicio)` | Desde `inicio` hasta el final | `"Programar".Substring(3)` | `"gramar"` |
| `IndexOf(x)` | Posición de la primera aparición (`-1` si no está) | `"banana".IndexOf('n')` | `2` |
| `LastIndexOf(x)` | Posición de la última aparición | `"banana".LastIndexOf('n')` | `4` |
| `Contains(x)` | ¿Contiene el texto? | `"Hola mundo".Contains("mundo")` | `true` |
| `StartsWith(x)` / `EndsWith(x)` | ¿Empieza / termina con? | `"archivo.cs".EndsWith(".cs")` | `true` |
| `Replace(a, b)` | Reemplaza todas las apariciones | `"a-b-c".Replace("-", "/")` | `"a/b/c"` |
| `Split(sep)` | Divide en un arreglo | `"a,b,c".Split(',')` | `["a","b","c"]` |
| `string.Join(sep, col)` | Une una colección en un texto | `string.Join("-", arr)` | `"a-b-c"` |
| `string.Concat(a, b)` | Concatena | `string.Concat("ab", "cd")` | `"abcd"` |
| `Insert(pos, txt)` | Inserta texto | `"Hla".Insert(1, "o")` | `"Hola"` |
| `Remove(pos, n)` | Elimina `n` caracteres | `"Hola!!".Remove(4, 2)` | `"Hola"` |
| `PadLeft(n, c)` / `PadRight(n, c)` | Rellena hasta `n` caracteres | `"7".PadLeft(3, '0')` | `"007"` |
| `Equals(x, comparación)` | Compara contenido | `a.Equals(b, StringComparison.OrdinalIgnoreCase)` | Ignora mayúsculas |
| `string.Compare(a, b)` | Orden alfabético (`<0`, `0`, `>0`) | `string.Compare("a", "b")` | `-1` |
| `string.IsNullOrEmpty(s)` | ¿Es `null` o `""`? | `string.IsNullOrEmpty("")` | `true` |
| `string.IsNullOrWhiteSpace(s)` | ¿Es `null`, `""` o solo espacios? | `string.IsNullOrWhiteSpace("  ")` | `true` |
| `ToCharArray()` | Convierte a arreglo de `char` | `"abc".ToCharArray()` | `['a','b','c']` |

> En `Substring(inicio, largo)` el segundo parámetro es la **cantidad** de caracteres, no la posición final. Si `inicio + largo` se pasa de `Length`, lanza `ArgumentOutOfRangeException`.

```csharp
string frase = "  Programación Orientada a Objetos  ";

string limpia = frase.Trim();                        // "Programación Orientada a Objetos"
Console.WriteLine(limpia.Length);                    // 32
Console.WriteLine(limpia.ToUpper());                 // "PROGRAMACIÓN ORIENTADA A OBJETOS"
Console.WriteLine(limpia.Contains("Objetos"));       // True
Console.WriteLine(limpia.IndexOf("Orientada"));      // 13
Console.WriteLine(limpia.Substring(13, 9));          // "Orientada"
Console.WriteLine(limpia.Replace("Objetos", "POO")); // "Programación Orientada a POO"

string[] palabras = limpia.Split(' ');
Console.WriteLine(palabras.Length);                  // 4
Console.WriteLine(string.Join(" | ", palabras));     // "Programación | Orientada | a | Objetos"
```

**Combinar métodos: extraer partes de un correo**

```csharp
string correo = "  Ana.Lopez@Escuela.edu.mx  ";

correo = correo.Trim().ToLower();                    // los métodos se pueden encadenar
int arroba = correo.IndexOf('@');                    // 9

string usuario = correo.Substring(0, arroba);        // "ana.lopez"
string dominio = correo.Substring(arroba + 1);       // "escuela.edu.mx"
bool esMx = correo.EndsWith(".mx");                  // True
```

> Encadenar funciona porque cada método **regresa una cadena nueva**, y sobre esa se llama el siguiente: `correo.Trim()` regresa una cadena, y sobre ella se ejecuta `.ToLower()`.

**Recorrer una cadena.** Como es una secuencia de `char`, se puede recorrer con `for` (con índice) o `foreach`:

```csharp
string palabra = "programacion";

// Contar vocales
int vocales = 0;
foreach (char c in palabra)
    if ("aeiou".Contains(c)) vocales++;
Console.WriteLine($"Vocales: {vocales}");            // 5

// Recorrer con índice
for (int i = 0; i < palabra.Length; i++)
    Console.Write($"{i}:{palabra[i]} ");             // 0:p 1:r 2:o ...

// Métodos de char
Console.WriteLine(char.IsDigit('7'));                // True
Console.WriteLine(char.IsLetter('a'));               // True
Console.WriteLine(char.ToUpper('a'));                // 'A'
```

### 1.3.2. StringBuilder y manipulación eficiente de cadenas

**El problema.** Como `string` es inmutable, cada concatenación crea **un objeto nuevo** y copia todo el texto anterior en él. En un ciclo eso significa copiar el texto una y otra vez:

```csharp
string resultado = "";
for (int i = 0; i < 5; i++)
    resultado += i;          // cada vuelta crea una cadena nueva
```

```
vuelta 0:  ""     + "0"  →  "0"          (copia 1 carácter)
vuelta 1:  "0"    + "1"  →  "01"         (copia 2)
vuelta 2:  "01"   + "2"  →  "012"        (copia 3)
vuelta 3:  "012"  + "3"  →  "0123"       (copia 4)
vuelta 4:  "0123" + "4"  →  "01234"      (copia 5)

5 objetos creados, 1+2+3+4+5 = 15 caracteres copiados
```

Con `n` concatenaciones se copian aproximadamente `n²/2` caracteres: con 10,000 vueltas son unos **50 millones** de copias y miles de objetos que el recolector de basura tiene que limpiar.

**La solución.** `StringBuilder` (en `System.Text`) es una cadena **mutable**: mantiene un **búfer** interno (un arreglo de `char`) con espacio de sobra. `Append` escribe en el espacio libre sin crear objetos nuevos; solo cuando el búfer se llena reserva más espacio. Al final, `ToString()` crea **una sola** cadena con el resultado.

```
string:        "a" → "ab" → "abc" → "abcd"          (4 objetos, 4 copias)

StringBuilder: búfer con capacidad 16
               [a][ ][ ][ ][ ][ ]...   Append("a")
               [a][b][ ][ ][ ][ ]...   Append("b")
               [a][b][c][ ][ ][ ]...   Append("c")
               [a][b][c][d][ ][ ]...   Append("d")   (1 búfer que se va llenando)
               ToString() → "abcd"                    (1 sola cadena al final)
```

| Característica | `string` | `StringBuilder` |
|---|---|---|
| Mutabilidad | Inmutable | Mutable |
| Concatenar | Crea un objeto nuevo cada vez | Escribe en el búfer existente |
| Costo de `n` concatenaciones en ciclo | ~n² copias | ~n copias |
| Modificar un carácter | No se puede (`s[0] = 'x'` no compila) | `sb[0] = 'x'` |
| Comparar con `==` | Compara contenido | Compara referencias (usa `sb.ToString()`) |
| Espacio de nombres | `System` | `System.Text` |

**Miembros principales**

| Miembro | Qué hace | Ejemplo |
|---|---|---|
| `new StringBuilder()` | Crea un builder vacío (capacidad inicial 16) | `var sb = new StringBuilder();` |
| `new StringBuilder(texto)` | Crea con un texto inicial | `new StringBuilder("Hola")` |
| `new StringBuilder(capacidad)` | Reserva espacio inicial | `new StringBuilder(1000)` |
| `Append(x)` | Agrega al final (acepta cualquier tipo) | `sb.Append("Hola").Append(5);` |
| `AppendLine(x)` | Agrega y añade salto de línea | `sb.AppendLine("Fin");` |
| `AppendFormat(fmt, args)` | Agrega con formato | `sb.AppendFormat("{0:F2}", 3.1416);` |
| `Insert(pos, x)` | Inserta en una posición | `sb.Insert(0, ">> ");` |
| `Remove(pos, n)` | Elimina `n` caracteres | `sb.Remove(0, 3);` |
| `Replace(a, b)` | Reemplaza | `sb.Replace("a", "@");` |
| `Clear()` | Vacía el contenido | `sb.Clear();` |
| `Length` | Largo actual (se puede asignar para recortar) | `sb.Length = 5;` |
| `Capacity` | Espacio reservado en el búfer | `sb.Capacity` |
| `[i]` | Lee **o modifica** un carácter | `sb[0] = 'h';` |
| `ToString()` | Obtiene el `string` final | `string r = sb.ToString();` |

> A diferencia de `string`, los métodos de `StringBuilder` **modifican el mismo objeto** y además lo regresan, por eso se pueden encadenar: `sb.Append("a").Append("b").AppendLine();`.

```csharp
using System.Text;

var sb = new StringBuilder();
sb.Append("Lista de números: ");
for (int i = 1; i <= 5; i++)
{
    sb.Append(i);
    if (i < 5) sb.Append(", ");
}
sb.Insert(0, ">> ");
sb.Replace("números", "enteros");

Console.WriteLine(sb.ToString());   // >> Lista de enteros: 1, 2, 3, 4, 5

// Quitar la última coma de una lista construida en ciclo
var lista = new StringBuilder();
foreach (string f in new[] { "Manzana", "Pera", "Uva" })
    lista.Append(f).Append(", ");
lista.Length -= 2;                  // recorta los últimos 2 caracteres
Console.WriteLine(lista);           // Manzana, Pera, Uva
```

**Invertir una cadena**

```csharp
string original = "Objetos";
var inv = new StringBuilder(original.Length);
for (int i = original.Length - 1; i >= 0; i--)
    inv.Append(original[i]);
Console.WriteLine(inv);             // sotejbO
```

**Generar un reporte**

```csharp
string[] productos = { "Lápiz", "Cuaderno", "Borrador" };
decimal[] precios = { 8.50m, 45.00m, 6.00m };

var reporte = new StringBuilder();
reporte.AppendLine("=== REPORTE DE PRODUCTOS ===");
for (int i = 0; i < productos.Length; i++)
    reporte.AppendLine($"{productos[i],-10} {precios[i],10:C}");
reporte.AppendLine(new string('-', 28));
reporte.Append("Total de productos: ").Append(productos.Length);

Console.WriteLine(reporte.ToString());
```

**Modificar caracteres individuales**

```csharp
var sb = new StringBuilder("hola mundo");
sb[0] = char.ToUpper(sb[0]);        // "Hola mundo"
for (int i = 0; i < sb.Length; i++)
    if (sb[i] == 'o') sb[i] = '0';
Console.WriteLine(sb);              // H0la mund0
```

**¿Cuándo usar cada uno?**

| Situación | Usa | Por qué |
|---|---|---|
| Pocas concatenaciones, texto fijo | `string` (con `$""`) | Más legible; el costo es despreciable |
| Concatenar dentro de ciclos | `StringBuilder` | Evita crear un objeto por vuelta |
| Construir textos grandes (reportes, archivos, HTML) | `StringBuilder` | Un solo búfer en lugar de miles de cadenas |
| Modificar caracteres individuales | `StringBuilder` | `string` no lo permite |
| Unir los elementos de una colección | `string.Join` | Ya está optimizado internamente |

> `StringBuilder` no siempre es mejor: para unir 2 o 3 cadenas, crear el builder cuesta más que concatenar. Úsalo cuando el número de concatenaciones es grande o desconocido (típicamente, dentro de un ciclo).

---

## Resumen de la unidad

- **Tipos por valor vs por referencia:** los primeros guardan el dato; los segundos guardan una dirección. Copiar una referencia no copia el objeto.
- **Primitivos:** `int` y `double` son la opción por defecto; `decimal` para dinero porque `double` guarda aproximaciones binarias. El rango de cada tipo depende de sus bits (`2ⁿ` valores).
- **Conversiones:** implícitas cuando no hay pérdida; explícitas `(T)` truncan y pueden desbordarse; `TryParse` es la forma segura de leer números del usuario.
- **`const` vs `readonly`:** `const` se fija al compilar y solo admite tipos simples; `readonly` se fija en el constructor, admite cualquier tipo y protege la referencia, no el contenido.
- **Aritméticos:** `int / int` da `int`; `%` da el residuo; `x++` regresa el valor anterior y `++x` el nuevo.
- **Relacionales:** siempre regresan `bool`; no confundas `=` con `==`; compara `double` con tolerancia.
- **Lógicos:** `&&` y `||` hacen cortocircuito, lo que permite proteger una condición con la anterior.
- **`string` es inmutable:** cada operación crea una cadena nueva. Para construir texto en ciclos, usa `StringBuilder`, que modifica un búfer interno.

*Universidad Politécnica de Tecamac — Ingeniería en Software*

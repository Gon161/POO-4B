# Unidad 1. Tipos de Datos, Operadores y Cadenas

## 1.1. Tipos de datos en programación

Un **tipo de dato** indica qué clase de valor puede guardar una variable, cuánta memoria ocupa y qué operaciones se pueden hacer con ella. C# es un lenguaje **fuertemente tipado**: cada variable tiene un tipo fijo que el compilador verifica.

```csharp
int edad = 20;          // número entero
double precio = 99.50;  // número con decimales
string nombre = "Ana";  // texto
bool activo = true;     // verdadero o falso
```

### 1.1.1. Datos primitivos

Los tipos primitivos (o *tipos integrados*) son los que el lenguaje trae de serie. Son **tipos por valor**: la variable contiene directamente el dato.

#### Enteros

| Tipo | Tamaño | Rango aproximado | Uso típico |
|---|---|---|---|
| `byte` | 1 byte | 0 a 255 | Datos binarios, valores pequeños sin signo |
| `short` | 2 bytes | −32 768 a 32 767 | Rara vez se usa |
| `int` | 4 bytes | ±2 100 millones | **Opción por defecto** para enteros |
| `long` | 8 bytes | ±9.2 × 10¹⁸ | Valores muy grandes (IDs, milisegundos) |

#### Reales (con decimales)

| Tipo | Tamaño | Precisión | Sufijo | Uso típico |
|---|---|---|---|---|
| `float` | 4 bytes | ~7 dígitos | `f` | Gráficos, juegos |
| `double` | 8 bytes | ~15-16 dígitos | `d` (opcional) | **Opción por defecto** para decimales |
| `decimal` | 16 bytes | 28-29 dígitos | `m` | **Dinero** y cálculos financieros |

```csharp
float temperatura = 36.6f;   // sin la f no compila
double distancia = 1234.5678;
decimal saldo = 1500.75m;    // sin la m no compila
```

> ⚠️ `double` no representa exactamente todos los decimales: `0.1 + 0.2` da `0.30000000000000004`. Para dinero usa `decimal`.

#### Otros primitivos

| Tipo | Valores | Ejemplo |
|---|---|---|
| `bool` | `true` / `false` | `bool aprobado = true;` |
| `char` | Un solo carácter Unicode, entre comillas simples | `char inicial = 'A';` |

> `string` **no** es un tipo primitivo por valor: es un tipo por referencia. Lo vemos a fondo en la sección 1.3.

#### Inferencia con `var`

```csharp
var cantidad = 10;      // el compilador deduce int
var mensaje = "Hola";   // deduce string
```

`var` no hace la variable "dinámica": el tipo se fija en la compilación y no puede cambiar.

#### Valores mínimos y máximos

```csharp
Console.WriteLine(int.MaxValue);   // → 2147483647
Console.WriteLine(int.MinValue);   // → -2147483648
Console.WriteLine(byte.MaxValue);  // → 255
```

### 1.1.2. Conversión de tipos

Convertir es pasar un valor de un tipo a otro. Hay cuatro formas principales.

#### a) Conversión implícita (automática)

Ocurre cuando **no hay riesgo de perder información** (de un tipo "pequeño" a uno "grande").

```csharp
int entero = 100;
long largo = entero;      // int → long
double real = entero;     // int → double
```

#### b) Conversión explícita (*cast*)

Cuando puede perderse información, hay que pedirlo con `(tipo)`.

```csharp
double pi = 3.99;
int truncado = (int)pi;   // → 3 (se trunca, NO se redondea)

int grande = 300;
byte chico = (byte)grande; // → 44 (desbordamiento: 300 - 256)
```

#### c) Métodos `Parse` y `TryParse` (texto → número)

```csharp
string texto = "42";
int numero = int.Parse(texto);      // → 42

int.Parse("hola");                  // ⚠️ lanza FormatException
```

`TryParse` es la forma **segura**: devuelve `true` o `false` en lugar de lanzar una excepción.

```csharp
Console.Write("Ingresa tu edad: ");
string? entrada = Console.ReadLine();

if (int.TryParse(entrada, out int edad))
{
    Console.WriteLine($"Tienes {edad} años");
}
else
{
    Console.WriteLine("Eso no es un número válido");
}
```

#### d) Clase `Convert`

```csharp
int a = Convert.ToInt32("123");     // → 123
int b = Convert.ToInt32(4.6);       // → 5 (redondea)
bool c = Convert.ToBoolean("true"); // → true
string d = Convert.ToString(3.14);  // → "3.14"
```

| Método | Si el valor no es válido | Redondea |
|---|---|---|
| `(int)x` | Trunca / desborda | No, trunca |
| `int.Parse(s)` | Lanza excepción | — |
| `int.TryParse(s, out n)` | Devuelve `false` | — |
| `Convert.ToInt32(x)` | Lanza excepción | Sí (al par más cercano) |

#### Número → texto

```csharp
int puntos = 85;
string s1 = puntos.ToString();         // "85"
string s2 = 1234.5.ToString("N2");     // "1,234.50" (según cultura)
string s3 = 0.256.ToString("P1");      // "25.6 %"
```

### 1.1.3. Constantes y variables (`const` y `readonly`)

#### Variables

Una **variable** es un espacio con nombre cuyo valor puede cambiar.

```csharp
int contador = 0;
contador = contador + 1;
```

Convenciones de nombres en C#:

- Variables locales y parámetros: `camelCase` → `totalVentas`.
- Constantes, propiedades, métodos y clases: `PascalCase` → `TasaIva`.

#### `const`: constante de compilación

- Su valor se fija **al escribir el código** y nunca cambia.
- Debe inicializarse en la declaración.
- Solo admite tipos primitivos, `string` o `null`.
- Es implícitamente `static`.

```csharp
const double Pi = 3.14159;
const int DiasSemana = 7;
const string NombreApp = "Inventario";

// Pi = 3.14;  ⚠️ error de compilación
```

#### `readonly`: constante de ejecución

- Es un **campo de clase** (no se usa en variables locales).
- Puede asignarse en la declaración **o en el constructor**.
- Después del constructor ya no puede cambiar.
- Admite cualquier tipo, incluidos objetos.

```csharp
public class Factura
{
    public readonly DateTime FechaEmision;
    public readonly string Folio;

    public Factura(string folio)
    {
        Folio = folio;
        FechaEmision = DateTime.Now;   // valor conocido solo al ejecutar
    }

    public void CambiarFolio()
    {
        // Folio = "X";  ⚠️ error: solo se asigna en el constructor
    }
}
```

#### `const` vs `readonly`

| Característica | `const` | `readonly` |
|---|---|---|
| ¿Cuándo se conoce el valor? | Al compilar | Al ejecutar |
| ¿Dónde se asigna? | Solo en la declaración | Declaración o constructor |
| ¿Puede ser local? | Sí | No (solo campos) |
| Tipos permitidos | Primitivos y `string` | Cualquiera |
| ¿Es `static`? | Siempre | Solo si se declara `static readonly` |
| Ejemplo | `const int MaxIntentos = 3;` | `readonly DateTime creado = DateTime.Now;` |

> Regla práctica: usa `const` para valores universales que nunca cambiarán (π, días de la semana) y `readonly` para valores que se calculan una vez por objeto o que dependen de la ejecución.

---

## 1.2. Operadores

Un **operador** es un símbolo que realiza una operación sobre uno o más valores (operandos) y produce un resultado.

### 1.2.1. Operadores aritméticos

| Operador | Operación | Ejemplo | Resultado |
|---|---|---|---|
| `+` | Suma | `7 + 2` | `9` |
| `-` | Resta | `7 - 2` | `5` |
| `*` | Multiplicación | `7 * 2` | `14` |
| `/` | División | `7 / 2` | `3` ⚠️ |
| `%` | Módulo (residuo) | `7 % 2` | `1` |

#### División entera vs decimal

```csharp
Console.WriteLine(7 / 2);      // → 3   (int / int = int)
Console.WriteLine(7 / 2.0);    // → 3.5 (si uno es double, el resultado es double)
Console.WriteLine((double)7 / 2); // → 3.5
```

> ⚠️ Error clásico al calcular promedios: `int suma = 15; int n = 4; double prom = suma / n;` da `3`, no `3.75`. Solución: `double prom = (double)suma / n;`

#### Usos comunes de `%`

```csharp
bool esPar = numero % 2 == 0;
int ultimoDigito = 1234 % 10;     // → 4
int minutos = 135 % 60;           // → 15
```

#### Incremento y decremento

```csharp
int x = 5;
x++;   // x = 6
x--;   // x = 5

int a = 5;
int b = a++;   // b = 5, a = 6  (postincremento: usa y luego suma)
int c = ++a;   // c = 7, a = 7  (preincremento: suma y luego usa)
```

#### Operadores de asignación compuesta

| Forma corta | Equivale a |
|---|---|
| `x += 3` | `x = x + 3` |
| `x -= 3` | `x = x - 3` |
| `x *= 3` | `x = x * 3` |
| `x /= 3` | `x = x / 3` |
| `x %= 3` | `x = x % 3` |

#### Clase `Math`

```csharp
Math.Pow(2, 10);     // → 1024
Math.Sqrt(81);       // → 9
Math.Abs(-5);        // → 5
Math.Round(3.456, 2);// → 3.46
Math.Max(4, 9);      // → 9
Math.Floor(3.9);     // → 3
Math.Ceiling(3.1);   // → 4
```

### 1.2.2. Operadores relacionales

Comparan dos valores y siempre devuelven un `bool`.

| Operador | Significado | Ejemplo | Resultado |
|---|---|---|---|
| `==` | Igual a | `5 == 5` | `true` |
| `!=` | Distinto de | `5 != 3` | `true` |
| `>` | Mayor que | `5 > 8` | `false` |
| `<` | Menor que | `5 < 8` | `true` |
| `>=` | Mayor o igual | `5 >= 5` | `true` |
| `<=` | Menor o igual | `4 <= 3` | `false` |

```csharp
int calificacion = 75;
bool aprobo = calificacion >= 70;   // true
```

> ⚠️ No confundas `=` (asignación) con `==` (comparación).

Comparar cadenas:

```csharp
string a = "Hola";
string b = "hola";
Console.WriteLine(a == b);                                          // → False
Console.WriteLine(a.Equals(b, StringComparison.OrdinalIgnoreCase)); // → True
```

### 1.2.3. Operadores lógicos

Combinan expresiones booleanas.

| Operador | Nombre | Devuelve `true` cuando… |
|---|---|---|
| `&&` | Y (AND) | **ambas** condiciones son verdaderas |
| `\|\|` | O (OR) | **al menos una** es verdadera |
| `!` | NO (NOT) | la condición es falsa |
| `^` | O exclusivo (XOR) | **exactamente una** es verdadera |

#### Tablas de verdad

| A | B | `A && B` | `A \|\| B` | `A ^ B` |
|---|---|---|---|---|
| true | true | true | true | false |
| true | false | false | true | true |
| false | true | false | true | true |
| false | false | false | false | false |

```csharp
int edad = 20;
bool tieneCredencial = true;

bool puedeEntrar = edad >= 18 && tieneCredencial;   // true
bool descuento = edad < 12 || edad >= 60;           // false
bool menor = !(edad >= 18);                         // false
```

#### Evaluación en cortocircuito

`&&` y `||` dejan de evaluar en cuanto conocen el resultado. Esto evita errores:

```csharp
string? nombre = null;

// Si nombre es null, NO se evalúa nombre.Length → no hay excepción
if (nombre != null && nombre.Length > 3)
{
    Console.WriteLine("Nombre largo");
}
```

#### Precedencia de operadores (de mayor a menor)

1. `()` paréntesis
2. `!`, `++`, `--` (unarios)
3. `*`, `/`, `%`
4. `+`, `-`
5. `<`, `>`, `<=`, `>=`
6. `==`, `!=`
7. `&&`
8. `||`
9. `=`, `+=`, `-=`… (asignación)

```csharp
bool r = 3 + 2 * 4 > 10 && !false;   // 3 + 8 = 11 > 10 → true && true → true
```

> Ante la duda, usa paréntesis: hacen el código más legible.

---

## 1.3. Cadenas de caracteres

### 1.3.1. `String` y métodos principales

Un `string` es una secuencia de caracteres (`char`). `string` y `String` son lo mismo (`string` es el alias de `System.String`).

```csharp
string saludo = "Hola mundo";
char primera = saludo[0];        // 'H'
int largo = saludo.Length;       // 10
```

#### Inmutabilidad

Los `string` son **inmutables**: ningún método modifica la cadena original; siempre se devuelve una **nueva**.

```csharp
string s = "hola";
s.ToUpper();               // ⚠️ no cambia s
Console.WriteLine(s);      // → hola

s = s.ToUpper();           // hay que reasignar
Console.WriteLine(s);      // → HOLA
```

#### Formas de construir cadenas

```csharp
string nombre = "Luis";
int edad = 21;

string a = "Hola " + nombre + ", tienes " + edad + " años"; // concatenación
string b = string.Format("Hola {0}, tienes {1} años", nombre, edad);
string c = $"Hola {nombre}, tienes {edad} años";            // interpolación (recomendada)
string d = $"Precio: {1234.5:C}";                           // con formato
string ruta = @"C:\Users\Ana\Documentos";                   // literal textual (sin escapes)
```

Secuencias de escape comunes: `\n` (salto de línea), `\t` (tabulación), `\"` (comillas), `\\` (barra invertida).

#### Métodos principales

| Método / propiedad | Descripción | Ejemplo con `s = "  Hola Mundo  "` | Resultado |
|---|---|---|---|
| `Length` | Número de caracteres | `s.Length` | `14` |
| `ToUpper()` / `ToLower()` | Mayúsculas / minúsculas | `"Hola".ToUpper()` | `"HOLA"` |
| `Trim()` | Quita espacios al inicio y al final | `s.Trim()` | `"Hola Mundo"` |
| `TrimStart()` / `TrimEnd()` | Quita espacios de un lado | `s.TrimStart()` | `"Hola Mundo  "` |
| `Contains(x)` | ¿Contiene el texto? | `"Hola".Contains("ol")` | `true` |
| `StartsWith(x)` / `EndsWith(x)` | ¿Empieza / termina con? | `"Hola".StartsWith("H")` | `true` |
| `IndexOf(x)` | Posición de la primera aparición (−1 si no está) | `"Hola".IndexOf('l')` | `2` |
| `LastIndexOf(x)` | Posición de la última aparición | `"banana".LastIndexOf('a')` | `5` |
| `Substring(i, n)` | Extrae `n` caracteres desde `i` | `"Programar".Substring(0, 3)` | `"Pro"` |
| `Replace(a, b)` | Reemplaza todas las apariciones | `"a-b-c".Replace("-", "/")` | `"a/b/c"` |
| `Split(sep)` | Divide en un arreglo | `"a,b,c".Split(',')` | `["a","b","c"]` |
| `string.Join(sep, arr)` | Une un arreglo en una cadena | `string.Join("-", arr)` | `"a-b-c"` |
| `Insert(i, x)` | Inserta texto en una posición | `"Hla".Insert(1, "o")` | `"Hola"` |
| `Remove(i, n)` | Elimina `n` caracteres desde `i` | `"Hola".Remove(1, 2)` | `"Ha"` |
| `PadLeft(n, c)` / `PadRight(n, c)` | Rellena hasta `n` caracteres | `"7".PadLeft(3, '0')` | `"007"` |
| `Equals(x, comp)` | Compara con opciones | ver 1.2.2 | — |
| `string.IsNullOrEmpty(s)` | ¿Es `null` o `""`? | `string.IsNullOrEmpty("")` | `true` |
| `string.IsNullOrWhiteSpace(s)` | ¿Es `null`, `""` o solo espacios? | `string.IsNullOrWhiteSpace("  ")` | `true` |
| `ToCharArray()` | Convierte a arreglo de `char` | `"abc".ToCharArray()` | `['a','b','c']` |

#### Ejemplo integrador

```csharp
string correo = "  Ana.Lopez@Escuela.edu.mx  ";

correo = correo.Trim().ToLower();
int arroba = correo.IndexOf('@');

string usuario = correo.Substring(0, arroba);
string dominio = correo.Substring(arroba + 1);

Console.WriteLine($"Usuario: {usuario}");   // → ana.lopez
Console.WriteLine($"Dominio: {dominio}");   // → escuela.edu.mx
Console.WriteLine(correo.EndsWith(".mx"));  // → True
```

#### Recorrer una cadena

```csharp
string palabra = "programacion";
int vocales = 0;

foreach (char c in palabra)
{
    if ("aeiou".Contains(c))
        vocales++;
}

Console.WriteLine($"Vocales: {vocales}");   // → 5
```

### 1.3.2. `StringBuilder` y manipulación eficiente de cadenas

#### El problema

Como `string` es inmutable, cada concatenación **crea un objeto nuevo** en memoria y copia todo el contenido anterior:

```csharp
string resultado = "";
for (int i = 0; i < 10000; i++)
{
    resultado += i + ",";   // ⚠️ crea 10 000 cadenas intermedias
}
```

Con muchas iteraciones esto es lento y consume mucha memoria.

#### La solución: `StringBuilder`

`StringBuilder` (espacio de nombres `System.Text`) es una cadena **mutable**: modifica un búfer interno sin crear objetos nuevos en cada operación.

```csharp
using System.Text;

var sb = new StringBuilder();
for (int i = 0; i < 10000; i++)
{
    sb.Append(i).Append(',');
}
string resultado = sb.ToString();   // se convierte a string al final
```

#### Métodos principales

| Método | Descripción | Ejemplo |
|---|---|---|
| `Append(x)` | Agrega al final | `sb.Append("Hola");` |
| `AppendLine(x)` | Agrega y un salto de línea | `sb.AppendLine("Fila 1");` |
| `AppendFormat(fmt, …)` | Agrega con formato | `sb.AppendFormat("{0:N2}", 3.5);` |
| `Insert(i, x)` | Inserta en una posición | `sb.Insert(0, ">> ");` |
| `Remove(i, n)` | Elimina `n` caracteres desde `i` | `sb.Remove(0, 3);` |
| `Replace(a, b)` | Reemplaza texto | `sb.Replace(",", ";");` |
| `Clear()` | Vacía el contenido | `sb.Clear();` |
| `Length` | Longitud actual (se puede asignar para recortar) | `sb.Length--;` |
| `sb[i]` | Lee o **modifica** un carácter | `sb[0] = 'h';` |
| `ToString()` | Obtiene el `string` final | `string s = sb.ToString();` |

#### Ejemplo: generar un reporte

```csharp
using System.Text;

string[] productos = { "Lápiz", "Cuaderno", "Borrador" };
decimal[] precios = { 8.50m, 45.00m, 6.00m };

var reporte = new StringBuilder();
reporte.AppendLine("=== REPORTE DE PRODUCTOS ===");

for (int i = 0; i < productos.Length; i++)
{
    reporte.AppendLine($"{productos[i],-10} {precios[i],8:C}");
}

reporte.AppendLine(new string('-', 28));
reporte.Append("Total de productos: ").Append(productos.Length);

Console.WriteLine(reporte.ToString());
```

#### Ejemplo: invertir una cadena

```csharp
string original = "estructura";
var sb = new StringBuilder();

for (int i = original.Length - 1; i >= 0; i--)
{
    sb.Append(original[i]);
}

Console.WriteLine(sb);   // → arutcurtse
```

#### ¿Cuándo usar cada uno?

| Situación | Usa |
|---|---|
| Pocas concatenaciones (2-5) | `string` con interpolación `$"..."` |
| Texto que no cambia | `string` |
| Concatenar dentro de un bucle | `StringBuilder` |
| Construir textos largos por partes (reportes, HTML, CSV) | `StringBuilder` |
| Modificar caracteres individuales muchas veces | `StringBuilder` |

---

## Resumen

- Los tipos primitivos (`int`, `double`, `decimal`, `bool`, `char`…) guardan valores directamente; elige `int` y `double` por defecto, y `decimal` para dinero.
- La conversión implícita es automática y segura; la explícita (`cast`) puede perder datos. Para convertir texto ingresado por el usuario usa `TryParse`.
- `const` se fija al compilar; `readonly` se fija al construir el objeto.
- Cuidado con la división entera (`7 / 2 == 3`) y con confundir `=` y `==`.
- `&&` y `||` se evalúan en cortocircuito.
- `string` es inmutable: sus métodos devuelven cadenas nuevas. Para construir texto en bucles usa `StringBuilder`.

---

## Ejercicios

> Resuélvelos en `practica/Unidad1/`.

1. **Calculadora de IMC.** Pide peso (kg) y estatura (m) con `TryParse`, calcula `peso / estatura²` y muestra el resultado con 2 decimales.
2. **Conversor de temperatura.** Declara `const double FactorF = 1.8;` y convierte grados Celsius a Fahrenheit (`C * 1.8 + 32`).
3. **Par o impar y múltiplo.** Lee un número y muestra si es par, y si es múltiplo de 3 **y** de 5 a la vez.
4. **Año bisiesto.** Un año es bisiesto si es divisible entre 4 y no entre 100, o si es divisible entre 400. Escríbelo con una sola expresión lógica.
5. **Clase `CuentaBancaria`.** Con un campo `readonly string NumeroCuenta` asignado en el constructor y una `const decimal SaldoMinimo = 500m`.
6. **Analizador de texto.** Pide una frase y muestra: número de caracteres sin espacios, número de palabras (`Split`), la frase en mayúsculas y cuántas veces aparece la letra `a`.
7. **Palíndromo.** Determina si una palabra se lee igual al revés (ignora mayúsculas y espacios).
8. **Iniciales.** A partir de `"juan carlos pérez gómez"` genera `"J.C.P.G."` usando `StringBuilder`.
9. **Comparación de rendimiento.** Concatena 50 000 números con `string +=` y con `StringBuilder`; mide ambos tiempos con `System.Diagnostics.Stopwatch` y compáralos.

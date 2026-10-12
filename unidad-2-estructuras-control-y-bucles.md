# Unidad 2 — Estructuras de Control y Bucles

Por defecto, un programa ejecuta sus instrucciones **una tras otra, de arriba hacia abajo**. Pero casi ningún problema real se resuelve así: hay que **decidir** qué hacer según los datos (¿aprobó o reprobó?), **repetir** acciones (procesar cada alumno de un grupo) y, a veces, **interrumpir** una repetición (dejar de buscar cuando ya se encontró). Las estructuras de control son las herramientas que permiten alterar ese orden secuencial. Todos los ejemplos están en **C#**.

---

## Índice

1. [2.1. Estructuras de control de flujo](#21-estructuras-de-control-de-flujo)
2. [2.2. Estructuras repetitivas (bucles)](#22-estructuras-repetitivas-bucles)
   - [2.2.1. Bucle while](#221-bucle-while)
   - [2.2.2. Bucle do-while](#222-bucle-do-while)
   - [2.2.3. Bucle for](#223-bucle-for)
3. [2.3. Comparación y casos de uso de los bucles](#23-comparación-y-casos-de-uso-de-los-bucles)
4. [2.4. Control de flujo](#24-control-de-flujo)
   - [2.4.1. Uso de break](#241-uso-de-break)
   - [2.4.2. Uso de continue](#242-uso-de-continue)

---

## 2.1. Estructuras de control de flujo

El **flujo de control** es el orden en que se ejecutan las instrucciones de un programa. Cualquier algoritmo, por complejo que sea, se puede construir combinando solo tres estructuras básicas (teorema de la **programación estructurada**, Böhm-Jacopini):

| Estructura | Qué hace | En C# |
|---|---|---|
| **Secuencia** | Ejecuta las instrucciones una tras otra | Instrucciones separadas por `;` |
| **Selección** (condicional) | Elige un camino según una condición | `if`, `if-else`, `switch`, `? :` |
| **Repetición** (iteración) | Repite un bloque mientras se cumpla una condición | `while`, `do-while`, `for`, `foreach` |

```
   Secuencia             Selección                  Repetición

      │                     │                          │
      ▼                     ▼                     ┌───►▼
  ┌───────┐            ◇ ¿condición? ◇            │  ◇ ¿condición? ◇──── no ───┐
  │ paso 1│           sí /        \ no            │       │ sí                 │
  └───┬───┘             ▼          ▼              │       ▼                    │
      ▼            ┌────────┐ ┌────────┐          │  ┌─────────┐               │
  ┌───────┐        │bloque A│ │bloque B│          └──┤ cuerpo  │               │
  │ paso 2│        └───┬────┘ └───┬────┘             └─────────┘               │
  └───┬───┘            └────┬─────┘                                          ▼
      ▼                     ▼                                             (sigue)
```

Una **condición** es cualquier expresión que da como resultado un `bool`: una comparación (`edad >= 18`), una combinación lógica (`a > 0 && b > 0`), una variable `bool` o una llamada a método que regrese `bool` (`lista.Contains(x)`).

> En C# la condición **debe** ser de tipo `bool`. No se puede escribir `if (numero)` como en C para preguntar "si es distinto de cero"; hay que escribir `if (numero != 0)`.

**`if`**: ejecuta un bloque solo si la condición es `true`. Si es `false`, el bloque se salta y el programa continúa después.

```csharp
int calificacion = 85;
if (calificacion >= 70)
{
    Console.WriteLine("Aprobado");
}
Console.WriteLine("Fin");   // se ejecuta siempre
```

**`if-else`**: elige **exactamente uno** de dos caminos.

```csharp
if (calificacion >= 70)
    Console.WriteLine("Aprobado");
else
    Console.WriteLine("Reprobado");
```

> Las llaves `{ }` son opcionales si el bloque tiene una sola instrucción, pero conviene ponerlas siempre: si después agregas una segunda línea sin llaves, esa línea quedará **fuera** del `if` y se ejecutará siempre.

```csharp
if (calificacion >= 70)
    Console.WriteLine("Aprobado");
    Console.WriteLine("¡Felicidades!");   // ¡se ejecuta SIEMPRE! La sangría no importa
```

**`if - else if - else`**: varias condiciones en cascada. Se evalúan **en orden** y se ejecuta solo la **primera** que se cumpla; las demás ya no se revisan.

```csharp
if (calificacion >= 90)
    Console.WriteLine("Excelente");
else if (calificacion >= 80)
    Console.WriteLine("Muy bien");      // con 85 entra aquí y termina
else if (calificacion >= 70)
    Console.WriteLine("Suficiente");    // también se cumpliría, pero ya no se revisa
else
    Console.WriteLine("Reprobado");
```

> Por eso el **orden** de las condiciones importa. Si pusieras primero `>= 70`, una calificación de 95 diría "Suficiente".

**`if` anidados**: un `if` dentro de otro. Útil cuando la segunda pregunta solo tiene sentido si la primera se cumplió.

```csharp
if (usuario == "admin")
{
    if (password == "1234")
        Console.WriteLine("Bienvenido");
    else
        Console.WriteLine("Contraseña incorrecta");
}
else
{
    Console.WriteLine("Usuario no encontrado");
}
```

> Muchos `if` anidados pueden simplificarse con `&&`. Si el anidamiento pasa de 2 o 3 niveles, el código se vuelve difícil de leer.

**Operador ternario `? :`**: un `if-else` corto que **regresa un valor**. Se lee "¿condición? entonces esto : si no, esto".

```csharp
string estado = calificacion >= 70 ? "Aprobado" : "Reprobado";
int mayor = a > b ? a : b;
```

**`switch`**: compara **una** expresión contra varios valores constantes y salta directamente al caso que coincide.

```csharp
int dia = 3;
switch (dia)
{
    case 1:
        Console.WriteLine("Lunes");
        break;
    case 2:
        Console.WriteLine("Martes");
        break;
    case 3:
        Console.WriteLine("Miércoles");
        break;
    case 6:
    case 7:                         // varios case comparten bloque
        Console.WriteLine("Fin de semana");
        break;
    default:                        // si ningún case coincide
        Console.WriteLine("Otro día");
        break;
}
```

> En C# cada `case` con código **debe** terminar en `break`, `return`, `throw` o `goto case`. No existe el "fall-through" accidental de C/Java (pasar al siguiente caso por olvidar el `break`); solo se pueden apilar `case` vacíos.

**`switch` con patrones y `when`** (C# 9+): permite comparar rangos y agregar condiciones.

```csharp
switch (calificacion)
{
    case >= 90:
        Console.WriteLine("A");
        break;
    case >= 80:
        Console.WriteLine("B");
        break;
    case int n when n < 0:
        Console.WriteLine("Calificación inválida");
        break;
    default:
        Console.WriteLine("C o menos");
        break;
}
```

**Expresión `switch`** (C# 8+): versión compacta que **regresa un valor**.

```csharp
string nombreDia = dia switch
{
    1 => "Lunes",
    2 => "Martes",
    3 => "Miércoles",
    6 or 7 => "Fin de semana",
    _ => "Otro día"          // _ equivale a default
};

string letra = calificacion switch
{
    >= 90 => "A",
    >= 80 => "B",
    >= 70 => "C",
    _ => "F"
};
```

| Usa… | Cuando… |
|---|---|
| `if` / `else if` | Las condiciones son rangos o expresiones complejas, o involucran varias variables |
| `switch` | Comparas **una** variable contra muchos valores concretos (menús, códigos) |
| Expresión `switch` | Quieres asignar un valor según varios casos |
| Ternario | Solo hay dos opciones y el resultado es un valor |

---

## 2.2. Estructuras repetitivas (bucles)

Un **bucle** (ciclo o *loop*) repite un bloque de instrucciones mientras se cumpla una condición. Cada repetición se llama **iteración** (o "vuelta"). Sin bucles, procesar 1,000 datos requeriría escribir 1,000 veces la misma instrucción.

Todo bucle bien construido tiene tres elementos:

| Elemento | Qué es | Ejemplo |
|---|---|---|
| **Inicialización** | Valor inicial de la variable de control | `int i = 1;` |
| **Condición** | Se evalúa en cada vuelta; si es `false`, el bucle termina | `i <= 10` |
| **Actualización** | Cambia la variable de control para que algún día la condición sea `false` | `i++` |

```
            inicialización
                  │
                  ▼
        ┌──► ◇ ¿condición? ◇ ── false ──► sale del bucle
        │         │ true
        │         ▼
        │     [ cuerpo ]
        │         │
        │         ▼
        └── actualización
```

> Si la actualización falta o nunca hace falsa la condición, el bucle se repite para siempre: **bucle infinito**. El programa se "congela" o consume toda la CPU.

```csharp
int i = 1;
while (i <= 5)
{
    Console.WriteLine(i);
    // falta i++  → imprime 1 para siempre
}
```

**Patrones de variables dentro de bucles.** Casi todos los problemas con bucles usan alguno de estos papeles:

| Papel | Qué hace | Inicia en | Ejemplo |
|---|---|---|---|
| **Contador** | Cuenta cuántas veces ocurre algo | `0` | `aprobados++;` |
| **Acumulador** | Va sumando (o multiplicando) valores | `0` para sumas, `1` para productos | `suma += nota;` / `factorial *= i;` |
| **Bandera** | Recuerda si algo ocurrió | `false` (o `true`) | `encontrado = true;` |
| **Centinela** | Valor especial que indica "terminar" | — | Leer hasta que el usuario escriba `0` |

### 2.2.1. Bucle while

`while` ("mientras") evalúa la condición **antes** de cada iteración. Si la condición es falsa desde el principio, el cuerpo **no se ejecuta ni una vez**. Por eso se dice que es un ciclo de **0 a n** iteraciones.

```
while (condición)
{
    // cuerpo
}
```

```
      ┌──► ◇ ¿condición? ◇ ── false ──► (sigue el programa)
      │          │ true
      │          ▼
      └──── [ cuerpo ]
```

Características:

| Característica | Descripción |
|---|---|
| Evalúa la condición | **Antes** de cada vuelta |
| Mínimo de ejecuciones | 0 |
| Inicialización y actualización | Fuera de la cabecera (antes del bucle y dentro del cuerpo) |
| Uso típico | No se sabe de antemano cuántas vueltas habrá |

```csharp
int contador = 1;               // inicialización
while (contador <= 5)           // condición
{
    Console.WriteLine(contador);
    contador++;                 // actualización
}
```

**Prueba de escritorio** (seguir el bucle a mano):

| Vuelta | `contador` antes | `contador <= 5` | Imprime | `contador` después |
|---|---|---|---|---|
| 1 | 1 | `true` | 1 | 2 |
| 2 | 2 | `true` | 2 | 3 |
| 3 | 3 | `true` | 3 | 4 |
| 4 | 4 | `true` | 4 | 5 |
| 5 | 5 | `true` | 5 | 6 |
| — | 6 | `false` | sale | — |

> La condición se evalúa **una vez más** que el número de vueltas: la última evaluación es la que da `false` y termina el bucle.

**Número de dígitos.** Caso típico de `while`: no sabemos cuántas vueltas dará, depende del número.

```csharp
int numero = 58231;
int digitos = 0;
while (numero > 0)
{
    numero /= 10;     // quita el último dígito: 58231 → 5823 → 582 → 58 → 5 → 0
    digitos++;
}
Console.WriteLine(digitos);   // 5
```

**Validar entrada del usuario.** Se repite la pregunta **mientras** el dato sea inválido.

```csharp
Console.Write("Ingresa tu edad: ");
int edad;
while (!int.TryParse(Console.ReadLine(), out edad) || edad < 0 || edad > 120)
{
    Console.Write("Dato inválido, intenta de nuevo: ");
}
Console.WriteLine($"Edad registrada: {edad}");
```

**Bucle con centinela.** Se leen datos hasta encontrar un valor especial que indica el final.

```csharp
int suma = 0, cantidad = 0;
Console.Write("Número (0 para terminar): ");
int n = int.Parse(Console.ReadLine());

while (n != 0)                  // 0 es el centinela
{
    suma += n;                  // acumulador
    cantidad++;                 // contador
    Console.Write("Número (0 para terminar): ");
    n = int.Parse(Console.ReadLine());
}

if (cantidad > 0)
    Console.WriteLine($"Promedio: {(double)suma / cantidad:F2}");
```

> Observa que el primer dato se lee **antes** del bucle y los siguientes al **final** del cuerpo. Así, si el primer dato ya es el centinela, el bucle no se ejecuta.

### 2.2.2. Bucle do-while

`do-while` ("hacer… mientras") ejecuta primero el cuerpo y evalúa la condición **después**. Por eso el cuerpo se ejecuta **al menos una vez**, aunque la condición sea falsa desde el principio. Es un ciclo de **1 a n** iteraciones.

```
do
{
    // cuerpo
} while (condición);     // ← lleva punto y coma
```

```
      ┌──► [ cuerpo ]
      │          │
      │          ▼
      └─ true ─ ◇ ¿condición? ◇ ── false ──► (sigue el programa)
```

Características:

| Característica | Descripción |
|---|---|
| Evalúa la condición | **Después** de cada vuelta |
| Mínimo de ejecuciones | 1 |
| Sintaxis | La condición va al final y termina en `;` |
| Uso típico | Menús, validar entrada, juegos de "¿otra vez?" |

**Diferencia con `while` cuando la condición es falsa desde el inicio:**

```csharp
int x = 10;

while (x < 5)
    Console.WriteLine("while");      // no imprime nada

do
    Console.WriteLine("do-while");   // imprime "do-while" una vez
while (x < 5);
```

**Menú de opciones.** El caso clásico: el menú **debe** mostrarse al menos una vez antes de saber si el usuario quiere salir.

```csharp
int opcion;
do
{
    Console.WriteLine("\n--- MENÚ ---");
    Console.WriteLine("1. Saludar");
    Console.WriteLine("2. Mostrar fecha");
    Console.WriteLine("0. Salir");
    Console.Write("Opción: ");
    int.TryParse(Console.ReadLine(), out opcion);

    switch (opcion)
    {
        case 1: Console.WriteLine("¡Hola!"); break;
        case 2: Console.WriteLine(DateTime.Now.ToShortDateString()); break;
        case 0: Console.WriteLine("Adiós"); break;
        default: Console.WriteLine("Opción inválida"); break;
    }
} while (opcion != 0);
```

**Validar entrada.** Con `do-while` no hace falta leer una vez antes del bucle:

```csharp
int calificacion;
do
{
    Console.Write("Calificación (0-10): ");
} while (!int.TryParse(Console.ReadLine(), out calificacion) || calificacion < 0 || calificacion > 10);
```

**Adivina el número**

```csharp
var rnd = new Random();
int secreto = rnd.Next(1, 101);      // entre 1 y 100
int intento, intentos = 0;

do
{
    Console.Write("Adivina (1-100): ");
    intento = int.Parse(Console.ReadLine());
    intentos++;

    if (intento < secreto) Console.WriteLine("Más alto");
    else if (intento > secreto) Console.WriteLine("Más bajo");
} while (intento != secreto);

Console.WriteLine($"¡Correcto! Lo lograste en {intentos} intentos");
```

> La variable que se usa en la condición del `do-while` (como `opcion` o `intento`) debe declararse **fuera** del `do { }`, porque la condición está fuera de las llaves y no vería una variable declarada dentro.

### 2.2.3. Bucle for

`for` ("para") reúne los tres elementos del bucle —**inicialización, condición y actualización**— en una sola línea. Es el bucle ideal cuando **se conoce el número de iteraciones** o cuando se necesita un **índice** para recorrer un arreglo.

```
for (inicialización; condición; actualización)
{
    // cuerpo
}
```

**Orden de ejecución:**

```
for (int i = 0; i < 3; i++)
      ①          ②      ④
{
    ③ cuerpo
}

① inicialización  → se ejecuta UNA sola vez, al principio
② condición       → se evalúa antes de cada vuelta
③ cuerpo          → se ejecuta si la condición es true
④ actualización   → se ejecuta al final de cada vuelta, luego regresa a ②

Secuencia: ① ② ③ ④ ② ③ ④ ② ③ ④ ② (false → sale)
```

Características:

| Característica | Descripción |
|---|---|
| Evalúa la condición | **Antes** de cada vuelta |
| Mínimo de ejecuciones | 0 |
| Inicialización y actualización | En la cabecera, todo a la vista |
| Ámbito de la variable de control | Solo existe dentro del `for` |
| Uso típico | Número de vueltas conocido, recorrer arreglos con índice, tablas |

**Equivalencia `for` ↔ `while`.** Todo `for` puede escribirse como `while`; el `for` solo agrupa las partes:

```csharp
for (int i = 0; i < 5; i++)          int i = 0;
{                                     while (i < 5)
    Console.WriteLine(i);             {
}                                         Console.WriteLine(i);
                                          i++;
                                      }
```

**Variaciones de la cabecera**

```csharp
for (int i = 0; i < 5; i++)
    Console.Write(i + " ");          // 0 1 2 3 4

for (int i = 1; i <= 5; i++)
    Console.Write(i + " ");          // 1 2 3 4 5

for (int i = 10; i > 0; i -= 2)
    Console.Write(i + " ");          // 10 8 6 4 2 (decreciente)

for (int i = 1; i <= 100; i *= 2)
    Console.Write(i + " ");          // 1 2 4 8 16 32 64 (multiplicando)

for (int i = 0, j = 10; i < j; i++, j--)
    Console.Write($"({i},{j}) ");    // dos variables: (0,10) (1,9) (2,8) (3,7) (4,6)
```

> **Cuántas vueltas da:** `for (int i = 0; i < n; i++)` da exactamente `n` vueltas (de `0` a `n-1`). `for (int i = 1; i <= n; i++)` también da `n` vueltas (de `1` a `n`). Mezclar ambas formas (`i = 0; i <= n`) da `n + 1` vueltas: es el origen del famoso error "**off-by-one**" (uno de más o de menos).

**Tabla de multiplicar**

```csharp
int n = 7;
for (int i = 1; i <= 10; i++)
    Console.WriteLine($"{n} x {i,2} = {n * i,3}");
```

**Contador y acumulador**

```csharp
int suma = 0;          // acumulador
int multiplos3 = 0;    // contador

for (int i = 1; i <= 100; i++)
{
    suma += i;
    if (i % 3 == 0) multiplos3++;
}

Console.WriteLine($"Suma 1..100 = {suma}");          // 5050
Console.WriteLine($"Múltiplos de 3 = {multiplos3}"); // 33
```

**Factorial** (acumulador de producto: empieza en `1`, no en `0`)

```csharp
int n = 5;
long factorial = 1;
for (int i = 2; i <= n; i++)
    factorial *= i;
Console.WriteLine($"{n}! = {factorial}");   // 5! = 120
```

| Vuelta | `i` | `factorial` antes | `factorial *= i` |
|---|---|---|---|
| 1 | 2 | 1 | 2 |
| 2 | 3 | 2 | 6 |
| 3 | 4 | 6 | 24 |
| 4 | 5 | 24 | 120 |

**Bucles anidados.** Un bucle dentro de otro. El bucle **interno** se ejecuta **completo** en cada vuelta del externo, así que el total de vueltas es el **producto** de ambos.

```csharp
for (int i = 1; i <= 3; i++)          // 3 vueltas
{
    for (int j = 1; j <= 4; j++)      // 4 vueltas por cada i
        Console.Write($"({i},{j}) ");
    Console.WriteLine();
}
// Total: 3 × 4 = 12 iteraciones del cuerpo interno
```

```
i = 1:  (1,1) (1,2) (1,3) (1,4)
i = 2:  (2,1) (2,2) (2,3) (2,4)
i = 3:  (3,1) (3,2) (3,3) (3,4)
```

```csharp
// Triángulo: el límite del interno depende del externo
for (int fila = 1; fila <= 5; fila++)
{
    for (int col = 1; col <= fila; col++)
        Console.Write("*");
    Console.WriteLine();
}
// *
// **
// ***
// ****
// *****
```

> La variable declarada en la cabecera (`int i`) **solo existe dentro del `for`**. Si necesitas su valor después del bucle, decláralo antes: `int i; for (i = 0; ...)`.

**`foreach`.** Existe un cuarto bucle, `foreach`, que recorre **cada elemento** de una colección sin manejar índices. Se estudia a fondo con arreglos (Unidad 3) y listas (Unidad 4).

```csharp
string[] frutas = { "Manzana", "Pera", "Uva" };
foreach (string fruta in frutas)
    Console.WriteLine(fruta);
```

---

## 2.3. Comparación y casos de uso de los bucles

Los tres bucles son **equivalentes en poder**: cualquier problema que se resuelva con uno se puede resolver con los otros. La diferencia está en qué tan **natural y legible** queda el código para cada tipo de problema.

| Característica | `while` | `do-while` | `for` | `foreach` |
|---|---|---|---|---|
| Evalúa la condición | Antes de cada vuelta | Después de cada vuelta | Antes de cada vuelta | Por cada elemento |
| Mínimo de ejecuciones | 0 | **1** | 0 | 0 |
| ¿Se conoce el número de vueltas? | Normalmente no | Normalmente no | **Sí** | Sí (los elementos) |
| Dónde va la inicialización | Antes del bucle | Antes del bucle | En la cabecera | Automática |
| Dónde va la actualización | En el cuerpo | En el cuerpo | En la cabecera | Automática |
| Riesgo de bucle infinito | Alto (olvidar actualizar) | Alto | Bajo (todo a la vista) | Ninguno |
| Uso típico | Centinelas, leer hasta fin, procesar dígitos | Menús, validar entrada | Contar, tablas, recorrer con índice | Recorrer colecciones |

**El mismo problema con los tres bucles** (sumar del 1 al 5):

```csharp
// while
int i = 1, s1 = 0;
while (i <= 5) { s1 += i; i++; }

// do-while
int j = 1, s2 = 0;
do { s2 += j; j++; } while (j <= 5);

// for
int s3 = 0;
for (int k = 1; k <= 5; k++) s3 += k;

// Los tres dan 15
```

> Aquí el `for` es el más claro, porque el número de repeticiones (5) se conoce de antemano y toda la lógica de control queda en una línea.

**Cuándo NO son equivalentes.** Si la condición es falsa desde el principio, `do-while` igual ejecuta una vez:

```csharp
int n = 0;    // el usuario no quiere sumar nada

int a = 0, i = 1;
while (i <= n) { a += i; i++; }        // a = 0  ✓

int b = 0, j = 1;
do { b += j; j++; } while (j <= n);    // b = 1  ✗ (sumó una vez de más)
```

**Guía para elegir**

```
¿Recorres todos los elementos de una colección sin necesitar el índice?
 ├── Sí ─────────────────────────────────────────────► foreach
 └── No
      └── ¿Sabes cuántas veces se repetirá (o necesitas un índice)?
           ├── Sí ────────────────────────────────────► for
           └── No
                └── ¿El cuerpo debe ejecutarse al menos una vez?
                     ├── Sí ──────────────────────────► do-while
                     └── No ──────────────────────────► while
```

| Problema | Bucle recomendado | Por qué |
|---|---|---|
| Imprimir la tabla del 7 | `for` | 10 vueltas conocidas |
| Recorrer un arreglo modificando elementos | `for` | Necesita el índice |
| Mostrar todos los nombres de una lista | `foreach` | Solo lectura, sin índice |
| Menú que se repite hasta elegir "Salir" | `do-while` | El menú se muestra al menos una vez |
| Pedir un dato hasta que sea válido | `do-while` o `while` | No se sabe cuántos intentos habrá |
| Leer números hasta que escriban 0 | `while` | Centinela; puede que el primero ya sea 0 |
| Contar los dígitos de un número | `while` | Depende del valor, no de un conteo |
| Calcular un factorial | `for` | Vueltas conocidas (de 2 a n) |

---

## 2.4. Control de flujo

Las **sentencias de salto** alteran el comportamiento normal de un bucle desde **dentro** del cuerpo, sin esperar a que la condición se vuelva falsa.

| Sentencia | Efecto |
|---|---|
| `break` | Termina **inmediatamente** el bucle (o `switch`) más cercano |
| `continue` | Salta el resto de la vuelta actual y pasa a la **siguiente** |
| `return` | Sale del método completo (y termina cualquier bucle) |

```
for (...)                          for (...)
{                                  {
    instrucción A;                     instrucción A;
    if (x) break;  ──────┐             if (x) continue; ──┐
    instrucción B;       │             instrucción B;     │
}                        │         } ◄────────────────────┘ (siguiente vuelta)
instrucción C; ◄─────────┘         instrucción C;
(sale del bucle)
```

### 2.4.1. Uso de break

`break` **termina el bucle de inmediato**. La ejecución continúa en la primera instrucción **después** del bucle, sin importar si la condición todavía era verdadera.

```csharp
for (int i = 1; i <= 10; i++)
{
    if (i == 5)
        break;
    Console.Write(i + " ");
}
// 1 2 3 4
```

| Vuelta | `i` | `i == 5` | Acción |
|---|---|---|---|
| 1 | 1 | `false` | imprime 1 |
| 2 | 2 | `false` | imprime 2 |
| 3 | 3 | `false` | imprime 3 |
| 4 | 4 | `false` | imprime 4 |
| 5 | 5 | `true` | **break** → sale (el 5 no se imprime) |

**Usos típicos de `break`:**

| Uso | Descripción |
|---|---|
| Búsqueda | Ya se encontró lo que se buscaba; seguir sería trabajo inútil |
| Bucle con salida en medio | `while (true)` que sale cuando se cumple una condición dentro del cuerpo |
| Detección de error | Un dato inválido hace que no tenga sentido continuar |
| `switch` | Obligatorio al final de cada `case` |

**Búsqueda: primer múltiplo común**

```csharp
int encontrado = -1;
for (int n = 100; n <= 200; n++)
{
    if (n % 7 == 0 && n % 11 == 0)
    {
        encontrado = n;
        break;            // ya no tiene sentido seguir buscando
    }
}
Console.WriteLine(encontrado);   // 154
```

**Número primo.** Basta un divisor para saber que **no** es primo; `break` evita revisar los demás.

```csharp
int numero = 29;
bool esPrimo = numero > 1;
for (int d = 2; d * d <= numero; d++)
{
    if (numero % d == 0)
    {
        esPrimo = false;
        break;
    }
}
Console.WriteLine($"{numero} es primo: {esPrimo}");   // True
```

> ¿Por qué `d * d <= numero`? Si un número tiene un divisor mayor que su raíz cuadrada, necesariamente tiene otro menor. Revisar hasta la raíz basta y ahorra muchas vueltas.

**`while (true)` con `break`.** Útil cuando la condición de salida se conoce **a la mitad** del cuerpo, no al principio ni al final.

```csharp
int suma = 0;
while (true)
{
    Console.Write("Número (0 para terminar): ");
    int n = int.Parse(Console.ReadLine());
    if (n == 0)
        break;            // la salida está en medio del cuerpo
    suma += n;
}
Console.WriteLine($"Suma total: {suma}");
```

> Compara con el ejemplo de centinela de 2.2.1: aquí no hace falta leer el primer dato antes del bucle. A cambio, la condición de salida no está en la cabecera y hay que buscarla dentro del cuerpo.

**`break` en bucles anidados.** `break` solo rompe el bucle **más interno** que lo contiene. El externo sigue normalmente.

```csharp
for (int i = 1; i <= 3; i++)
{
    for (int j = 1; j <= 3; j++)
    {
        if (j == 2) break;           // solo sale del for de j
        Console.Write($"({i},{j}) ");
    }
}
// (1,1) (2,1) (3,1)
```

Para salir de **ambos** bucles, las opciones son una **bandera** o poner los bucles en un método y usar `return`:

```csharp
// Con bandera
bool encontrado = false;
for (int i = 0; i < 5 && !encontrado; i++)
{
    for (int j = 0; j < 5; j++)
    {
        if (i * j == 6)
        {
            Console.WriteLine($"{i} x {j} = 6");
            encontrado = true;
            break;
        }
    }
}

// Con return (más limpio)
static (int, int) BuscarProducto(int objetivo)
{
    for (int i = 0; i < 5; i++)
        for (int j = 0; j < 5; j++)
            if (i * j == objetivo)
                return (i, j);       // sale de ambos bucles y del método
    return (-1, -1);
}
```

### 2.4.2. Uso de continue

`continue` **salta el resto de la vuelta actual** y pasa directamente a la **siguiente** iteración. El bucle **no termina**.

- En un `for`, `continue` salta a la **actualización** (`i++`) y luego a la condición.
- En un `while` o `do-while`, salta directamente a la **condición**.

```csharp
for (int i = 1; i <= 10; i++)
{
    if (i % 2 == 0)
        continue;         // los pares se saltan el Write
    Console.Write(i + " ");
}
// 1 3 5 7 9
```

| Vuelta | `i` | `i % 2 == 0` | Acción |
|---|---|---|---|
| 1 | 1 | `false` | imprime 1 |
| 2 | 2 | `true` | **continue** → salta a `i++` |
| 3 | 3 | `false` | imprime 3 |
| 4 | 4 | `true` | **continue** |
| … | … | … | … |

**Filtrar datos inválidos.** `continue` funciona como un "filtro" al inicio del cuerpo: los datos que no sirven se descartan y el resto del código solo trabaja con datos válidos.

```csharp
string[] datos = { "10", "abc", "25", "", "-3", "7" };
int suma = 0;

foreach (string d in datos)
{
    if (!int.TryParse(d, out int valor))
        continue;         // no es número: se ignora
    if (valor < 0)
        continue;         // negativo: se ignora

    suma += valor;        // aquí solo llegan números válidos y positivos
}

Console.WriteLine(suma);  // 42
```

> Este patrón ("cláusulas de guarda") evita anidar `if` dentro de `if`. Sin `continue`, el código quedaría como `if (es número) { if (no es negativo) { suma... } }`.

**Cuidado con `continue` en `while`.** En un `for` la actualización está en la cabecera y `continue` la respeta. En un `while`, la actualización está en el cuerpo y `continue` puede **saltársela**:

```csharp
int i = 0;
while (i < 10)
{
    if (i == 5)
        continue;         // ¡salta el i++! → i se queda en 5 para siempre
    Console.Write(i + " ");
    i++;
}
// Bucle infinito
```

```csharp
// Corrección: actualizar antes del continue
int i = 0;
while (i < 10)
{
    i++;
    if (i == 5)
        continue;
    Console.Write(i + " ");   // 1 2 3 4 6 7 8 9 10
}
```

**`break` vs `continue`**

| | `break` | `continue` |
|---|---|---|
| ¿Qué hace? | Sale del bucle | Salta a la siguiente vuelta |
| ¿El bucle sigue? | No | Sí |
| ¿A dónde salta? | A la instrucción después del bucle | A la actualización (`for`) o a la condición (`while`) |
| ¿Se usa en `switch`? | Sí (obligatorio en cada `case`) | No |
| Uso típico | Búsquedas, centinelas, salir al encontrar algo | Filtrar o ignorar elementos |
| En bucles anidados | Afecta solo al bucle más interno | Afecta solo al bucle más interno |

> Úsalos con moderación. Un bucle con muchos `break` y `continue` es difícil de seguir, porque hay muchas formas de "salir" del flujo normal. Si es posible, expresa la condición de salida en la cabecera del bucle.

---

## Resumen de la unidad

- **Programación estructurada:** todo algoritmo se construye con secuencia, selección y repetición.
- **Condicionales:** `if / else if / else` para rangos y condiciones complejas (el orden importa: gana la primera que se cumple); `switch` para comparar una variable contra valores concretos; ternario para dos opciones que devuelven un valor.
- **Todo bucle** necesita inicialización, condición y actualización; sin actualización se vuelve infinito.
- **`while`** evalúa antes (0 o más vueltas), **`do-while`** evalúa después (1 o más vueltas) y **`for`** agrupa todo en la cabecera, ideal con un número conocido de vueltas o con índices.
- **Bucles anidados:** el interno se ejecuta completo por cada vuelta del externo; el total es el producto.
- **Off-by-one:** `i = 0; i < n` y `i = 1; i <= n` dan `n` vueltas; mezclarlas da una de más o de menos.
- **`break`** termina el bucle más cercano; **`continue`** salta a la siguiente vuelta. Ambos afectan solo al bucle más interno, y `continue` en un `while` puede saltarse la actualización.

*Universidad Politécnica de Tecamac — Ingeniería en Software*

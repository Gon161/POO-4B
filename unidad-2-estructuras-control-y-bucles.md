# Unidad 2. Estructuras de Control y Bucles

## 2.1. Estructuras de control de flujo

Por defecto, un programa se ejecuta **de arriba hacia abajo**, una instrucción tras otra (flujo secuencial). Las **estructuras de control** permiten cambiar ese orden: elegir entre caminos (selección) o repetir bloques (iteración).

```
Secuencial      Selección             Repetición
    │               │                     │
    ▼               ▼                ┌───►▼
 [paso 1]      ◇ ¿condición?         │  ◇ ¿condición?
    │          sí/      \no          │  sí│      \no
    ▼          ▼         ▼           │    ▼       ▼
 [paso 2]   [bloque A] [bloque B]    └─[cuerpo]  (sale)
```

### `if` / `else`

```csharp
int calificacion = 82;

if (calificacion >= 70)
{
    Console.WriteLine("Aprobado");
}
else
{
    Console.WriteLine("Reprobado");
}
```

### `if` / `else if` / `else`

Se evalúan en orden; se ejecuta **solo el primer** bloque cuya condición sea verdadera.

```csharp
int calificacion = 82;
string nivel;

if (calificacion >= 90)
    nivel = "Excelente";
else if (calificacion >= 80)
    nivel = "Muy bien";
else if (calificacion >= 70)
    nivel = "Suficiente";
else
    nivel = "No acreditado";

Console.WriteLine(nivel);   // → Muy bien
```

> ⚠️ Si el bloque tiene una sola línea, las llaves son opcionales, pero usarlas siempre evita errores al agregar líneas después.

### `if` anidados

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

Muchas veces un `if` anidado puede simplificarse con `&&`.

### Operador ternario `? :`

Forma compacta de un `if-else` que **devuelve un valor**.

```csharp
int edad = 16;
string tipo = edad >= 18 ? "Adulto" : "Menor";
```

Úsalo solo para decisiones simples; si se anida, el código se vuelve difícil de leer.

### `switch`

Compara **una** expresión contra varios valores constantes.

```csharp
Console.Write("Día (1-7): ");
int dia = int.Parse(Console.ReadLine()!);

switch (dia)
{
    case 1:
        Console.WriteLine("Lunes");
        break;
    case 2:
        Console.WriteLine("Martes");
        break;
    case 6:
    case 7:
        Console.WriteLine("Fin de semana");   // varios case comparten bloque
        break;
    default:
        Console.WriteLine("Otro día entre semana o inválido");
        break;
}
```

> En C# cada `case` con código **debe** terminar en `break` (o `return`). No se permite "caer" al siguiente caso.

### Expresión `switch` (C# 8+)

Más compacta cuando cada caso solo produce un valor:

```csharp
string nombreDia = dia switch
{
    1 => "Lunes",
    2 => "Martes",
    3 => "Miércoles",
    4 => "Jueves",
    5 => "Viernes",
    6 or 7 => "Fin de semana",
    _ => "Inválido"           // _ equivale a default
};

string rango = calificacion switch
{
    >= 90 => "A",
    >= 80 => "B",
    >= 70 => "C",
    _ => "F"
};
```

### ¿`if` o `switch`?

| Usa `if` cuando… | Usa `switch` cuando… |
|---|---|
| Las condiciones son rangos o combinaciones (`x > 5 && y < 3`) | Comparas una sola variable contra valores concretos |
| Hay pocas opciones (1-3) | Hay muchas opciones (menús, códigos) |
| Las condiciones involucran variables distintas | Quieres un código más ordenado y legible |

---

## 2.2. Estructuras repetitivas (bucles)

Un **bucle** repite un bloque de código mientras se cumpla una condición. Cada repetición se llama **iteración**.

Todo bucle bien escrito tiene tres elementos:

1. **Inicialización** de la variable de control.
2. **Condición** que decide si se sigue repitiendo.
3. **Actualización** que acerca la variable a que la condición sea falsa.

> ⚠️ Si falta la actualización, se produce un **bucle infinito**.

### 2.2.1. Bucle `while`

Evalúa la condición **antes** de cada iteración. Si es falsa desde el inicio, el cuerpo **no se ejecuta ni una vez** (0 o más iteraciones).

```csharp
while (condicion)
{
    // cuerpo
}
```

#### Ejemplo: contar del 1 al 5

```csharp
int i = 1;              // 1. inicialización
while (i <= 5)          // 2. condición
{
    Console.WriteLine(i);
    i++;                // 3. actualización
}
```

#### Ejemplo: número de dígitos

Caso típico de `while`: **no sabemos de antemano** cuántas veces se repetirá.

```csharp
int numero = 58231;
int digitos = 0;

while (numero > 0)
{
    numero /= 10;
    digitos++;
}

Console.WriteLine(digitos);   // → 5
```

#### Ejemplo: validar entrada

```csharp
Console.Write("Ingresa una edad válida: ");
int edad;

while (!int.TryParse(Console.ReadLine(), out edad) || edad < 0)
{
    Console.Write("Dato inválido, intenta de nuevo: ");
}

Console.WriteLine($"Edad registrada: {edad}");
```

### 2.2.2. Bucle `do-while`

Evalúa la condición **después** de cada iteración, por lo que el cuerpo se ejecuta **al menos una vez** (1 o más iteraciones).

```csharp
do
{
    // cuerpo
} while (condicion);   // ← lleva punto y coma
```

#### Ejemplo: menú de opciones

El caso clásico: el menú debe mostrarse al menos una vez.

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

#### Ejemplo: adivina el número

```csharp
var rnd = new Random();
int secreto = rnd.Next(1, 101);
int intento, intentos = 0;

do
{
    Console.Write("Adivina (1-100): ");
    intento = int.Parse(Console.ReadLine()!);
    intentos++;

    if (intento < secreto) Console.WriteLine("Más alto");
    else if (intento > secreto) Console.WriteLine("Más bajo");
} while (intento != secreto);

Console.WriteLine($"¡Correcto! Lo lograste en {intentos} intentos");
```

### 2.2.3. Bucle `for`

Reúne inicialización, condición y actualización **en una sola línea**. Es ideal cuando **se conoce el número de iteraciones**.

```csharp
for (inicialización; condición; actualización)
{
    // cuerpo
}
```

Orden de ejecución: `inicialización` → `condición` → `cuerpo` → `actualización` → `condición` → `cuerpo` → … hasta que la condición sea falsa.

#### Ejemplos

```csharp
// Del 1 al 10
for (int i = 1; i <= 10; i++)
    Console.Write(i + " ");

// Del 10 al 1 (decreciente)
for (int i = 10; i >= 1; i--)
    Console.Write(i + " ");

// Solo pares, saltando de 2 en 2
for (int i = 0; i <= 20; i += 2)
    Console.Write(i + " ");
```

#### Ejemplo: tabla de multiplicar

```csharp
int n = 7;
for (int i = 1; i <= 10; i++)
{
    Console.WriteLine($"{n} x {i,2} = {n * i,3}");
}
```

#### Ejemplo: acumulador y contador

```csharp
int suma = 0;        // acumulador
int multiplos3 = 0;  // contador

for (int i = 1; i <= 100; i++)
{
    suma += i;
    if (i % 3 == 0) multiplos3++;
}

Console.WriteLine($"Suma 1..100 = {suma}");          // → 5050
Console.WriteLine($"Múltiplos de 3 = {multiplos3}"); // → 33
```

#### Ejemplo: factorial

```csharp
int n = 5;
long factorial = 1;

for (int i = 2; i <= n; i++)
    factorial *= i;

Console.WriteLine($"{n}! = {factorial}");   // → 5! = 120
```

#### Bucles anidados

Un bucle dentro de otro: el interno se ejecuta **completo** en cada iteración del externo.

```csharp
// Triángulo de asteriscos
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

> ⚠️ El alcance de `i` declarado en el `for` termina con el bucle; no puede usarse después.

---

## 2.3. Comparación y casos de uso de los bucles

| Característica | `while` | `do-while` | `for` |
|---|---|---|---|
| ¿Dónde se evalúa la condición? | Antes del cuerpo | Después del cuerpo | Antes del cuerpo |
| Mínimo de iteraciones | 0 | **1** | 0 |
| ¿Se conoce el número de repeticiones? | Normalmente no | Normalmente no | **Sí** |
| Inicialización y actualización | Fuera / dentro del cuerpo | Fuera / dentro del cuerpo | En la cabecera |
| Caso típico | Leer hasta un centinela, procesar dígitos | Menús, validar entrada | Contar, recorrer arreglos, tablas |

### El mismo problema con los tres bucles

Sumar los números del 1 al 5:

```csharp
// while
int i = 1, suma = 0;
while (i <= 5) { suma += i; i++; }

// do-while
int j = 1, suma2 = 0;
do { suma2 += j; j++; } while (j <= 5);

// for
int suma3 = 0;
for (int k = 1; k <= 5; k++) suma3 += k;
```

Los tres dan `15`, pero el `for` es el más claro porque el número de repeticiones es fijo.

### Guía rápida para elegir

```
¿Sabes cuántas veces se repetirá?
 ├── Sí ──────────────────────────────► for
 └── No
      └── ¿Debe ejecutarse al menos una vez?
           ├── Sí ─────────────────────► do-while
           └── No ─────────────────────► while
```

> Para recorrer **todos** los elementos de un arreglo o colección también existe `foreach`, que se ve en las unidades 3 y 4.

---

## 2.4. Control de flujo

`break` y `continue` permiten alterar el comportamiento normal de un bucle.

### 2.4.1. Uso de `break`

`break` **termina inmediatamente** el bucle más cercano; la ejecución continúa en la primera instrucción después del bucle.

```csharp
for (int i = 1; i <= 10; i++)
{
    if (i == 5)
        break;
    Console.Write(i + " ");
}
// → 1 2 3 4
```

#### Ejemplo: buscar el primer múltiplo

```csharp
int encontrado = -1;

for (int n = 100; n <= 200; n++)
{
    if (n % 7 == 0 && n % 11 == 0)
    {
        encontrado = n;
        break;           // ya no tiene sentido seguir buscando
    }
}

Console.WriteLine(encontrado);   // → 154
```

#### Ejemplo: bucle con salida controlada

```csharp
int suma = 0;

while (true)
{
    Console.Write("Número (0 para terminar): ");
    int n = int.Parse(Console.ReadLine()!);

    if (n == 0)
        break;

    suma += n;
}

Console.WriteLine($"Suma total: {suma}");
```

#### Ejemplo: número primo

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

Console.WriteLine($"{numero} es primo: {esPrimo}");   // → True
```

> ⚠️ En bucles anidados, `break` solo sale del bucle **interno**. Para salir de ambos, usa una variable bandera o extrae el código a un método y usa `return`.

```csharp
bool salir = false;
for (int i = 0; i < 5 && !salir; i++)
{
    for (int j = 0; j < 5; j++)
    {
        if (i * j == 6)
        {
            salir = true;
            break;
        }
    }
}
```

### 2.4.2. Uso de `continue`

`continue` **salta el resto de la iteración actual** y pasa a la siguiente (en un `for`, primero ejecuta la actualización).

```csharp
for (int i = 1; i <= 10; i++)
{
    if (i % 2 == 0)
        continue;        // salta los pares
    Console.Write(i + " ");
}
// → 1 3 5 7 9
```

#### Ejemplo: ignorar datos inválidos

```csharp
string[] datos = { "10", "abc", "25", "", "-3", "7" };
int suma = 0;

foreach (string d in datos)
{
    if (!int.TryParse(d, out int valor))
        continue;        // no es número: lo ignoramos
    if (valor < 0)
        continue;        // negativo: lo ignoramos

    suma += valor;
}

Console.WriteLine(suma);   // → 42
```

> ⚠️ Con `while`, cuidado de no saltarte la actualización con `continue`:
>
> ```csharp
> int i = 0;
> while (i < 10)
> {
>     if (i == 5) continue;   // ⚠️ i nunca pasa de 5 → bucle infinito
>     i++;
> }
> ```

### `break` vs `continue`

| | `break` | `continue` |
|---|---|---|
| ¿Qué hace? | Sale del bucle | Salta a la siguiente iteración |
| ¿El bucle sigue? | No | Sí |
| ¿Se usa en `switch`? | Sí (obligatorio en cada `case`) | No |
| Uso típico | Búsquedas, centinelas, salir al encontrar algo | Filtrar o ignorar elementos |

> Buena práctica: úsalos con moderación. Si un bucle tiene muchos `break` y `continue`, probablemente la condición del bucle puede reescribirse de forma más clara.

---

## Resumen

- `if / else if / else` sirve para condiciones generales; `switch` para comparar una variable contra valores concretos; el ternario para decisiones simples que devuelven un valor.
- `while` evalúa antes (0 o más veces), `do-while` evalúa después (1 o más veces) y `for` es ideal con un número conocido de repeticiones.
- Todo bucle necesita inicialización, condición y actualización; sin actualización se vuelve infinito.
- `break` termina el bucle; `continue` salta a la siguiente iteración.

---

## Ejercicios

> Resuélvelos en `practica/Unidad2/`.

1. **Clasificador de triángulos.** Lee tres lados e indica si es equilátero, isósceles o escaleno (y si los lados forman un triángulo válido).
2. **Calculadora con `switch`.** Pide dos números y un operador (`+ - * /`) y muestra el resultado. Controla la división entre cero.
3. **Suma de dígitos (`while`).** Calcula la suma de los dígitos de un número entero, por ejemplo `4721 → 14`.
4. **Menú de conversiones (`do-while`).** Ofrece: 1) km a millas, 2) °C a °F, 3) pesos a dólares, 0) salir.
5. **Tablas del 1 al 10 (`for` anidado).** Imprímelas en formato de cuadrícula.
6. **Serie de Fibonacci.** Muestra los primeros `n` términos.
7. **Promedio con centinela.** Lee calificaciones hasta que se ingrese `-1` (`break`) e ignora valores fuera de 0-100 (`continue`). Al final muestra el promedio, la máxima y la mínima.
8. **Primos en un rango.** Muestra todos los números primos entre 1 y 100 usando `break` para cortar la verificación.
9. **FizzBuzz.** Del 1 al 100 imprime `Fizz` para múltiplos de 3, `Buzz` para múltiplos de 5 y `FizzBuzz` para ambos.
10. **Comparativa.** Resuelve el ejercicio 3 con los tres tipos de bucle y explica en un comentario cuál es el más adecuado y por qué.

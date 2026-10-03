# POO-4B — Material del curso

Material de estudio del curso, organizado por unidades. Todos los ejemplos están escritos en **C#** (.NET) y pueden ejecutarse como aplicaciones de consola.

## Contenido

| Unidad | Tema | Archivo |
|---|---|---|
| 1 | Tipos de Datos, Operadores y Cadenas | [unidad-1-tipos-datos-operadores-cadenas.md](unidad-1-tipos-datos-operadores-cadenas.md) |
| 2 | Estructuras de Control y Bucles | [unidad-2-estructuras-control-y-bucles.md](unidad-2-estructuras-control-y-bucles.md) |
| 3 | Arreglos | [unidad-3-arreglos.md](unidad-3-arreglos.md) |
| 4 | Colecciones | [unidad-4-colecciones.md](unidad-4-colecciones.md) |

El temario completo con todos los subtemas está en [poo-4b-completo.md](poo-4b-completo.md).

## Estructura del repositorio

```
POO-4B/
├── README.md                                   ← este archivo
├── poo-4b-completo.md                          ← temario completo e índice
├── unidad-1-tipos-datos-operadores-cadenas.md
├── unidad-2-estructuras-control-y-bucles.md
├── unidad-3-arreglos.md
├── unidad-4-colecciones.md
└── practica/                                   ← aquí van tus ejercicios resueltos
```

## Cómo usar este material

1. Lee la unidad en orden: cada tema se apoya en el anterior.
2. Copia los ejemplos en un proyecto de consola y ejecútalos; modifícalos para ver qué cambia.
3. Resuelve los ejercicios del final de cada unidad dentro de la carpeta `practica/`.

### Crear un proyecto de consola para practicar

```bash
cd practica
dotnet new console -n Unidad1
cd Unidad1
dotnet run
```

> Requiere tener instalado el [SDK de .NET](https://dotnet.microsoft.com/download).

## Convenciones

- El código usa *top-level statements* (C# 9+), así que no siempre verás `class Program` ni `static void Main`.
- Los comentarios `// →` indican lo que imprime la línea en consola.
- Los bloques marcados con ⚠️ señalan errores comunes.

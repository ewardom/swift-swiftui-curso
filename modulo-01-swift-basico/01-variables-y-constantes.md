# 🔤 Variables y Constantes en Swift

## 🎯 Lo que aprenderás

- Qué es una variable y una constante
- Diferencia entre `var` y `let`
- Cómo nombrar variables correctamente (camelCase)
- Inferencia de tipos en Swift
- Cuándo usar cada uno

---

## 1. ¿Qué es una Variable?

Una **variable** es como una caja con una etiqueta donde puedes guardar información. Esa información **puede cambiar** con el tiempo.

En Swift, las variables se declaran con la palabra clave `var`:

```swift
// Sintaxis: var nombreVariable = valor
var edad = 25
var nombre = "Carlos"
var temperatura = 36.6
var estaLloviendo = false
```

Como el valor **puede cambiar**, podemos hacer esto:

```swift
var puntaje = 0
print(puntaje) // 0

puntaje = 10
print(puntaje) // 10

puntaje = puntaje + 5
print(puntaje) // 15
```

---

## 2. ¿Qué es una Constante?

Una **constante** es como una caja sellada: guardas el valor una vez y **ya no puede cambiar**.

En Swift, las constantes se declaran con `let`:

```swift
// Sintaxis: let nombreConstante = valor
let pi = 3.14159
let nombreApp = "Mi Primera App"
let añoNacimiento = 1995
```

Si intentas cambiar una constante, Xcode te dará un error:

```swift
let ciudad = "Ciudad de México"
ciudad = "Guadalajara" // ❌ ERROR: no se puede cambiar una constante
```

---

## 3. ¿Cuándo usar `var` vs `let`?

> 💡 **Regla de oro en Swift:** Usa siempre `let` por defecto. Solo cambia a `var` cuando necesites que el valor cambie.

| Usa `let` cuando... | Usa `var` cuando... |
|---|---|
| El valor no cambiará nunca | El valor puede cambiar |
| Configuración de la app | Puntuación de un juego |
| Nombre del usuario (fijo) | Edad (puede actualizarse) |
| URL de una API | Texto de un campo de búsqueda |

```swift
// ✅ Buenas prácticas
let nombreUsuario = "Ana"          // no cambia
let urlAPI = "https://api.ejemplo.com"  // no cambia

var puntos = 0                     // cambia durante el juego
var mensajeBienvenida = "Hola"     // puede cambiar
```

---

## 4. Nomenclatura: camelCase

En Swift usamos **camelCase** para nombrar variables y constantes:

- Primera palabra en minúscula
- Cada palabra siguiente empieza con mayúscula
- Sin espacios ni guiones

```swift
// ✅ Correcto — camelCase
var nombreCompleto = "Juan Pérez"
var edadDelUsuario = 30
var estaConectadoAInternet = true

// ❌ Incorrecto
var nombre_completo = "Juan Pérez"   // snake_case (no se usa en Swift)
var NombreCompleto = "Juan Pérez"    // PascalCase (se reserva para tipos)
var nombrecomp = "Juan Pérez"        // sin separación, difícil de leer
```

> 💡 **Tip:** Los nombres deben ser **descriptivos**. Prefiere `edadDelUsuario` sobre `e` o `edad1`.

---

## 5. Inferencia de Tipos

Swift es un lenguaje **fuertemente tipado**, pero es inteligente: puede **inferir** (adivinar) el tipo de dato automáticamente.

```swift
var numero = 42          // Swift infiere: Int
var precio = 19.99       // Swift infiere: Double
var saludo = "Hola"      // Swift infiere: String
var activo = true        // Swift infiere: Bool
```

También puedes declarar el tipo **explícitamente** si lo prefieres o necesitas:

```swift
var numero: Int = 42
var precio: Double = 19.99
var saludo: String = "Hola"
var activo: Bool = true
```

### ¿Cuándo declarar el tipo explícitamente?

```swift
// Cuando el valor inicial no deja claro el tipo
var calificacion: Double = 10   // sin Double, sería Int

// Cuando declaras sin valor inicial
var nombreDelGanador: String    // declaras ahora, asignas después
nombreDelGanador = "María"
```

---

## 6. Ejemplo Práctico: App de Lista de Compras

Veamos cómo se usan variables y constantes en un contexto real:

```swift
// Información fija de la app (constantes)
let nombreApp = "Mi Lista de Compras"
let versionApp = "1.0"
let maximoArticulos = 100

// Información que cambia (variables)
var numeroDeArticulos = 0
var totalCompra = 0.0
var ultimoArticuloAgregado = ""
var listaCompleta = false

// Simulando el uso de la app
numeroDeArticulos = 5
totalCompra = 250.50
ultimoArticuloAgregado = "Leche"

print("App: \(nombreApp) v\(versionApp)")
print("Artículos en lista: \(numeroDeArticulos)/\(maximoArticulos)")
print("Total: $\(totalCompra)")
print("Último agregado: \(ultimoArticuloAgregado)")
```

---

## ⚠️ Errores Comunes

```swift
// ❌ Usar var cuando debería ser let
var pi = 3.14159   // pi nunca cambia, usa let

// ❌ Nombre poco descriptivo
var x = "Juan García"   // ¿qué es x?
var nombre = "Juan García"  // ✅ mucho mejor

// ❌ Intentar usar una variable antes de asignarle valor
var resultado: Int
print(resultado)   // ❌ ERROR: variable usada antes de inicializar

// ✅ Correcto
var resultado: Int = 0
print(resultado)
```

---

## 🛠️ Ejercicio en Swift Playgrounds

Copia este código en Swift Playgrounds y complétalo:

```swift
// ============================================
// 🛠️ EJERCICIO: Variables y Constantes
// ============================================
// Instrucciones:
// 1. Completa los espacios marcados con "???"
// 2. Ejecuta el playground y verifica los resultados
// 3. Intenta agregar tu propia información al final

// --- PARTE 1: Tu información personal ---
// Usa 'let' para datos que no cambian
let miNombre: String = "???"           // Escribe tu nombre
let miCiudad = "???"                   // Escribe tu ciudad
let añoDeNacimiento: Int = ???         // Escribe tu año de nacimiento

// Usa 'var' para datos que pueden cambiar
var miEdad = 2026 - añoDeNacimiento   // Se calcula automáticamente
var miHobby = "???"                    // Escribe un hobby tuyo

// --- PARTE 2: Mostrando la información ---
print("👤 Mi perfil:")
print("Nombre: \(miNombre)")
print("Ciudad: \(miCiudad)")
print("Edad: \(miEdad) años")
print("Hobby favorito: \(miHobby)")

// --- PARTE 3: Modificando variables ---
// Las variables SÍ se pueden cambiar
miHobby = "Programar en Swift"   // Actualizamos el hobby
print("\n🎯 Nuevo hobby: \(miHobby)")

// Intenta cambiar una constante (esto dará error, es normal):
// miNombre = "Otro nombre"  // ← quita el // y observa el error

// --- PARTE 4: Tu turno ---
// Crea 3 variables/constantes propias relacionadas con una app que
// te gustaría crear. Por ejemplo, una app de música, deportes, etc.

// Escribe aquí tu código:
// let ???
// var ???
// var ???
```

---

## ✅ Resumen

| Concepto | Palabra clave | ¿Puede cambiar? |
|---|---|---|
| Variable | `var` | ✅ Sí |
| Constante | `let` | ❌ No |

- Usa `let` siempre que puedas
- Cambia a `var` solo cuando el valor necesite modificarse
- Nombra tus variables con **camelCase** descriptivo
- Swift puede **inferir el tipo** automáticamente

---

⬅️ [Módulo 00 — Preparación](../modulo-00-preparacion/README.md) | ➡️ [02 — Tipos de Datos](./02-tipos-de-datos.md)

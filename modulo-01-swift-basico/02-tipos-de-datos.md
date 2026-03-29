# 📦 Tipos de Datos en Swift

## 🎯 Lo que aprenderás

- Los tipos de datos básicos: Int, Double, Bool, String
- Colecciones: Array, Dictionary, Set
- Tuplas y Type Aliases
- Cómo convertir entre tipos

---

## 1. Tipos Básicos

### 🔢 Int — Números enteros
```swift
let edad: Int = 28
let añoActual = 2026
var puntos = 0
var temperatura = -5   // puede ser negativo
```

### 🔣 Double y Float — Números decimales
```swift
let precio: Double = 99.99
let pi = 3.14159265358979   // Swift infiere Double
let peso: Float = 72.5      // menos precisión, ocupa menos memoria

// 💡 Usa Double por defecto, Float solo si necesitas ahorrar memoria
```

### ✅ Bool — Verdadero o Falso
```swift
let esMayorDeEdad = true
var sesionIniciada = false
var tieneDescuento: Bool = true

// Útil en condiciones
if sesionIniciada {
    print("Bienvenido de nuevo")
}
```

### 📝 String — Texto
```swift
let saludo = "¡Hola, mundo!"
var nombreUsuario = "María"
let emoji = "🍎"

// String multilínea
let descripcion = """
    Esta es una app
    para aprender Swift
    desde cero.
    """

// Interpolación de strings (insertar valores dentro de texto)
let nombre = "Carlos"
let edad = 25
let mensaje = "Me llamo \(nombre) y tengo \(edad) años."
print(mensaje) // Me llamo Carlos y tengo 25 años.

// Operaciones con strings
let apellido = "García"
let nombreCompleto = nombre + " " + apellido  // concatenación
print(nombreCompleto.count)    // número de caracteres
print(nombreCompleto.uppercased())  // CARLOS GARCÍA
print(nombreCompleto.lowercased())  // carlos garcía
```

### 🔤 Character — Un solo carácter
```swift
let inicial: Character = "C"
let simbolo: Character = "★"
```

---

## 2. Colecciones

### 📋 Array — Lista ordenada de elementos
```swift
// Crear un array
var frutas = ["manzana", "naranja", "plátano"]
var numeros: [Int] = [1, 2, 3, 4, 5]
var vacio: [String] = []   // array vacío

// Acceder a elementos (índice empieza en 0)
print(frutas[0])   // manzana
print(frutas[1])   // naranja

// Modificar
frutas.append("uva")           // agregar al final
frutas.insert("pera", at: 0)   // insertar en posición
frutas.remove(at: 2)           // eliminar por posición
frutas[0] = "mango"            // reemplazar

// Información del array
print(frutas.count)       // número de elementos
print(frutas.isEmpty)     // ¿está vacío?
print(frutas.contains("uva"))  // ¿contiene "uva"?

// Recorrer un array
for fruta in frutas {
    print("Fruta: \(fruta)")
}
```

### 📖 Dictionary — Pares clave-valor
```swift
// Crear un diccionario
var contacto: [String: String] = [
    "nombre": "Ana López",
    "telefono": "555-1234",
    "email": "ana@ejemplo.com"
]

// Acceder a valores (devuelve Optional)
let nombre = contacto["nombre"]           // Optional("Ana López")
let nombreSeguro = contacto["nombre"] ?? "Sin nombre"  // "Ana López"

// Modificar
contacto["telefono"] = "555-5678"    // actualizar
contacto["ciudad"] = "Monterrey"     // agregar nueva clave

// Eliminar
contacto.removeValue(forKey: "email")

// Recorrer
for (clave, valor) in contacto {
    print("\(clave): \(valor)")
}

print(contacto.count)   // número de pares
```

### 🔵 Set — Colección sin duplicados, sin orden
```swift
// Crear un set
var colores: Set<String> = ["rojo", "azul", "verde"]
var ids: Set<Int> = [1, 2, 3, 4, 5]

// Los duplicados se ignoran automáticamente
var letras: Set<String> = ["a", "b", "a", "c", "b"]
print(letras)  // {"a", "b", "c"} — sin duplicados

// Operaciones de conjuntos
let set1: Set = [1, 2, 3, 4]
let set2: Set = [3, 4, 5, 6]

let union = set1.union(set2)              // [1,2,3,4,5,6]
let interseccion = set1.intersection(set2) // [3,4]
let diferencia = set1.subtracting(set2)   // [1,2]

// ¿Cuándo usar Set vs Array?
// - Array: cuando el orden importa o puedes tener duplicados
// - Set: cuando necesitas unicidad y búsquedas rápidas
```

---

## 3. Tuplas

Las tuplas agrupan múltiples valores en uno solo, sin necesidad de crear una estructura:

```swift
// Tupla básica
let coordenadas = (40.4168, -3.7038)   // Madrid
let persona = ("Luis", 30, "México")

// Acceder por índice
print(coordenadas.0)  // 40.4168
print(persona.1)      // 30

// Tupla con nombres (más legible)
let punto = (x: 10, y: 25)
print(punto.x)   // 10
print(punto.y)   // 25

// Muy útil para retornar múltiples valores de una función
func obtenerMinMax(numeros: [Int]) -> (min: Int, max: Int) {
    return (numeros.min()!, numeros.max()!)
}

let resultado = obtenerMinMax(numeros: [3, 1, 7, 2, 9])
print("Mínimo: \(resultado.min)")  // 1
print("Máximo: \(resultado.max)")  // 9
```

---

## 4. Type Aliases

Puedes crear un nombre alternativo para un tipo existente, haciendo el código más legible:

```swift
typealias Nombre = String
typealias Edad = Int
typealias Coordenada = (latitud: Double, longitud: Double)

let usuario: Nombre = "Sofía"
let años: Edad = 28
let ubicacion: Coordenada = (latitud: 19.4326, longitud: -99.1332)
```

---

## 5. Conversión de Tipos

Swift NO convierte tipos automáticamente. Debes hacerlo explícitamente:

```swift
let entero = 42
let decimal = 3.14

// ❌ Esto NO funciona en Swift
// let suma = entero + decimal

// ✅ Convertir explícitamente
let suma = Double(entero) + decimal   // 45.14
let soloEntero = entero + Int(decimal) // 45 (se pierde el .14)

// String ↔ Int
let textoNumero = "123"
let numero = Int(textoNumero)          // Optional(123)
let numeroSeguro = Int(textoNumero) ?? 0  // 123

let otroNumero = 456
let texto = String(otroNumero)         // "456"

// Ejemplos prácticos
let edadTexto = "25"
if let edadReal = Int(edadTexto) {
    print("Edad válida: \(edadReal)")
} else {
    print("Edad inválida")
}
```

---

## ⚠️ Errores Comunes

```swift
// ❌ Mezclar tipos sin convertir
var total: Int = 10
var descuento: Double = 2.5
// var final = total - descuento  // ERROR

// ✅ Convertir primero
var final = Double(total) - descuento   // 7.5

// ❌ Acceder a índice fuera de rango
var lista = ["a", "b", "c"]
// print(lista[5])  // ERROR en tiempo de ejecución

// ✅ Verificar primero
if lista.indices.contains(5) {
    print(lista[5])
} else {
    print("Índice fuera de rango")
}
```

---

## 🛠️ Ejercicio en Swift Playgrounds

```swift
// ============================================
// 🛠️ EJERCICIO: Tipos de Datos
// Instrucciones: completa los ??? y ejecuta
// ============================================

// --- PARTE 1: Tu perfil de app ---
let appNombre: String = "???"
let appVersion: Double = ???
let appGratuita: Bool = ???
var descargas: Int = ???

print("📱 App: \(appNombre) v\(appVersion)")
print("¿Gratuita? \(appGratuita)")
print("Descargas: \(descargas)")

// --- PARTE 2: Array de favoritos ---
var peliculasFavoritas: [String] = ["???", "???", "???"]
peliculasFavoritas.append("???")  // agrega una más

print("\n🎬 Mis películas favoritas:")
for pelicula in peliculasFavoritas {
    print("  • \(pelicula)")
}
print("Total: \(peliculasFavoritas.count) películas")

// --- PARTE 3: Diccionario de contacto ---
var miContacto: [String: String] = [
    "nombre": "???",
    "ciudad": "???",
    "hobby": "???"
]

print("\n👤 Mi contacto:")
for (clave, valor) in miContacto {
    print("  \(clave): \(valor)")
}

// --- PARTE 4: Conversión ---
let edadEnTexto = "???"   // escribe tu edad como texto
if let edadNumerica = Int(edadEnTexto) {
    let añoNacimiento = 2026 - edadNumerica
    print("\n📅 Naciste aproximadamente en \(añoNacimiento)")
} else {
    print("⚠️ Por favor escribe solo números en edadEnTexto")
}

// --- PARTE 5 (DESAFÍO): ---
// Crea un Set con los géneros musicales que te gustan
// (los repetidos deben eliminarse automáticamente)
var generosMusicales: Set<String> = ["???", "???", "???", "???"]
print("\n🎵 Géneros únicos que me gustan: \(generosMusicales.count)")
```

---

## ✅ Resumen

| Tipo | Ejemplo | Uso |
|---|---|---|
| `Int` | `42` | Números enteros |
| `Double` | `3.14` | Números decimales |
| `Bool` | `true` | Verdadero/Falso |
| `String` | `"Hola"` | Texto |
| `Array` | `[1, 2, 3]` | Lista ordenada |
| `Dictionary` | `["a": 1]` | Clave-valor |
| `Set` | `{1, 2, 3}` | Únicos sin orden |
| `Tuple` | `(x: 1, y: 2)` | Grupo de valores |

---

⬅️ [01 — Variables y Constantes](./01-variables-y-constantes.md) | ➡️ [03 — Control de Flujo](./03-control-de-flujo.md)

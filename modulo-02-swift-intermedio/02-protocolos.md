# 📋 Protocolos en Swift

## 🎯 Lo que aprenderás

- Qué es un protocolo y para qué sirve
- Protocol-Oriented Programming (POP)
- Cómo conformar protocolos
- Protocolos clave de Swift: Equatable, Hashable, Codable, Identifiable
- Usar extensions para conformar protocolos

---

## 1. ¿Qué es un Protocolo?

Un protocolo es un **contrato** que define qué propiedades y métodos debe tener un tipo, sin decir cómo implementarlos. Es como una lista de requisitos que un tipo debe cumplir.

```swift
// Definir un protocolo
protocol Saludable {
    var nombre: String { get }
    func saludar()
}

// Conformar el protocolo en una struct
struct Persona: Saludable {
    var nombre: String  // cumple el requisito de la propiedad

    func saludar() {   // cumple el requisito del método
        print("Hola, soy \(nombre)")
    }
}

// Conformar el mismo protocolo en una clase
class Robot: Saludable {
    var nombre: String

    init(nombre: String) { self.nombre = nombre }

    func saludar() {
        print("BEEP BOOP. SOY \(nombre.uppercased())")
    }
}

let persona = Persona(nombre: "Ana")
let robot = Robot(nombre: "R2D2")

persona.saludar()  // Hola, soy Ana
robot.saludar()    // BEEP BOOP. SOY R2D2

// Polimorfismo — tratar ambos como "Saludable"
let saludadores: [any Saludable] = [persona, robot]
for s in saludadores {
    s.saludar()
}
```

---

## 2. Protocolos con Propiedades y Métodos

```swift
protocol Vehiculo {
    // Propiedades requeridas
    var marca: String { get }           // solo lectura
    var velocidadMaxima: Int { get }    // solo lectura
    var velocidadActual: Int { get set } // lectura y escritura

    // Métodos requeridos
    func acelerar(a velocidad: Int)
    func frenar()
    func describir() -> String
}

struct Bicicleta: Vehiculo {
    var marca: String
    var velocidadMaxima: Int = 30
    var velocidadActual: Int = 0

    mutating func acelerar(a velocidad: Int) {
        velocidadActual = min(velocidad, velocidadMaxima)
    }

    mutating func frenar() {
        velocidadActual = 0
    }

    func describir() -> String {
        return "🚲 \(marca) — \(velocidadActual)/\(velocidadMaxima) km/h"
    }
}

var bici = Bicicleta(marca: "Trek")
bici.acelerar(a: 25)
print(bici.describir())  // 🚲 Trek — 25/30 km/h
```

---

## 3. Protocol-Oriented Programming (POP)

Swift favorece la composición de protocolos sobre la herencia:

```swift
// Protocolos pequeños y específicos
protocol Nombrable {
    var nombre: String { get }
}

protocol Describible {
    func describir() -> String
}

protocol Guardable {
    func guardar()
}

// Combinar múltiples protocolos
protocol Modelo: Nombrable, Describible, Guardable { }

// Un tipo puede conformar múltiples protocolos
struct Nota: Modelo {
    var nombre: String
    var contenido: String

    func describir() -> String {
        return "📝 \(nombre): \(contenido)"
    }

    func guardar() {
        print("💾 Guardando nota: \(nombre)")
    }
}

let nota = Nota(nombre: "Lista de compras", contenido: "Leche, pan, huevos")
print(nota.describir())
nota.guardar()
```

---

## 4. Default Implementations con Extensions

Puedes dar implementaciones por defecto a los métodos de un protocolo:

```swift
protocol Animado {
    var nombre: String { get }
    func animar()
    func detener()
    func describir() -> String  // implementación por defecto
}

extension Animado {
    // Implementación por defecto — no es obligatorio sobreescribir
    func describir() -> String {
        return "Animación: \(nombre)"
    }
}

struct Explosion: Animado {
    var nombre = "Explosión"
    func animar() { print("💥 BOOM!") }
    func detener() { print("Deteniendo explosión") }
    // No necesita implementar describir() — usa el default
}

struct Lluvia: Animado {
    var nombre = "Lluvia"
    func animar() { print("🌧️ Cayendo...") }
    func detener() { print("Dejó de llover") }
    // Sobreescribe la implementación por defecto
    func describir() -> String { "🌧️ Animación de lluvia suave" }
}

let efectos: [any Animado] = [Explosion(), Lluvia()]
for efecto in efectos {
    print(efecto.describir())
}
```

---

## 5. Protocolos Esenciales de Swift

### Equatable — Comparar con ==
```swift
struct Punto: Equatable {
    var x: Int
    var y: Int
    // Swift genera == automáticamente para structs con Equatable
}

let p1 = Punto(x: 1, y: 2)
let p2 = Punto(x: 1, y: 2)
let p3 = Punto(x: 3, y: 4)

print(p1 == p2)  // true
print(p1 == p3)  // false
print(p1 != p3)  // true
```

### Hashable — Usar en Sets y como clave de Dictionary
```swift
struct Estudiante: Hashable {
    var id: Int
    var nombre: String
}

let est1 = Estudiante(id: 1, nombre: "Ana")
let est2 = Estudiante(id: 2, nombre: "Luis")

// Usar en Set (requiere Hashable)
var grupo: Set<Estudiante> = [est1, est2]
print(grupo.count)  // 2

// Usar como clave de Dictionary
var calificaciones: [Estudiante: Double] = [est1: 9.5, est2: 8.0]
print(calificaciones[est1] ?? 0)  // 9.5
```

### Codable — Convertir a/desde JSON
```swift
struct Producto: Codable {
    var id: Int
    var nombre: String
    var precio: Double
}

// Codificar (Swift → JSON)
let producto = Producto(id: 1, nombre: "MacBook", precio: 1299.99)
let encoder = JSONEncoder()
encoder.outputFormatting = .prettyPrinted

if let jsonData = try? encoder.encode(producto),
   let jsonString = String(data: jsonData, encoding: .utf8) {
    print(jsonString)
}

// Decodificar (JSON → Swift)
let json = """
{"id": 2, "nombre": "iPhone", "precio": 999.99}
""".data(using: .utf8)!

let decoder = JSONDecoder()
if let iphone = try? decoder.decode(Producto.self, from: json) {
    print("Producto: \(iphone.nombre) — $\(iphone.precio)")
}
```

### Identifiable — Requerido por SwiftUI para listas
```swift
struct Tarea: Identifiable {
    let id = UUID()  // identificador único automático
    var titulo: String
    var completada: Bool = false
}

// SwiftUI puede usar esto directamente en List y ForEach
let tareas = [
    Tarea(titulo: "Aprender Swift"),
    Tarea(titulo: "Crear primera app"),
    Tarea(titulo: "Publicar en App Store")
]
```

---

## 6. Extensions para Conformar Protocolos

```swift
struct Libro {
    var titulo: String
    var autor: String
    var paginas: Int
    var precio: Double
}

// Separar la conformidad en extensions — más organizado
extension Libro: Equatable {
    static func == (lhs: Libro, rhs: Libro) -> Bool {
        return lhs.titulo == rhs.titulo && lhs.autor == rhs.autor
    }
}

extension Libro: CustomStringConvertible {
    var description: String {
        "📚 '\(titulo)' por \(autor) (\(paginas) págs.) — $\(precio)"
    }
}

extension Libro: Comparable {
    static func < (lhs: Libro, rhs: Libro) -> Bool {
        lhs.precio < rhs.precio
    }
}

var libros = [
    Libro(titulo: "Swift en Profundidad", autor: "Juan", paginas: 400, precio: 350.0),
    Libro(titulo: "SwiftUI para Todos", autor: "María", paginas: 300, precio: 250.0),
    Libro(titulo: "iOS Avanzado", autor: "Pedro", paginas: 500, precio: 450.0)
]

// Gracias a Comparable, podemos ordenar
libros.sort()
for libro in libros {
    print(libro)  // usa CustomStringConvertible
}
```

---

## ⚠️ Errores Comunes

```swift
// ❌ Olvidar implementar todos los requisitos del protocolo
protocol Reproducible {
    func play()
    func pause()
    func stop()
}

// struct Video: Reproducible {  // ❌ ERROR — falta implementar pause() y stop()
//     func play() { print("▶️") }
// }

// ✅ Implementar todos los requisitos
struct Video: Reproducible {
    func play()  { print("▶️ Reproduciendo") }
    func pause() { print("⏸️ Pausado") }
    func stop()  { print("⏹️ Detenido") }
}
```

---

## 🛠️ Ejercicio en Swift Playgrounds

```swift
// ============================================
// 🛠️ EJERCICIO: Protocolos
// ============================================

// --- PARTE 1: Sistema de notificaciones ---
protocol Notificable {
    var titulo: String { get }
    var mensaje: String { get }
    func enviar()
}

// Implementa tres tipos de notificación:

struct NotificacionEmail: Notificable {
    var titulo: String
    var mensaje: String
    var destinatario: String

    func enviar() {
        print("📧 Email a \(destinatario)")
        print("   Asunto: \(titulo)")
        print("   Mensaje: \(mensaje)")
    }
}

struct NotificacionSMS: Notificable {
    var titulo: String
    var mensaje: String
    var telefono: String

    func enviar() {
        // Implementa aquí
        ???
    }
}

struct NotificacionPush: Notificable {
    var titulo: String
    var mensaje: String

    func enviar() {
        // Implementa aquí
        ???
    }
}

// Crea una notificación de cada tipo y envíalas
let notificaciones: [any Notificable] = [
    NotificacionEmail(titulo: "Bienvenido", mensaje: "Gracias por registrarte", destinatario: "usuario@email.com"),
    NotificacionSMS(titulo: "Código", mensaje: "Tu código es 1234", telefono: "+52 555 1234"),
    NotificacionPush(titulo: "Nueva actualización", mensaje: "Versión 2.0 disponible")
]

print("📬 Enviando notificaciones:")
for notif in notificaciones {
    notif.enviar()
    print("---")
}

// --- PARTE 2: Protocolo Calculable ---
protocol Calculable {
    var valor: Double { get }
    func duplicar() -> Double
    func porcentaje(_ pct: Double) -> Double
}

extension Calculable {
    func duplicar() -> Double { valor * 2 }
    func porcentaje(_ pct: Double) -> Double { valor * pct / 100 }
}

// Crea un struct Precio que conforme Calculable
struct Precio: Calculable {
    var valor: Double
    // duplicar() y porcentaje() vienen del extension — ¡no necesitas implementarlos!
}

let precio = Precio(valor: 500.0)
print("\n💰 Precio: $\(precio.valor)")
print("Duplicado: $\(precio.duplicar())")
print("10%: $\(precio.porcentaje(10))")
```

---

## ✅ Resumen

| Protocolo | Uso |
|---|---|
| `Equatable` | Comparar con `==` |
| `Hashable` | Usar en `Set` y como clave de `Dictionary` |
| `Codable` | Serializar/deserializar (JSON) |
| `Identifiable` | Identificar elementos únicos (SwiftUI) |
| `Comparable` | Ordenar con `<`, `>` |
| `CustomStringConvertible` | Personalizar `print()` |

---

⬅️ [01 — Estructuras y Clases](./01-estructuras-y-clases.md) | ➡️ [03 — Closures](./03-closures.md)

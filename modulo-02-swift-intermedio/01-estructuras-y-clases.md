# 🏗️ Estructuras y Clases en Swift

## 🎯 Lo que aprenderás

- Qué son `struct` y `class` y sus diferencias
- Value types vs Reference types
- Propiedades almacenadas y computadas
- Métodos e inicializadores
- Cuándo usar cada uno en Swift moderno

---

## 1. ¿Qué es una Estructura (struct)?

Una `struct` agrupa datos relacionados y comportamientos en un solo tipo. Es un **value type**: cuando la copias, se crea una copia independiente.

```swift
struct Persona {
    var nombre: String
    var edad: Int

    func saludar() {
        print("Hola, soy \(nombre) y tengo \(edad) años.")
    }
}

// Crear una instancia
var persona1 = Persona(nombre: "Ana", edad: 28)
persona1.saludar()   // Hola, soy Ana y tengo 28 años.

// Copiar — son INDEPENDIENTES
var persona2 = persona1
persona2.nombre = "Luis"

print(persona1.nombre)  // Ana  — no cambió
print(persona2.nombre)  // Luis — solo cambió la copia
```

---

## 2. ¿Qué es una Clase (class)?

Una `class` es similar a una `struct` pero es un **reference type**: cuando la "copias", ambas variables apuntan al mismo objeto.

```swift
class Vehiculo {
    var marca: String
    var velocidad: Int

    init(marca: String, velocidad: Int) {
        self.marca = marca
        self.velocidad = velocidad
    }

    func describir() {
        print("🚗 \(marca) a \(velocidad) km/h")
    }
}

var coche1 = Vehiculo(marca: "Toyota", velocidad: 0)
var coche2 = coche1   // ambos apuntan al MISMO objeto

coche2.velocidad = 120
print(coche1.velocidad)  // 120 — ¡cambió porque es el mismo objeto!
print(coche2.velocidad)  // 120
```

---

## 3. Value Type vs Reference Type

```
┌─────────────────────────────────────────┐
│              STRUCT (Value Type)         │
│                                         │
│  persona1 ──→ [nombre: "Ana", edad: 28] │
│  persona2 ──→ [nombre: "Luis", edad: 28]│  ← copia independiente
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│              CLASS (Reference Type)     │
│                                         │
│  coche1 ──┐                             │
│           ├──→ [marca: "Toyota", vel: 120] │
│  coche2 ──┘                             │  ← mismo objeto
└─────────────────────────────────────────┘
```

---

## 4. Propiedades

### Propiedades Almacenadas
```swift
struct Producto {
    // Almacenadas — guardan un valor directamente
    var nombre: String
    var precio: Double
    let id: Int             // constante — no cambia
    var descuento: Double = 0.0  // valor por defecto
}
```

### Propiedades Computadas
```swift
struct Producto {
    var nombre: String
    var precio: Double
    var descuento: Double = 0.0

    // Computada — se calcula a partir de otras propiedades
    var precioFinal: Double {
        return precio - (precio * descuento / 100)
    }

    var descripcion: String {
        "📦 \(nombre): $\(precioFinal)"
    }
}

var producto = Producto(nombre: "iPhone 17", precio: 999.99, descuento: 10)
print(producto.precioFinal)   // 899.991
print(producto.descripcion)   // 📦 iPhone 17: $899.991
```

### Property Observers (willSet y didSet)
```swift
struct Termostato {
    var temperatura: Double {
        willSet {
            print("Cambiando temperatura a \(newValue)°C")
        }
        didSet {
            if temperatura > 30 {
                print("⚠️ Temperatura muy alta!")
            }
            print("Temperatura anterior: \(oldValue)°C")
        }
    }
}

var termo = Termostato(temperatura: 20)
termo.temperatura = 35
// Cambiando temperatura a 35.0°C
// ⚠️ Temperatura muy alta!
// Temperatura anterior: 20.0°C
```

---

## 5. Métodos

```swift
struct CuentaBancaria {
    var titular: String
    var saldo: Double

    // Método normal (solo lectura)
    func mostrarSaldo() {
        print("💰 \(titular): $\(saldo)")
    }

    // Método mutating — modifica propiedades del struct
    mutating func depositar(_ cantidad: Double) {
        guard cantidad > 0 else {
            print("⚠️ La cantidad debe ser positiva")
            return
        }
        saldo += cantidad
        print("✅ Depósito de $\(cantidad). Nuevo saldo: $\(saldo)")
    }

    mutating func retirar(_ cantidad: Double) -> Bool {
        guard cantidad <= saldo else {
            print("❌ Saldo insuficiente")
            return false
        }
        saldo -= cantidad
        print("✅ Retiro de $\(cantidad). Nuevo saldo: $\(saldo)")
        return true
    }
}

var cuenta = CuentaBancaria(titular: "María", saldo: 1000)
cuenta.mostrarSaldo()         // 💰 María: $1000.0
cuenta.depositar(500)         // ✅ Depósito de $500. Nuevo saldo: $1500.0
cuenta.retirar(200)           // ✅ Retiro de $200. Nuevo saldo: $1300.0
cuenta.retirar(2000)          // ❌ Saldo insuficiente
```

> 💡 **Tip:** En `struct`, los métodos que modifican propiedades deben marcarse con `mutating`. En `class` no es necesario.

---

## 6. Inicializadores

```swift
struct Punto {
    var x: Double
    var y: Double

    // Inicializador por defecto (Swift lo genera automáticamente para structs)
    // init(x: Double, y: Double) — ya existe sin escribirlo

    // Inicializador personalizado
    init(enOrigen: Bool = false) {
        if enOrigen {
            x = 0
            y = 0
        } else {
            x = 0
            y = 0
        }
    }

    var descripcion: String { "(\(x), \(y))" }
}

// Para clases SIEMPRE debes escribir init si tienes propiedades sin valor
class Animal {
    var nombre: String
    var tipo: String
    var edad: Int

    init(nombre: String, tipo: String, edad: Int) {
        self.nombre = nombre  // self diferencia la propiedad del parámetro
        self.tipo = tipo
        self.edad = edad
    }

    // Inicializador de conveniencia
    convenience init(nombre: String) {
        self.init(nombre: nombre, tipo: "Desconocido", edad: 0)
    }
}

let gato = Animal(nombre: "Michi")
print(gato.tipo)   // Desconocido
```

---

## 7. Herencia (solo en Class)

```swift
class Figura {
    var color: String

    init(color: String) {
        self.color = color
    }

    func area() -> Double {
        return 0
    }

    func describir() {
        print("Figura de color \(color), área: \(area())")
    }
}

class Circulo: Figura {
    var radio: Double

    init(color: String, radio: Double) {
        self.radio = radio
        super.init(color: color)  // llamar al init del padre
    }

    override func area() -> Double {
        return Double.pi * radio * radio
    }
}

class Rectangulo: Figura {
    var ancho: Double
    var alto: Double

    init(color: String, ancho: Double, alto: Double) {
        self.ancho = ancho
        self.alto = alto
        super.init(color: color)
    }

    override func area() -> Double {
        return ancho * alto
    }
}

let circulo = Circulo(color: "rojo", radio: 5)
circulo.describir()   // Figura de color rojo, área: 78.53...

let rect = Rectangulo(color: "azul", ancho: 4, alto: 6)
rect.describir()      // Figura de color azul, área: 24.0
```

---

## 8. ¿Cuándo usar struct vs class?

| Usa `struct` cuando... | Usa `class` cuando... |
|---|---|
| Modelas datos simples | Necesitas herencia |
| Quieres copias independientes | Necesitas identidad compartida |
| La mayoría de los casos en SwiftUI | Trabajas con UIKit/AppKit |
| Modelos de datos (Usuario, Producto) | Managers, Services, ViewModels |

> ✅ **Regla de Apple:** Empieza con `struct`. Solo usa `class` cuando necesites herencia o semántica de referencia.

---

## 🛠️ Ejercicio en Swift Playgrounds

```swift
// ============================================
// 🛠️ EJERCICIO: Estructuras y Clases
// ============================================

// --- PARTE 1: Crea un modelo de Tarea (struct) ---
struct Tarea {
    let id: Int
    var titulo: String
    var completada: Bool = false
    var prioridad: String  // "alta", "media", "baja"

    // Propiedad computada: emoji según prioridad
    var emoji: String {
        switch prioridad {
        case "alta": return "🔴"
        case "media": return "🟡"
        default: return "🟢"
        }
    }

    var descripcion: String {
        let estado = completada ? "✅" : "⬜"
        return "\(estado) \(emoji) \(titulo)"
    }

    mutating func completar() {
        completada = true
    }
}

// Crea al menos 3 tareas
var tareas: [Tarea] = [
    Tarea(id: 1, titulo: "???", prioridad: "alta"),
    Tarea(id: 2, titulo: "???", prioridad: "media"),
    Tarea(id: 3, titulo: "???", prioridad: "baja")
]

// Muestra todas las tareas
print("📋 Lista de tareas:")
for tarea in tareas {
    print("  \(tarea.descripcion)")
}

// Completa la primera tarea
tareas[0].completar()

print("\n📋 Lista actualizada:")
for tarea in tareas {
    print("  \(tarea.descripcion)")
}

// --- PARTE 2: Clase Contador (class) ---
class Contador {
    var valor: Int = 0
    let nombre: String

    init(nombre: String) {
        self.nombre = nombre
    }

    func incrementar(en cantidad: Int = 1) {
        valor += cantidad
    }

    func reiniciar() {
        valor = 0
    }

    func mostrar() {
        print("🔢 \(nombre): \(valor)")
    }
}

let contadorJuego = Contador(nombre: "Puntuación")
contadorJuego.incrementar()        // +1
contadorJuego.incrementar(en: 10)  // +10
contadorJuego.incrementar(en: 5)   // +5
contadorJuego.mostrar()            // 🔢 Puntuación: 16

// Demuestra referencia compartida
let mismoContador = contadorJuego
mismoContador.incrementar(en: 100)
contadorJuego.mostrar()   // ¿Qué valor muestra? ¿Por qué?
```

---

## ✅ Resumen

| Característica | `struct` | `class` |
|---|---|---|
| Tipo | Value | Reference |
| Herencia | ❌ | ✅ |
| `mutating` en métodos | Necesario | No necesario |
| `init` automático | ✅ | ❌ |
| Uso recomendado en SwiftUI | ✅ Modelos | ✅ ViewModels/Services |

---

⬅️ [Módulo 01 — Opcionales](../modulo-01-swift-basico/05-opcionales.md) | ➡️ [02 — Protocolos](./02-protocolos.md)

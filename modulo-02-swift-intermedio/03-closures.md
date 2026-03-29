# 🔒 Closures en Swift

## 🎯 Lo que aprenderás

- Qué es un closure y cómo funciona
- Sintaxis completa y abreviada
- Trailing closures
- Capture list y memoria
- map, filter, reduce, sorted

---

## 1. ¿Qué es un Closure?

Un closure es una **función sin nombre** que puedes guardar en una variable, pasar como parámetro o retornar desde otra función. Son bloques de código autocontenidos.

```swift
// Una función normal
func saludar(nombre: String) -> String {
    return "Hola, \(nombre)!"
}

// El mismo código como closure
let saludarClosure = { (nombre: String) -> String in
    return "Hola, \(nombre)!"
}

// Ambas se usan igual
print(saludar(nombre: "Ana"))          // Hola, Ana!
print(saludarClosure("Ana"))           // Hola, Ana!
```

---

## 2. Sintaxis Completa → Abreviada

Swift permite simplificar los closures progresivamente:

```swift
let numeros = [5, 2, 8, 1, 9, 3]

// Sintaxis completa
let ordenado1 = numeros.sorted(by: { (a: Int, b: Int) -> Bool in
    return a < b
})

// Inferencia de tipos (Swift los deduce)
let ordenado2 = numeros.sorted(by: { a, b in
    return a < b
})

// Return implícito (una sola expresión)
let ordenado3 = numeros.sorted(by: { a, b in a < b })

// Shorthand argument names ($0, $1, ...)
let ordenado4 = numeros.sorted(by: { $0 < $1 })

// Operator function — la más compacta
let ordenado5 = numeros.sorted(by: <)

print(ordenado5)  // [1, 2, 3, 5, 8, 9]
```

---

## 3. Trailing Closure

Cuando el último parámetro de una función es un closure, puedes escribirlo fuera de los paréntesis:

```swift
// Sin trailing closure
let resultado1 = numeros.sorted(by: { $0 < $1 })

// Con trailing closure
let resultado2 = numeros.sorted { $0 < $1 }

// Muy útil cuando el closure es largo
let procesado = numeros.map { numero in
    let cuadrado = numero * numero
    let texto = "El cuadrado de \(numero) es \(cuadrado)"
    return texto
}

for texto in procesado {
    print(texto)
}
```

---

## 4. Closures en Variables

```swift
// Guardar closures en variables
var operacion: (Int, Int) -> Int

operacion = { $0 + $1 }
print(operacion(3, 4))   // 7

operacion = { $0 * $1 }
print(operacion(3, 4))   // 12

// Closures opcionales
var alCompletarse: (() -> Void)?
alCompletarse = { print("✅ ¡Completado!") }
alCompletarse?()   // se llama solo si no es nil

// Array de closures
let transformaciones: [(Int) -> Int] = [
    { $0 * 2 },
    { $0 + 10 },
    { $0 * $0 }
]

var valor = 5
for transform in transformaciones {
    valor = transform(valor)
}
print(valor)   // ((5 * 2) + 10)^2 = 400
```

---

## 5. Captura de Variables

Los closures "capturan" las variables de su entorno:

```swift
func crearContador() -> () -> Int {
    var contador = 0

    let incrementar = {
        contador += 1   // captura 'contador'
        return contador
    }

    return incrementar
}

let contador1 = crearContador()
let contador2 = crearContador()

print(contador1())  // 1
print(contador1())  // 2
print(contador1())  // 3
print(contador2())  // 1 — contador independiente
```

---

## 6. Capture List y [weak self]

En clases, debes cuidar las referencias circulares:

```swift
class DescargaManager {
    var progreso = 0

    // ⚠️ Sin capture list — referencia fuerte (posible retain cycle)
    var alActualizar: (() -> Void)?

    func iniciarDescarga() {
        // ✅ Con [weak self] — referencia débil, evita retain cycles
        alActualizar = { [weak self] in
            guard let self else { return }
            self.progreso += 10
            print("Progreso: \(self.progreso)%")
        }
    }

    deinit {
        print("DescargaManager liberado de memoria")
    }
}

var manager: DescargaManager? = DescargaManager()
manager?.iniciarDescarga()
manager?.alActualizar?()   // Progreso: 10%
manager?.alActualizar?()   // Progreso: 20%
manager = nil              // DescargaManager liberado de memoria
```

> 💡 **Regla:** Usa `[weak self]` en closures dentro de clases cuando el closure puede vivir más que el objeto. En SwiftUI con structs, no es necesario.

---

## 7. Funciones de Alto Orden

### map — Transformar cada elemento
```swift
let numeros = [1, 2, 3, 4, 5]

// Duplicar cada número
let duplicados = numeros.map { $0 * 2 }
print(duplicados)   // [2, 4, 6, 8, 10]

// Convertir a String
let textos = numeros.map { "Número \($0)" }
print(textos)   // ["Número 1", "Número 2", ...]

let nombres = ["ana", "luis", "maría"]
let mayusculas = nombres.map { $0.capitalized }
print(mayusculas)   // ["Ana", "Luis", "María"]
```

### filter — Filtrar elementos
```swift
let calificaciones = [6, 9, 4, 8, 5, 10, 7, 3]

let aprobados = calificaciones.filter { $0 >= 6 }
print(aprobados)   // [6, 9, 8, 10, 7]

let reprobados = calificaciones.filter { $0 < 6 }
print(reprobados)  // [4, 5, 3]

let palabras = ["swift", "swiftui", "xcode", "apple", "ios"]
let conSwift = palabras.filter { $0.contains("swift") }
print(conSwift)   // ["swift", "swiftui"]
```

### reduce — Combinar en un solo valor
```swift
let precios = [100.0, 250.0, 75.0, 320.0]

// Sumar todos los precios
let total = precios.reduce(0, +)
print("Total: $\(total)")   // $745.0

// Con closure explícito
let totalVerboso = precios.reduce(0) { acumulado, precio in
    acumulado + precio
}

// Construir un string
let palabras2 = ["Swift", "es", "increíble"]
let frase = palabras2.reduce("") { $0.isEmpty ? $1 : "\($0) \($1)" }
print(frase)   // Swift es increíble
```

### sorted — Ordenar
```swift
struct Estudiante {
    var nombre: String
    var promedio: Double
}

let estudiantes = [
    Estudiante(nombre: "Carlos", promedio: 8.5),
    Estudiante(nombre: "Ana", promedio: 9.2),
    Estudiante(nombre: "Luis", promedio: 7.8)
]

// Ordenar por promedio (mayor a menor)
let porPromedio = estudiantes.sorted { $0.promedio > $1.promedio }
for e in porPromedio {
    print("\(e.nombre): \(e.promedio)")
}
// Ana: 9.2
// Carlos: 8.5
// Luis: 7.8
```

### Encadenar operaciones
```swift
let datos = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// Filtrar pares, duplicar y sumar
let resultado = datos
    .filter { $0 % 2 == 0 }   // [2, 4, 6, 8, 10]
    .map { $0 * 2 }            // [4, 8, 12, 16, 20]
    .reduce(0, +)              // 60

print("Resultado: \(resultado)")   // 60
```

---

## ⚠️ Errores Comunes

```swift
// ❌ Retain cycle — el closure retiene fuertemente self
class Vista {
    var closure: (() -> Void)?

    func configurar() {
        // ❌ Esto crea un retain cycle
        closure = {
            print(self)  // referencia fuerte a self
        }
    }
}

// ✅ Usar [weak self]
class VistaCorregida {
    var closure: (() -> Void)?

    func configurar() {
        closure = { [weak self] in
            guard let self else { return }
            print(self)
        }
    }
}
```

---

## 🛠️ Ejercicio en Swift Playgrounds

```swift
// ============================================
// 🛠️ EJERCICIO: Closures y Funciones de Alto Orden
// ============================================

struct Producto {
    var nombre: String
    var precio: Double
    var categoria: String
    var enStock: Bool
}

let inventario = [
    Producto(nombre: "iPhone 17", precio: 999.99, categoria: "iPhone", enStock: true),
    Producto(nombre: "MacBook Air", precio: 1299.99, categoria: "Mac", enStock: true),
    Producto(nombre: "AirPods Pro", precio: 249.99, categoria: "Audio", enStock: false),
    Producto(nombre: "iPad Pro", precio: 799.99, categoria: "iPad", enStock: true),
    Producto(nombre: "Apple Watch", precio: 399.99, categoria: "Watch", enStock: false),
    Producto(nombre: "Mac Mini", precio: 599.99, categoria: "Mac", enStock: true)
]

// --- PARTE 1: filter ---
// Filtra solo los productos en stock
let enStock = inventario.filter { ??? }
print("📦 Productos en stock: \(enStock.count)")

// Filtra productos de la categoría "Mac"
let productosMac = inventario.filter { ??? }
print("💻 Productos Mac: \(productosMac.count)")

// --- PARTE 2: map ---
// Obtén solo los nombres de todos los productos
let nombres = inventario.map { ??? }
print("\n🏷️ Nombres:", nombres)

// Crea strings con formato "Nombre - $Precio"
let etiquetas = inventario.map { ??? }
for etiqueta in etiquetas {
    print("  \(etiqueta)")
}

// --- PARTE 3: reduce ---
// Suma el precio total de todos los productos en stock
let totalEnStock = inventario
    .filter { $0.enStock }
    .map { $0.precio }
    .reduce(???, +)
print("\n💰 Valor total en stock: $\(totalEnStock)")

// --- PARTE 4: sorted ---
// Ordena los productos por precio (menor a mayor)
let porPrecio = inventario.sorted { ??? }
print("\n📊 Por precio (menor a mayor):")
for p in porPrecio {
    print("  \(p.nombre): $\(p.precio)")
}

// --- PARTE 5 (DESAFÍO): Encadenar todo ---
// En una sola cadena: filtra los que están en stock,
// ordénalos por precio mayor a menor,
// y obtén un array con "nombre: $precio"
let resumen = inventario
    .filter { ??? }
    .sorted { ??? }
    .map { ??? }

print("\n🏆 Top productos en stock:")
for item in resumen {
    print("  \(item)")
}
```

---

## ✅ Resumen

| Función | Qué hace | Retorna |
|---|---|---|
| `map` | Transforma cada elemento | Array del mismo tamaño |
| `filter` | Filtra elementos | Array más pequeño o igual |
| `reduce` | Combina en un valor | Un solo valor |
| `sorted` | Ordena | Array del mismo tamaño |
| `forEach` | Itera sin retornar | Void |
| `compactMap` | map + elimina nils | Array sin opcionales |
| `flatMap` | map + aplana arrays anidados | Array plano |

---

⬅️ [02 — Protocolos](./02-protocolos.md) | ➡️ [04 — Manejo de Errores](./04-manejo-de-errores.md)

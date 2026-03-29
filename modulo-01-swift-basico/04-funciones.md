# ⚡ Funciones en Swift

## 🎯 Lo que aprenderás

- Crear y llamar funciones
- Parámetros, etiquetas y valores de retorno
- Parámetros con valores por defecto
- Funciones variádicas
- Funciones como parámetros (introducción a closures)

---

## 1. ¿Qué es una Función?

Una función es un bloque de código reutilizable que realiza una tarea específica. En lugar de repetir código, lo encapsulas en una función y la llamas cuando la necesites.

```swift
// Sin funciones — código repetido ❌
print("Buenos días, Carlos")
print("Buenos días, Ana")
print("Buenos días, Luis")

// Con funciones — código reutilizable ✅
func saludar(nombre: String) {
    print("Buenos días, \(nombre)")
}

saludar(nombre: "Carlos")
saludar(nombre: "Ana")
saludar(nombre: "Luis")
```

---

## 2. Sintaxis Básica

```swift
// Función sin parámetros ni retorno
func mostrarBienvenida() {
    print("🍎 Bienvenido a la app")
}
mostrarBienvenida()

// Función con parámetros
func sumar(a: Int, b: Int) {
    print("\(a) + \(b) = \(a + b)")
}
sumar(a: 5, b: 3)   // 5 + 3 = 8

// Función con valor de retorno
func multiplicar(a: Int, b: Int) -> Int {
    return a * b
}
let resultado = multiplicar(a: 4, b: 6)
print(resultado)   // 24
```

---

## 3. Etiquetas de Parámetros

Swift usa etiquetas para que las llamadas a funciones se lean como lenguaje natural:

```swift
// Etiqueta externa vs nombre interno
// func nombre(etiquetaExterna nombreInterno: Tipo)

func mover(desde origen: String, hasta destino: String) {
    print("Moviendo de \(origen) a \(destino)")
}
// Se llama así — muy legible:
mover(desde: "Casa", hasta: "Oficina")

// Omitir etiqueta externa con _
func elevarAlCuadrado(_ numero: Int) -> Int {
    return numero * numero
}
// Se llama sin etiqueta:
let cuadrado = elevarAlCuadrado(5)   // 25

// Combinando ambos
func repetir(_ texto: String, veces cantidad: Int) {
    for _ in 1...cantidad {
        print(texto)
    }
}
repetir("¡Hola!", veces: 3)
```

---

## 4. Valores de Retorno

```swift
// Retornar un valor simple
func calcularIVA(precio: Double) -> Double {
    return precio * 0.16
}

// Retornar múltiples valores con tupla
func calcularPrecioFinal(precio: Double) -> (subtotal: Double, iva: Double, total: Double) {
    let iva = precio * 0.16
    let total = precio + iva
    return (subtotal: precio, iva: iva, total: total)
}

let compra = calcularPrecioFinal(precio: 100.0)
print("Subtotal: $\(compra.subtotal)")  // $100.0
print("IVA: $\(compra.iva)")            // $16.0
print("Total: $\(compra.total)")        // $116.0

// Retorno implícito (cuando es una sola expresión)
func cubo(de numero: Int) -> Int {
    numero * numero * numero   // no necesita 'return'
}
print(cubo(de: 3))   // 27
```

---

## 5. Parámetros con Valores por Defecto

```swift
func crearPerfil(nombre: String, edad: Int = 18, pais: String = "México") -> String {
    return "👤 \(nombre), \(edad) años, de \(pais)"
}

// Puedes llamarla de varias formas:
print(crearPerfil(nombre: "Sofía"))
// 👤 Sofía, 18 años, de México

print(crearPerfil(nombre: "Pedro", edad: 25))
// 👤 Pedro, 25 años, de México

print(crearPerfil(nombre: "María", edad: 30, pais: "España"))
// 👤 María, 30 años, de España
```

---

## 6. Funciones Variádicas

Aceptan un número variable de parámetros del mismo tipo:

```swift
func sumarTodos(_ numeros: Int...) -> Int {
    var total = 0
    for numero in numeros {
        total += numero
    }
    return total
}

print(sumarTodos(1, 2, 3))            // 6
print(sumarTodos(10, 20, 30, 40))     // 100
print(sumarTodos(5))                   // 5

// Ejemplo práctico: calcular promedio
func promedio(_ calificaciones: Double...) -> Double {
    guard !calificaciones.isEmpty else { return 0 }
    let suma = calificaciones.reduce(0, +)
    return suma / Double(calificaciones.count)
}

let prom = promedio(8.5, 9.0, 7.5, 10.0, 8.0)
print("Promedio: \(prom)")   // 8.6
```

---

## 7. Funciones como Parámetros

En Swift, las funciones son "ciudadanos de primera clase": puedes pasarlas como argumentos:

```swift
// Una función que recibe otra función como parámetro
func aplicarOperacion(_ a: Int, _ b: Int, operacion: (Int, Int) -> Int) -> Int {
    return operacion(a, b)
}

// Funciones que pasaremos como argumento
func sumar(_ a: Int, _ b: Int) -> Int { a + b }
func restar(_ a: Int, _ b: Int) -> Int { a - b }
func multiplicar(_ a: Int, _ b: Int) -> Int { a * b }

print(aplicarOperacion(10, 5, operacion: sumar))        // 15
print(aplicarOperacion(10, 5, operacion: restar))       // 5
print(aplicarOperacion(10, 5, operacion: multiplicar))  // 50

// Con closure (función anónima) — veremos más en el módulo 2
print(aplicarOperacion(10, 5, operacion: { $0 / $1 }))  // 2
```

---

## 8. Ejemplo Completo: App de Calculadora

```swift
// Una mini calculadora usando funciones
func sumar(_ a: Double, _ b: Double) -> Double { a + b }
func restar(_ a: Double, _ b: Double) -> Double { a - b }
func multiplicar(_ a: Double, _ b: Double) -> Double { a * b }
func dividir(_ a: Double, entre b: Double) -> Double? {
    guard b != 0 else {
        print("⚠️ No se puede dividir entre cero")
        return nil
    }
    return a / b
}

func calcular(_ a: Double, operador op: String, _ b: Double) -> String {
    var resultado: Double?

    switch op {
    case "+": resultado = sumar(a, b)
    case "-": resultado = restar(a, b)
    case "*": resultado = multiplicar(a, b)
    case "/": resultado = dividir(a, entre: b)
    default:  return "❓ Operador desconocido"
    }

    if let res = resultado {
        return "\(a) \(op) \(b) = \(res)"
    } else {
        return "❌ Error en el cálculo"
    }
}

print(calcular(10, operador: "+", 5))   // 10.0 + 5.0 = 15.0
print(calcular(10, operador: "/", 0))   // ⚠️ No se puede dividir entre cero
print(calcular(8, operador: "*", 7))    // 8.0 * 7.0 = 56.0
```

---

## ⚠️ Errores Comunes

```swift
// ❌ Olvidar el tipo de retorno
// func obtenerNombre() {     // si no retorna nada, no hay ->
//     return "Carlos"        // ERROR
// }

// ✅ Declarar el tipo de retorno
func obtenerNombre() -> String {
    return "Carlos"
}

// ❌ No usar la etiqueta al llamar la función
func mover(desde origen: String, hasta destino: String) { }
// mover("Casa", "Oficina")   // ERROR

// ✅ Usar las etiquetas correctas
mover(desde: "Casa", hasta: "Oficina")
```

---

## 🛠️ Ejercicio en Swift Playgrounds

```swift
// ============================================
// 🛠️ EJERCICIO: Funciones
// Instrucciones: implementa las funciones y pruébalas
// ============================================

// --- PARTE 1: Conversor de temperaturas ---
// Implementa estas funciones de conversión

func celsiusAFahrenheit(_ celsius: Double) -> Double {
    // Fórmula: (celsius × 9/5) + 32
    return ???
}

func fahrenheitACelsius(_ fahrenheit: Double) -> Double {
    // Fórmula: (fahrenheit - 32) × 5/9
    return ???
}

// Prueba tus funciones:
print("🌡️ Conversiones:")
print("0°C = \(celsiusAFahrenheit(0))°F")      // debe ser 32.0
print("100°C = \(celsiusAFahrenheit(100))°F")  // debe ser 212.0
print("98.6°F = \(fahrenheitACelsius(98.6))°C") // debe ser ~37.0

// --- PARTE 2: Validador de contraseña ---
// La contraseña es válida si tiene al menos 8 caracteres
func validarContrasena(_ contrasena: String, minimoCaracteres: Int = 8) -> (esValida: Bool, mensaje: String) {
    guard contrasena.count >= minimoCaracteres else {
        return (false, "❌ Muy corta: necesita \(minimoCaracteres - contrasena.count) caracteres más")
    }
    return (true, "✅ Contraseña válida")
}

let contrasenas = ["abc", "mipass123", "x", "SuperSegura2026"]
print("\n🔐 Validación de contraseñas:")
for pass in contrasenas {
    let resultado = validarContrasena(pass)
    print("'\(pass)': \(resultado.mensaje)")
}

// --- PARTE 3 (DESAFÍO): Calculadora de propina ---
// Crea una función que calcule la propina de una cuenta de restaurante
// Parámetros: total de la cuenta, porcentaje de propina (default 15%), número de personas
// Retorna: propina total y propina por persona

func calcularPropina(cuenta: Double, porcentaje: Double = 15.0, personas: Int = 1) -> (propina: Double, porPersana: Double) {
    // Escribe tu implementación aquí
    ???
}

// Prueba:
let resultado = calcularPropina(cuenta: 850.0, porcentaje: 18.0, personas: 4)
print("\n🍽️ Propina: $\(resultado.propina)")
print("Por persona: $\(resultado.porPersana)")
```

---

## ✅ Resumen

| Concepto | Sintaxis |
|---|---|
| Función básica | `func nombre() { }` |
| Con parámetros | `func nombre(param: Tipo) { }` |
| Con retorno | `func nombre() -> Tipo { return valor }` |
| Etiqueta externa | `func nombre(externo interno: Tipo)` |
| Sin etiqueta | `func nombre(_ param: Tipo)` |
| Valor por defecto | `func nombre(param: Tipo = valor)` |
| Variádica | `func nombre(_ params: Tipo...)` |

---

⬅️ [03 — Control de Flujo](./03-control-de-flujo.md) | ➡️ [05 — Opcionales](./05-opcionales.md)

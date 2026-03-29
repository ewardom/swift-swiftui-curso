# 🔀 Control de Flujo en Swift

## 🎯 Lo que aprenderás

- Tomar decisiones con `if`, `else`, `guard`
- Elegir entre múltiples casos con `switch`
- Repetir acciones con `for`, `while`, `repeat-while`
- Controlar bucles con `break` y `continue`

---

## 1. if / else if / else

La estructura más básica para tomar decisiones:

```swift
let temperatura = 28

if temperatura > 30 {
    print("☀️ Hace mucho calor")
} else if temperatura > 20 {
    print("😊 Temperatura agradable")
} else if temperatura > 10 {
    print("🧥 Un poco frío")
} else {
    print("🥶 Hace mucho frío")
}
// Resultado: 😊 Temperatura agradable

// Con múltiples condiciones
let tieneTicket = true
let esVIP = false

if tieneTicket && esVIP {
    print("Acceso VIP")
} else if tieneTicket {
    print("Acceso general")
} else {
    print("Sin acceso")
}

// Operadores lógicos
// && = Y (ambas deben ser true)
// || = O (al menos una debe ser true)
// !  = NO (invierte el valor)
```

### Operador ternario — if en una línea
```swift
let edad = 20
let acceso = edad >= 18 ? "Permitido" : "Denegado"
print(acceso)  // Permitido

let hora = 14
let saludo = hora < 12 ? "Buenos días" : hora < 19 ? "Buenas tardes" : "Buenas noches"
print(saludo)  // Buenas tardes
```

---

## 2. guard

`guard` es como un "portero": verifica una condición y si NO se cumple, sale de la función. Ideal para validaciones:

```swift
func procesarEdad(_ edad: Int) {
    guard edad >= 0 else {
        print("⚠️ La edad no puede ser negativa")
        return   // sale de la función
    }

    guard edad <= 120 else {
        print("⚠️ Edad poco realista")
        return
    }

    // Si llegamos aquí, la edad es válida
    print("✅ Edad válida: \(edad) años")
}

procesarEdad(25)    // ✅ Edad válida: 25 años
procesarEdad(-5)    // ⚠️ La edad no puede ser negativa
procesarEdad(200)   // ⚠️ Edad poco realista
```

> 💡 **Tip:** Usa `guard` para las validaciones al inicio de una función. Mantiene el "camino feliz" sin muchos niveles de indentación.

---

## 3. switch

Ideal cuando tienes muchos casos posibles. Más limpio que múltiples `if-else`:

```swift
let diaSemana = "lunes"

switch diaSemana {
case "lunes":
    print("😴 Inicio de semana")
case "martes", "miércoles", "jueves":
    print("💪 A trabajar")
case "viernes":
    print("🎉 ¡Por fin viernes!")
case "sábado", "domingo":
    print("😎 Fin de semana")
default:
    print("❓ Día desconocido")
}

// Switch con rangos
let puntuacion = 85

switch puntuacion {
case 90...100:
    print("⭐ Excelente")
case 80..<90:
    print("✅ Muy bien")
case 70..<80:
    print("👍 Bien")
case 60..<70:
    print("⚠️ Regular")
default:
    print("❌ Reprobado")
}
// Resultado: ✅ Muy bien

// Switch con tuplas
let coordenada = (0, 0)

switch coordenada {
case (0, 0):
    print("📍 Origen")
case (let x, 0):
    print("📍 En el eje X: \(x)")
case (0, let y):
    print("📍 En el eje Y: \(y)")
case (let x, let y):
    print("📍 Punto en (\(x), \(y))")
}
```

---

## 4. for-in

Para repetir una acción un número determinado de veces o recorrer una colección:

```swift
// Rango de números
for i in 1...5 {
    print("Iteración \(i)")
}

// Rango sin incluir el último (0, 1, 2, 3, 4)
for i in 0..<5 {
    print(i)
}

// Recorrer un array
let frutas = ["🍎", "🍊", "🍋", "🍇"]
for fruta in frutas {
    print("Fruta: \(fruta)")
}

// Recorrer con índice usando enumerated()
for (indice, fruta) in frutas.enumerated() {
    print("\(indice + 1). \(fruta)")
}

// Recorrer un diccionario
let capitales = ["México": "CDMX", "España": "Madrid", "Argentina": "Buenos Aires"]
for (pais, capital) in capitales {
    print("\(pais) → \(capital)")
}

// Ignorar el valor con _
for _ in 1...3 {
    print("🔔 Beep!")
}

// stride — control del paso
for numero in stride(from: 0, to: 20, by: 5) {
    print(numero)  // 0, 5, 10, 15
}
```

---

## 5. while y repeat-while

Cuando no sabes cuántas veces necesitas repetir algo:

```swift
// while — verifica la condición ANTES de ejecutar
var contador = 0
while contador < 5 {
    print("Contador: \(contador)")
    contador += 1
}

// Ejemplo práctico: juego de adivinar
var intentos = 0
let numeroSecreto = 7
var adivinado = false

while !adivinado && intentos < 3 {
    intentos += 1
    let intento = intentos * 3  // simulamos intentos
    
    if intento == numeroSecreto {
        adivinado = true
        print("🎉 ¡Adivinaste en \(intentos) intentos!")
    } else {
        print("Intento \(intentos): \(intento) — incorrecto")
    }
}

// repeat-while — ejecuta AL MENOS una vez, luego verifica
var numero = 1
repeat {
    print("Número: \(numero)")
    numero *= 2
} while numero < 50
// Imprime: 1, 2, 4, 8, 16, 32
```

---

## 6. break y continue

Control adicional dentro de los bucles:

```swift
// break — termina el bucle completamente
for i in 1...10 {
    if i == 6 {
        break   // sale del bucle cuando i es 6
    }
    print(i)  // imprime 1, 2, 3, 4, 5
}

// continue — salta a la siguiente iteración
for i in 1...10 {
    if i % 2 == 0 {
        continue   // salta los números pares
    }
    print(i)  // imprime 1, 3, 5, 7, 9
}

// Ejemplo práctico: filtrar una lista
let calificaciones = [85, 42, 91, 67, 55, 78, 38, 95]
var aprobados: [Int] = []

for calificacion in calificaciones {
    if calificacion < 60 {
        continue   // ignorar reprobados
    }
    aprobados.append(calificacion)
}
print("Aprobados: \(aprobados)")   // [85, 91, 67, 78, 95]
```

---

## ⚠️ Errores Comunes

```swift
// ❌ Olvidar el default en switch (cuando no cubres todos los casos)
let valor = 5
// switch valor {         // ERROR si no hay default
// case 1: print("uno")
// }

// ✅ Siempre incluye default en switch con tipos no exhaustivos
switch valor {
case 1: print("uno")
default: print("otro valor")
}

// ❌ Bucle infinito por olvidar actualizar la condición
var x = 0
// while x < 10 {     // ⚠️ BUCLE INFINITO — olvidamos x += 1
//     print(x)
// }

// ✅ Asegúrate de modificar la variable de control
while x < 10 {
    print(x)
    x += 1   // ← esto es crucial
}
```

---

## 🛠️ Ejercicio en Swift Playgrounds

```swift
// ============================================
// 🛠️ EJERCICIO: Control de Flujo
// Instrucciones: completa los ??? y ejecuta
// ============================================

// --- PARTE 1: Clasificador de edades ---
// Completa la función para clasificar edades
func clasificarEdad(_ edad: Int) -> String {
    // Usa guard para validar que la edad sea positiva
    guard edad >= 0 else {
        return "⚠️ Edad inválida"
    }

    // Usa switch con rangos para clasificar
    switch edad {
    case ???:           // 0 a 12
        return "👶 Niño/a"
    case ???:           // 13 a 17
        return "🧑 Adolescente"
    case ???:           // 18 a 64
        return "🧑‍💼 Adulto/a"
    default:
        return "👴 Adulto/a mayor"
    }
}

// Prueba la función
let edades = [5, 15, 30, 70, -1]
for edad in edades {
    print("Edad \(edad): \(clasificarEdad(edad))")
}

// --- PARTE 2: Tabla de multiplicar ---
// Usa for-in para imprimir la tabla del número que elijas
let tabla = ???   // elige un número del 1 al 10

print("\n📊 Tabla del \(tabla):")
for i in 1...10 {
    print("\(tabla) × \(i) = \(tabla * i)")
}

// --- PARTE 3: Contador de vocales ---
// Cuenta cuántas vocales tiene una palabra
let palabra = "programación"
var contadorVocales = 0
let vocales: Set<Character> = ["a", "e", "i", "o", "u", "á", "é", "í", "ó", "ú"]

for letra in palabra {
    if vocales.contains(letra) {
        contadorVocales += 1
    }
}
print("\n🔤 La palabra '\(palabra)' tiene \(contadorVocales) vocales")

// --- PARTE 4 (DESAFÍO): FizzBuzz ---
// Imprime números del 1 al 30:
// - Si es divisible por 3: imprime "Fizz"
// - Si es divisible por 5: imprime "Buzz"
// - Si es divisible por ambos: imprime "FizzBuzz"
// - Si no: imprime el número

print("\n🎮 FizzBuzz:")
for numero in 1...30 {
    // Escribe tu solución aquí usando if/else o switch
}
```

---

## ✅ Resumen

| Estructura | Uso principal |
|---|---|
| `if / else` | Decisiones simples |
| `guard` | Validaciones al inicio de funciones |
| `switch` | Múltiples casos posibles |
| `for-in` | Repetir un número conocido de veces |
| `while` | Repetir mientras se cumpla una condición |
| `repeat-while` | Igual que while, pero ejecuta al menos una vez |
| `break` | Salir del bucle completamente |
| `continue` | Saltar a la siguiente iteración |

---

⬅️ [02 — Tipos de Datos](./02-tipos-de-datos.md) | ➡️ [04 — Funciones](./04-funciones.md)

# ❓ Opcionales en Swift

## 🎯 Lo que aprenderás

- Qué es `nil` y por qué existe en Swift
- Cómo declarar y usar opcionales
- Optional binding con `if let` y `guard let`
- Nil coalescing operator `??`
- Optional chaining `?.`
- Cuándo NO usar forced unwrapping `!`

---

## 1. ¿Qué es nil y por qué existe?

En Swift, `nil` significa **"ausencia de valor"**. No es cero, no es vacío — significa que no hay ningún valor.

El problema en otros lenguajes es que cualquier variable podría ser `nil` sin que lo sepas, causando crashes. Swift resuelve esto obligándote a ser **explícito** cuando un valor podría ser nil:

```swift
// En Swift, esto NO compila — una variable normal NUNCA puede ser nil:
// var nombre: String = nil  // ❌ ERROR

// Para permitir nil, debes declararlo como Opcional con ?
var nombre: String? = nil   // ✅ Esto sí funciona
var edad: Int? = nil        // ✅

// Un opcional puede tener valor o no tenerlo
var ciudad: String? = "Monterrey"  // tiene valor
ciudad = nil                        // ahora no tiene valor
```

> 💡 **Tip:** Piensa en un opcional como una caja que puede estar llena (tiene valor) o vacía (nil).

---

## 2. Declarar Opcionales

```swift
// Opcional de String
var apellido: String?         // automáticamente es nil
var email: String? = "usuario@email.com"

// Opcional de Int
var puntuacion: Int?
var nivel: Int? = 5

// Opcional de tipos propios
struct Usuario {
    var nombre: String
    var foto: String?    // la foto es opcional — puede no tenerla
}

let usuario = Usuario(nombre: "Ana", foto: nil)
let usuario2 = Usuario(nombre: "Luis", foto: "foto_luis.jpg")
```

---

## 3. Optional Binding — if let

La forma más común y segura de "abrir" un opcional:

```swift
var nombreUsuario: String? = "Sofía"

// if let "desenvuelve" el opcional de forma segura
if let nombre = nombreUsuario {
    // Aquí 'nombre' es String (no String?), ya tiene valor seguro
    print("Hola, \(nombre)!")   // Hola, Sofía!
} else {
    print("No hay nombre disponible")
}

// Si el opcional es nil, entra al else
nombreUsuario = nil
if let nombre = nombreUsuario {
    print("Hola, \(nombre)!")
} else {
    print("No hay nombre disponible")   // esto se ejecuta
}

// Swift 5.7+: puedes usar el mismo nombre de variable
var email: String? = "hola@email.com"
if let email {   // mismo nombre, más limpio
    print("Email: \(email)")
}

// Múltiples opcionales en un solo if let
var usuario: String? = "Carlos"
var contrasena: String? = "1234"

if let usuario, let contrasena {
    print("Login: \(usuario) / \(contrasena)")
} else {
    print("Faltan datos de login")
}
```

---

## 4. guard let

Ideal para validar al inicio de una función y salir rápido si algo falla:

```swift
func mostrarPerfil(nombre: String?, edad: Int?) {
    // guard let "saca" el valor y lo hace disponible en el resto de la función
    guard let nombre = nombre else {
        print("⚠️ Se requiere un nombre")
        return
    }

    guard let edad = edad else {
        print("⚠️ Se requiere una edad")
        return
    }

    // Si llegamos aquí, 'nombre' y 'edad' están garantizados
    print("👤 \(nombre), \(edad) años")
}

mostrarPerfil(nombre: "María", edad: 28)  // 👤 María, 28 años
mostrarPerfil(nombre: nil, edad: 28)       // ⚠️ Se requiere un nombre
mostrarPerfil(nombre: "Luis", edad: nil)   // ⚠️ Se requiere una edad

// guard let con mismo nombre (Swift 5.7+)
func procesarEmail(_ emailOpcional: String?) {
    guard let emailOpcional else {
        print("No hay email")
        return
    }
    print("Procesando: \(emailOpcional)")
}
```

> 💡 **Tip:** Usa `if let` cuando quieras hacer algo CON el valor. Usa `guard let` cuando quieras SALIR si no hay valor.

---

## 5. Nil Coalescing Operator ??

Proporciona un valor por defecto cuando el opcional es nil:

```swift
// Sintaxis: opcional ?? valorPorDefecto
var nombreGuardado: String? = nil
let nombre = nombreGuardado ?? "Invitado"
print(nombre)   // Invitado

nombreGuardado = "Pedro"
let nombre2 = nombreGuardado ?? "Invitado"
print(nombre2)  // Pedro

// Ejemplos prácticos
var puntos: Int? = nil
let puntosActuales = puntos ?? 0
print("Puntos: \(puntosActuales)")   // 0

var descripcion: String? = "App increíble"
let textoMostrar = descripcion ?? "Sin descripción disponible"

// Encadenamiento de ??
var a: String? = nil
var b: String? = nil
var c: String? = "Valor encontrado"
let resultado = a ?? b ?? c ?? "Default final"
print(resultado)   // Valor encontrado
```

---

## 6. Optional Chaining ?.

Accede a propiedades y métodos de un opcional sin necesidad de desenvolverlo primero:

```swift
struct Direccion {
    var ciudad: String
    var codigoPostal: String
}

struct Persona {
    var nombre: String
    var direccion: Direccion?  // puede no tener dirección
}

let persona1 = Persona(nombre: "Ana", direccion: Direccion(ciudad: "Guadalajara", codigoPostal: "44100"))
let persona2 = Persona(nombre: "Bob", direccion: nil)

// Sin optional chaining — muy verboso
if let dir = persona1.direccion {
    print(dir.ciudad)
}

// Con optional chaining — mucho más limpio
print(persona1.direccion?.ciudad ?? "Sin ciudad")   // Guadalajara
print(persona2.direccion?.ciudad ?? "Sin ciudad")   // Sin ciudad

// Llamar métodos con optional chaining
var texto: String? = "  hola mundo  "
let limpio = texto?.trimmingCharacters(in: .whitespaces).uppercased()
print(limpio ?? "")   // HOLA MUNDO

texto = nil
let limpio2 = texto?.trimmingCharacters(in: .whitespaces)
print(limpio2 ?? "texto vacío")   // texto vacío
```

---

## 7. Forced Unwrapping ! — Úsalo con Cuidado

El `!` "fuerza" la apertura de un opcional. Si el valor es nil, la app **crashea**:

```swift
var nombre: String? = "Elena"

// Forced unwrapping — peligroso si es nil
print(nombre!)   // Elena — funciona porque tiene valor

nombre = nil
// print(nombre!)  // 💥 CRASH — fatal error: unexpectedly found nil

// ✅ Cuándo es aceptable usar !:
// 1. IBOutlets en UIKit (se conectan antes de usarse)
// 2. Cuando estás 100% seguro de que no es nil
// 3. En pruebas/tests

// ❌ Nunca hagas esto:
let edadTexto = "no es un número"
// let edad = Int(edadTexto)!   // 💥 CRASH seguro
```

> ⚠️ **Regla:** Si usas `!` y no estás absolutamente seguro del valor, es un bug esperando ocurrir. Prefiere siempre `if let`, `guard let` o `??`.

---

## 8. Ejemplo Completo: Buscador de Contactos

```swift
struct Contacto {
    var nombre: String
    var telefono: String?
    var email: String?
    var notas: String?
}

let contactos = [
    Contacto(nombre: "Ana", telefono: "555-1111", email: "ana@email.com", notas: nil),
    Contacto(nombre: "Luis", telefono: nil, email: "luis@email.com", notas: "Cliente VIP"),
    Contacto(nombre: "Marta", telefono: "555-3333", email: nil, notas: nil)
]

func mostrarContacto(_ contacto: Contacto) {
    print("👤 \(contacto.nombre)")
    print("   📱 \(contacto.telefono ?? "Sin teléfono")")
    print("   📧 \(contacto.email ?? "Sin email")")

    if let notas = contacto.notas {
        print("   📝 \(notas)")
    }
    print("")
}

for contacto in contactos {
    mostrarContacto(contacto)
}
```

---

## ⚠️ Errores Comunes

```swift
// ❌ Usar ! sin verificar
var valor: Int? = nil
// print(valor!)   // 💥 CRASH

// ✅ Siempre verificar antes
if let valor {
    print(valor)
}

// ❌ Confundir String? con String
var a: String? = "Hola"
var b: String = "Mundo"
// let c = a + b   // ❌ ERROR — no puedes sumar String? con String

// ✅ Desenvolver primero
let c = (a ?? "") + b
```

---

## 🛠️ Ejercicio en Swift Playgrounds

```swift
// ============================================
// 🛠️ EJERCICIO: Opcionales
// Instrucciones: completa los ??? y ejecuta
// ============================================

// --- PARTE 1: Perfil de usuario ---
struct PerfilUsuario {
    var nombre: String
    var apellido: String?
    var edad: Int?
    var ciudad: String?
    var sitioWeb: String?
}

// Crea dos perfiles: uno completo y uno con solo el nombre
let perfilCompleto = PerfilUsuario(
    nombre: "???",
    apellido: "???",
    edad: ???,
    ciudad: "???",
    sitioWeb: "???"
)

let perfilMinimo = PerfilUsuario(
    nombre: "???",
    apellido: nil,
    edad: nil,
    ciudad: nil,
    sitioWeb: nil
)

// Función que muestra el perfil usando ?? para valores por defecto
func mostrarPerfil(_ perfil: PerfilUsuario) {
    let nombreCompleto = perfil.nombre + " " + (perfil.apellido ?? "")
    print("👤 \(nombreCompleto.trimmingCharacters(in: .whitespaces))")
    print("   Edad: \(perfil.edad ?? 0) años")
    print("   Ciudad: \(perfil.ciudad ?? "No especificada")")
    print("   Web: \(perfil.sitioWeb ?? "Sin sitio web")")
}

mostrarPerfil(perfilCompleto)
print("---")
mostrarPerfil(perfilMinimo)

// --- PARTE 2: Conversor seguro ---
// Convierte strings a números de forma segura
func convertirAEntero(_ texto: String) -> String {
    if let numero = Int(texto) {
        return "✅ Número válido: \(numero)"
    } else {
        return "❌ '\(texto)' no es un número válido"
    }
}

let pruebas = ["42", "hola", "100", "3.14", "-7"]
print("\n🔢 Conversiones:")
for texto in pruebas {
    print(convertirAEntero(texto))
}

// --- PARTE 3 (DESAFÍO): Cadena de opcionales ---
struct Motor {
    var potencia: Int
    var tipo: String
}

struct Coche {
    var marca: String
    var motor: Motor?
}

struct Garage {
    var nombre: String
    var coche: Coche?
}

let miGarage = Garage(
    nombre: "Mi Garage",
    coche: Coche(
        marca: "???",
        motor: Motor(potencia: ???, tipo: "???")
    )
)

// Usa optional chaining para acceder a la potencia del motor
let potencia = miGarage.coche?.motor?.potencia
print("\n🚗 Potencia del motor: \(potencia ?? 0) HP")

// Ahora prueba con un garage sin coche:
let garageVacio = Garage(nombre: "Garage vacío", coche: nil)
let potencia2 = garageVacio.coche?.motor?.potencia
print("🚗 Potencia (garage vacío): \(potencia2 ?? 0) HP")
```

---

## ✅ Resumen

| Técnica | Sintaxis | Cuándo usar |
|---|---|---|
| Declarar opcional | `var x: Tipo?` | Valor que puede ser nil |
| Optional binding | `if let x = opcional` | Cuando quieres usar el valor |
| Guard let | `guard let x = opcional else { return }` | Validar al inicio de función |
| Nil coalescing | `opcional ?? valorDefault` | Proveer valor por defecto |
| Optional chaining | `opcional?.propiedad` | Acceder sin desenvolver |
| Forced unwrap | `opcional!` | Solo cuando estás 100% seguro |

---

⬅️ [04 — Funciones](./04-funciones.md) | ➡️ [Módulo 02 — Estructuras y Clases](../modulo-02-swift-intermedio/01-estructuras-y-clases.md)

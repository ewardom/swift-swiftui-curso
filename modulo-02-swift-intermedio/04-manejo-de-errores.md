# 🚨 Manejo de Errores en Swift

## 🎯 Lo que aprenderás

- Cómo definir errores con el protocolo `Error`
- Lanzar errores con `throw`
- Capturar errores con `try / catch`
- Usar `try?` y `try!`
- El tipo `Result<Success, Failure>`

---

## 1. ¿Por qué manejar errores?

En cualquier app real, las cosas pueden salir mal: una conexión falla, un archivo no existe, el usuario ingresa datos incorrectos. Swift te obliga a manejar estos casos explícitamente, haciendo tu código más robusto.

```swift
// Sin manejo de errores — peligroso
func dividir(_ a: Int, entre b: Int) -> Int {
    return a / b   // 💥 CRASH si b es 0
}

// Con manejo de errores — seguro
func dividirSeguro(_ a: Int, entre b: Int) throws -> Int {
    guard b != 0 else {
        throw NSError(domain: "MatematicaError", code: 0)
    }
    return a / b
}
```

---

## 2. Definir Errores con enum

La forma más limpia de definir errores en Swift:

```swift
// Errores de validación de formulario
enum ErrorFormulario: Error {
    case campoVacio(campo: String)
    case emailInvalido
    case contrasenaMuyCorta(minimoCaracteres: Int)
    case edadFueraDeRango(min: Int, max: Int)
}

// Errores de red
enum ErrorRed: Error {
    case sinConexion
    case timeOut
    case respuestaInvalida(codigo: Int)
    case datosCorruptos
}

// Errores de archivo
enum ErrorArchivo: Error {
    case noEncontrado(ruta: String)
    case sinPermisos
    case formatoIncorrecto
}
```

---

## 3. throw — Lanzar Errores

Una función que puede lanzar errores se marca con `throws`:

```swift
struct Usuario {
    var nombre: String
    var email: String
    var edad: Int
}

func validarUsuario(nombre: String, email: String, edad: Int) throws -> Usuario {
    // Validar nombre
    guard !nombre.isEmpty else {
        throw ErrorFormulario.campoVacio(campo: "nombre")
    }

    // Validar email
    guard email.contains("@") && email.contains(".") else {
        throw ErrorFormulario.emailInvalido
    }

    // Validar edad
    guard edad >= 13 && edad <= 120 else {
        throw ErrorFormulario.edadFueraDeRango(min: 13, max: 120)
    }

    return Usuario(nombre: nombre, email: email, edad: edad)
}
```

---

## 4. try / catch — Capturar Errores

```swift
// try — dentro de un bloque do-catch
do {
    let usuario = try validarUsuario(
        nombre: "Ana",
        email: "ana@email.com",
        edad: 25
    )
    print("✅ Usuario válido: \(usuario.nombre)")
} catch ErrorFormulario.campoVacio(let campo) {
    print("❌ El campo '\(campo)' está vacío")
} catch ErrorFormulario.emailInvalido {
    print("❌ El email no tiene formato válido")
} catch ErrorFormulario.edadFueraDeRango(let min, let max) {
    print("❌ La edad debe estar entre \(min) y \(max)")
} catch {
    // Captura cualquier otro error
    print("❌ Error desconocido: \(error)")
}

// Probar con datos inválidos
do {
    let usuario = try validarUsuario(
        nombre: "",
        email: "invalido",
        edad: 5
    )
    print("✅ \(usuario.nombre)")
} catch ErrorFormulario.campoVacio(let campo) {
    print("❌ El campo '\(campo)' está vacío")
    // Sale aquí — nombre vacío
} catch {
    print("❌ \(error)")
}
```

---

## 5. try? y try!

```swift
// try? — convierte el resultado en Optional (nil si hay error)
let usuario1 = try? validarUsuario(nombre: "Luis", email: "luis@email.com", edad: 30)
print(usuario1?.nombre ?? "Error al crear usuario")   // Luis

let usuario2 = try? validarUsuario(nombre: "", email: "invalido", edad: 5)
print(usuario2?.nombre ?? "Error al crear usuario")   // Error al crear usuario

// try! — fuerza el resultado, crash si hay error (úsalo solo si estás 100% seguro)
// let usuario3 = try! validarUsuario(nombre: "", email: "", edad: 0)  // 💥 CRASH
```

> 💡 **Tip:** `try?` es útil cuando no te importa el error específico, solo si tuvo éxito o no. Úsalo con cuidado — puedes perder información valiosa del error.

---

## 6. Propagación de Errores

Un error puede propagarse hacia arriba en la cadena de llamadas:

```swift
enum ErrorApp: Error {
    case datosInvalidos
    case errorRed(descripcion: String)
    case errorDesconocido
}

// Esta función puede lanzar errores
func obtenerDatosDelServidor() throws -> String {
    // Simulamos un error de red
    throw ErrorApp.errorRed(descripcion: "Timeout al conectar")
}

// Esta función también puede lanzar (propaga el error)
func cargarPerfil() throws -> String {
    let datos = try obtenerDatosDelServidor()  // el error se propaga
    return "Perfil: \(datos)"
}

// Aquí lo capturamos finalmente
func iniciarApp() {
    do {
        let perfil = try cargarPerfil()
        print(perfil)
    } catch ErrorApp.errorRed(let descripcion) {
        print("🌐 Error de red: \(descripcion)")
    } catch {
        print("❌ Error: \(error)")
    }
}

iniciarApp()   // 🌐 Error de red: Timeout al conectar
```

---

## 7. Result<Success, Failure>

`Result` es un tipo que encapsula un éxito o un fracaso, ideal para operaciones asíncronas:

```swift
enum ErrorBusqueda: Error {
    case sinResultados
    case errorConexion
}

struct Articulo {
    var titulo: String
    var autor: String
}

// Función que retorna Result
func buscarArticulo(titulo: String) -> Result<Articulo, ErrorBusqueda> {
    guard !titulo.isEmpty else {
        return .failure(.sinResultados)
    }

    // Simulamos encontrar un artículo
    if titulo.lowercased().contains("swift") {
        let articulo = Articulo(titulo: titulo, autor: "Apple Developer")
        return .success(articulo)
    } else {
        return .failure(.sinResultados)
    }
}

// Usar Result con switch
let resultado1 = buscarArticulo(titulo: "Introducción a Swift")
switch resultado1 {
case .success(let articulo):
    print("✅ Encontrado: '\(articulo.titulo)' por \(articulo.autor)")
case .failure(let error):
    switch error {
    case .sinResultados:
        print("🔍 Sin resultados")
    case .errorConexion:
        print("🌐 Error de conexión")
    }
}

// Usar Result con get()
let resultado2 = buscarArticulo(titulo: "Recetas de cocina")
if let articulo = try? resultado2.get() {
    print("Artículo: \(articulo.titulo)")
} else {
    print("No se encontró el artículo")
}
```

---

## 8. Ejemplo Completo: Validador de App

```swift
enum ErrorRegistro: Error, LocalizedError {
    case nombreMuyCorto
    case emailDuplicado
    case contrasenaMuyDebil
    case menorDeEdad

    // Mensajes legibles para el usuario
    var errorDescription: String? {
        switch self {
        case .nombreMuyCorto:    return "El nombre debe tener al menos 3 caracteres"
        case .emailDuplicado:    return "Este email ya está registrado"
        case .contrasenaMuyDebil: return "La contraseña debe tener al menos 8 caracteres"
        case .menorDeEdad:       return "Debes tener al menos 13 años"
        }
    }
}

func registrarUsuario(nombre: String, email: String, contrasena: String, edad: Int) throws -> String {
    guard nombre.count >= 3 else { throw ErrorRegistro.nombreMuyCorto }
    guard contrasena.count >= 8 else { throw ErrorRegistro.contrasenaMuyDebil }
    guard edad >= 13 else { throw ErrorRegistro.menorDeEdad }

    // Emails "duplicados" de ejemplo
    let emailsExistentes = ["admin@app.com", "test@app.com"]
    guard !emailsExistentes.contains(email) else { throw ErrorRegistro.emailDuplicado }

    return "✅ Usuario '\(nombre)' registrado con éxito"
}

// Probar varios casos
let casosDePrueba: [(String, String, String, Int)] = [
    ("Ana García", "ana@email.com", "segura123", 25),
    ("Bo", "bo@email.com", "pass", 20),
    ("Luis", "admin@app.com", "password1", 18),
    ("María", "maria@email.com", "clave2026", 10)
]

for (nombre, email, pass, edad) in casosDePrueba {
    do {
        let mensaje = try registrarUsuario(nombre: nombre, email: email, contrasena: pass, edad: edad)
        print(mensaje)
    } catch let error as ErrorRegistro {
        print("❌ \(nombre): \(error.errorDescription ?? "Error")")
    }
}
```

---

## ⚠️ Errores Comunes

```swift
// ❌ Olvidar el try
// let resultado = validarUsuario(nombre: "Ana", email: "a@b.com", edad: 20)  // ERROR

// ✅ Siempre usar try dentro de do-catch
do {
    let resultado = try validarUsuario(nombre: "Ana", email: "a@b.com", edad: 20)
    print(resultado)
} catch {
    print(error)
}

// ❌ Usar try! sin certeza
// let usuario = try! validarUsuario(nombre: "", email: "", edad: 0)  // 💥 CRASH

// ✅ Preferir try? o do-catch
let usuario = try? validarUsuario(nombre: "", email: "", edad: 0)
print(usuario?.nombre ?? "Datos inválidos")
```

---

## 🛠️ Ejercicio en Swift Playgrounds

```swift
// ============================================
// 🛠️ EJERCICIO: Manejo de Errores
// ============================================

// --- PARTE 1: Crea tu propio sistema de errores ---
enum ErrorCalculadora: Error {
    case divisionEntreCero
    case raizDeNumeroNegativo
    case numeroDemasiadoGrande(limite: Double)
}

// Implementa estas funciones:
func dividir(_ a: Double, entre b: Double) throws -> Double {
    // Lanza ErrorCalculadora.divisionEntreCero si b es 0
    ???
}

func raizCuadrada(de numero: Double) throws -> Double {
    // Lanza ErrorCalculadora.raizDeNumeroNegativo si numero < 0
    ???
}

func verificarLimite(_ numero: Double, limite: Double = 1_000_000) throws -> Double {
    // Lanza ErrorCalculadora.numeroDemasiadoGrande si supera el límite
    ???
}

// Prueba tus funciones:
print("🧮 Calculadora Segura:")

let operaciones: [(String, () throws -> Double)] = [
    ("10 / 2",   { try dividir(10, entre: 2) }),
    ("5 / 0",    { try dividir(5, entre: 0) }),
    ("√16",      { try raizCuadrada(de: 16) }),
    ("√-4",      { try raizCuadrada(de: -4) }),
    ("999999",   { try verificarLimite(999_999) }),
    ("9999999",  { try verificarLimite(9_999_999) })
]

for (descripcion, operacion) in operaciones {
    do {
        let resultado = try operacion()
        print("✅ \(descripcion) = \(resultado)")
    } catch ErrorCalculadora.divisionEntreCero {
        print("❌ \(descripcion): No se puede dividir entre cero")
    } catch ErrorCalculadora.raizDeNumeroNegativo {
        print("❌ \(descripcion): No existe raíz de número negativo")
    } catch ErrorCalculadora.numeroDemasiadoGrande(let limite) {
        print("❌ \(descripcion): Supera el límite de \(limite)")
    } catch {
        print("❌ Error desconocido: \(error)")
    }
}

// --- PARTE 2: Result type ---
func buscarProducto(id: Int) -> Result<String, Error> {
    let productos = [1: "iPhone", 2: "iPad", 3: "MacBook"]

    if let producto = productos[id] {
        return .success(producto)
    } else {
        return .failure(NSError(domain: "No encontrado", code: 404))
    }
}

print("\n🔍 Búsqueda de productos:")
for id in [1, 2, 5, 3] {
    switch buscarProducto(id: id) {
    case .success(let nombre):
        print("✅ ID \(id): \(nombre)")
    case .failure:
        print("❌ ID \(id): Producto no encontrado")
    }
}
```

---

## ✅ Resumen

| Mecanismo | Uso |
|---|---|
| `throws` | Marcar función que puede lanzar errores |
| `throw` | Lanzar un error |
| `do { try } catch` | Capturar y manejar errores |
| `try?` | Convertir a Optional (nil si hay error) |
| `try!` | Forzar — solo si estás seguro |
| `Result<S,F>` | Encapsular éxito o fracaso |

---

⬅️ [03 — Closures](./03-closures.md) | ➡️ [Módulo 03 — Introducción a SwiftUI](../modulo-03-swiftui-esenciales/01-introduccion-swiftui.md)

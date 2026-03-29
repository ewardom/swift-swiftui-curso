# 🔄 @State y @Binding en SwiftUI

## 🎯 Lo que aprenderás

- Qué es el estado en SwiftUI y por qué importa
- `@State`: estado local de una vista
- `@Binding`: compartir estado entre vistas padre e hijo
- Flujo de datos unidireccional
- Ejemplos prácticos: contador, toggle, formulario

---

## 1. ¿Qué es el Estado?

El **estado** es cualquier dato que puede cambiar y que, cuando cambia, actualiza la interfaz automáticamente. En SwiftUI, cuando el estado cambia, la vista se **reconstruye** con los nuevos valores.

```
┌─────────────────────────────────────────────┐
│           Ciclo de vida en SwiftUI           │
│                                              │
│   Estado cambia → Vista se reconstruye       │
│        ↑                    ↓                │
│   Usuario interactúa ← UI se actualiza       │
└─────────────────────────────────────────────┘
```

Sin `@State`, los structs en Swift son inmutables — no podrías cambiar valores en la vista:

```swift
// ❌ Esto NO funciona — structs son inmutables
struct ContadorRoto: View {
    var contador = 0   // no es @State

    var body: some View {
        Button("Incrementar") {
            contador += 1   // ❌ ERROR: no se puede modificar
        }
    }
}

// ✅ Con @State funciona perfectamente
struct ContadorCorrecto: View {
    @State private var contador = 0   // ✅ SwiftUI gestiona los cambios

    var body: some View {
        Button("Incrementar: \(contador)") {
            contador += 1   // ✅ Funciona y actualiza la UI
        }
    }
}
```

---

## 2. @State en Profundidad

```swift
struct EjemplosState: View {
    // Tipos básicos
    @State private var nombre = ""
    @State private var edad = 0
    @State private var activo = false
    @State private var opcion = "A"

    // Colecciones
    @State private var elementos: [String] = []
    @State private var seleccionados: Set<String> = []

    // Tipos propios
    @State private var color: Color = .blue

    var body: some View {
        VStack(spacing: 20) {
            // TextField vinculado a @State
            TextField("Tu nombre", text: $nombre)
                .textFieldStyle(.roundedBorder)
                .padding(.horizontal)

            // Texto que se actualiza automáticamente
            if !nombre.isEmpty {
                Text("Hola, \(nombre)! 👋")
                    .font(.headline)
                    .transition(.opacity)
            }

            // Toggle vinculado a @State
            Toggle("Notificaciones activas", isOn: $activo)
                .padding(.horizontal)

            // Stepper
            Stepper("Edad: \(edad)", value: $edad, in: 0...120)
                .padding(.horizontal)

            // ColorPicker
            ColorPicker("Color favorito", selection: $color)
                .padding(.horizontal)

            // Mostrar el color seleccionado
            Circle()
                .fill(color)
                .frame(width: 50, height: 50)
        }
        .animation(.spring(), value: nombre)
    }
}
```

---

## 3. El Signo $ — Binding

Cuando usas `$variable` estás creando un `Binding`: una referencia bidireccional al estado:

```swift
@State private var texto = "Hola"

// Sin $: solo lees el valor
Text(texto)           // lee "Hola"

// Con $: lectura Y escritura (binding)
TextField("...", text: $texto)   // puede leer y modificar 'texto'
```

---

## 4. @Binding — Compartir Estado

`@Binding` permite que una vista **hijo** modifique el estado de su vista **padre**:

```swift
// ✅ Vista padre — tiene el @State
struct VistaPadre: View {
    @State private var contador = 0
    @State private var activo = false

    var body: some View {
        VStack(spacing: 20) {
            Text("Contador: \(contador)")
                .font(.title)

            Text(activo ? "✅ Activo" : "⭕ Inactivo")

            // Pasa el binding con $
            VistaHijo(contador: $contador, activo: $activo)
        }
        .padding()
    }
}

// Vista hijo — recibe @Binding
struct VistaHijo: View {
    @Binding var contador: Int   // NO tiene el valor, tiene la referencia
    @Binding var activo: Bool

    var body: some View {
        VStack(spacing: 12) {
            HStack(spacing: 16) {
                Button("➖") { contador -= 1 }
                    .buttonStyle(.bordered)
                Button("➕") { contador += 1 }
                    .buttonStyle(.borderedProminent)
            }

            Toggle("Activar", isOn: $activo)
                .toggleStyle(.button)

            Button("Reiniciar") {
                contador = 0
                activo = false
            }
            .buttonStyle(.bordered)
            .tint(.red)
        }
    }
}
```

---

## 5. Flujo de Datos Unidireccional

```
┌──────────────────────────────────────────────┐
│           Flujo de datos en SwiftUI           │
│                                               │
│   VistaPadre                                  │
│   @State var dato = "valor"                   │
│        │                                      │
│        │  pasa $dato (Binding)                │
│        ↓                                      │
│   VistaHijo                                   │
│   @Binding var dato: String                   │
│        │                                      │
│        │  modifica dato                       │
│        ↓                                      │
│   SwiftUI actualiza VistaPadre automáticamente│
└──────────────────────────────────────────────┘
```

---

## 6. Ejemplo Completo: Formulario de Registro

```swift
import SwiftUI

struct FormularioRegistro: View {
    @State private var nombre = ""
    @State private var email = ""
    @State private var contrasena = ""
    @State private var confirmarContrasena = ""
    @State private var fechaNacimiento = Date()
    @State private var aceptaTerminos = false
    @State private var mostrarContrasena = false
    @State private var registroExitoso = false

    var formularioValido: Bool {
        !nombre.isEmpty &&
        email.contains("@") &&
        contrasena.count >= 8 &&
        contrasena == confirmarContrasena &&
        aceptaTerminos
    }

    var body: some View {
        NavigationStack {
            Form {
                // Información personal
                Section("Información Personal") {
                    TextField("Nombre completo", text: $nombre)
                    TextField("Email", text: $email)
                        .keyboardType(.emailAddress)
                        .autocapitalization(.none)
                    DatePicker("Fecha de nacimiento",
                               selection: $fechaNacimiento,
                               in: ...Date(),
                               displayedComponents: .date)
                }

                // Seguridad
                Section("Contraseña") {
                    HStack {
                        if mostrarContrasena {
                            TextField("Contraseña", text: $contrasena)
                        } else {
                            SecureField("Contraseña", text: $contrasena)
                        }
                        Button {
                            mostrarContrasena.toggle()
                        } label: {
                            Image(systemName: mostrarContrasena
                                  ? "eye.slash" : "eye")
                                .foregroundStyle(.secondary)
                        }
                    }

                    SecureField("Confirmar contraseña",
                                text: $confirmarContrasena)

                    // Indicador de fortaleza
                    if !contrasena.isEmpty {
                        IndicadorFortaleza(contrasena: contrasena)
                    }
                }

                // Validaciones en tiempo real
                if !contrasena.isEmpty && !confirmarContrasena.isEmpty
                    && contrasena != confirmarContrasena {
                    Section {
                        Label("Las contraseñas no coinciden",
                              systemImage: "exclamationmark.triangle")
                            .foregroundStyle(.red)
                            .font(.caption)
                    }
                }

                // Términos
                Section {
                    Toggle(isOn: $aceptaTerminos) {
                        Text("Acepto los ")
                        + Text("Términos y Condiciones")
                            .foregroundStyle(.blue)
                    }
                }

                // Botón
                Section {
                    Button {
                        registroExitoso = true
                    } label: {
                        HStack {
                            Spacer()
                            Text("Crear Cuenta")
                                .fontWeight(.semibold)
                            Spacer()
                        }
                    }
                    .disabled(!formularioValido)
                }
            }
            .navigationTitle("Crear Cuenta")
            .alert("¡Registro Exitoso!", isPresented: $registroExitoso) {
                Button("Continuar") { }
            } message: {
                Text("Bienvenido, \(nombre)! Tu cuenta ha sido creada.")
            }
        }
    }
}

// Vista auxiliar
struct IndicadorFortaleza: View {
    let contrasena: String

    var fortaleza: (nivel: String, color: Color, valor: Double) {
        let n = contrasena.count
        if n < 6 { return ("Débil", .red, 0.25) }
        if n < 10 { return ("Regular", .orange, 0.5) }
        if n < 14 { return ("Fuerte", .yellow, 0.75) }
        return ("Muy fuerte", .green, 1.0)
    }

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            ProgressView(value: fortaleza.valor)
                .tint(fortaleza.color)
            Text("Contraseña \(fortaleza.nivel)")
                .font(.caption)
                .foregroundStyle(fortaleza.color)
        }
    }
}

#Preview {
    FormularioRegistro()
}
```

---

## ⚠️ Errores Comunes

```swift
// ❌ No usar @State para datos que cambian
struct VistaRota: View {
    var contador = 0   // no actualiza la UI
    var body: some View {
        Button("Tap") { /* contador += 1  — ERROR */ }
    }
}

// ❌ Pasar valor en lugar de binding al hijo
struct Padre: View {
    @State private var valor = 0
    var body: some View {
        // Hijo(valor: valor)   // ❌ el hijo no puede modificarlo
        Hijo(valor: $valor)    // ✅ con $ pasa el binding
    }
}

// ❌ Olvidar private en @State
// @State var x = 0   // accesible desde afuera, mal encapsulado
// ✅
// @State private var x = 0
```

---

## ✅ Resumen

| Property Wrapper | Dónde | Qué hace |
|---|---|---|
| `@State` | Vista propietaria | Almacena y gestiona estado local |
| `@Binding` | Vista hijo | Referencia bidireccional al @State del padre |
| `$variable` | Al pasar a hijos | Crea el Binding desde un @State |

**Reglas:**
- `@State` siempre es `private`
- Usa `@Binding` cuando el hijo necesita **modificar** el estado del padre
- Si el hijo solo **lee** el valor, pásalo sin `$`

---

⬅️ [Módulo 03 — Navegación](../modulo-03-swiftui-esenciales/05-navegacion.md) | ➡️ [02 — @ObservedObject y @StateObject](./02-observedobject-stateobject.md)

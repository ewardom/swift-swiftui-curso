# 🎨 Introducción a SwiftUI

## 🎯 Lo que aprenderás

- Qué es SwiftUI y por qué es revolucionario
- Diferencia entre declarativo e imperativo
- Estructura básica de una app SwiftUI
- El ciclo de vida: @main, App, Scene, WindowGroup
- Vista previa con #Preview
- Tu primera app SwiftUI completa

---

## 1. ¿Qué es SwiftUI?

SwiftUI es el framework moderno de Apple para construir interfaces de usuario. Lanzado en 2019, funciona en **todas las plataformas de Apple**: iPhone, iPad, Mac, Apple Watch y Apple TV.

### Paradigma Declarativo vs Imperativo

```swift
// ❌ IMPERATIVO (UIKit — cómo hacerlo paso a paso)
let label = UILabel()
label.text = "Hola, mundo!"
label.font = UIFont.systemFont(ofSize: 24, weight: .bold)
label.textColor = .blue
label.textAlignment = .center
view.addSubview(label)
// ...y aún falta configurar constraints...

// ✅ DECLARATIVO (SwiftUI — qué mostrar)
Text("Hola, mundo!")
    .font(.title)
    .foregroundStyle(.blue)
    .multilineTextAlignment(.center)
```

> 💡 **Clave:** En SwiftUI describes **qué** quieres ver, no **cómo** construirlo. SwiftUI se encarga del resto.

---

## 2. Estructura Básica de una App

Cuando creas un nuevo proyecto en Xcode con SwiftUI, obtienes esta estructura:

```swift
// MiAppApp.swift — punto de entrada de la app
import SwiftUI

@main  // le dice a Swift que aquí empieza la app
struct MiAppApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()  // la primera vista que se muestra
        }
    }
}
```

```swift
// ContentView.swift — tu primera vista
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundStyle(.tint)
            Text("Hello, world!")
        }
        .padding()
    }
}

// Vista previa en Xcode (no afecta la app)
#Preview {
    ContentView()
}
```

---

## 3. El Protocolo View

Todo en SwiftUI es una `View`. Para crear una vista propia, conformas el protocolo `View`:

```swift
struct MiVista: View {
    // 'body' es la única propiedad requerida
    var body: some View {
        // Aquí describes tu interfaz
        Text("¡Hola desde mi vista!")
    }
}
```

### some View — ¿Qué significa?

`some View` significa "algún tipo que conforma View". SwiftUI infiere el tipo exacto por ti, permitiéndote combinar vistas sin preocuparte por los tipos complejos.

```swift
struct MiVista: View {
    var body: some View {
        // SwiftUI infiere que esto es un VStack<TupleView<(Text, Text, Button<Text>)>>
        // Pero tú solo escribes:
        VStack {
            Text("Título")
            Text("Subtítulo")
            Button("Toca aquí") { }
        }
    }
}
```

---

## 4. Crear tu Primera App Completa

Abre Xcode, crea un nuevo proyecto y reemplaza `ContentView.swift` con esto:

```swift
import SwiftUI

struct ContentView: View {
    // @State — veremos esto en detalle en el Módulo 04
    @State private var contador = 0
    @State private var nombre = ""

    var body: some View {
        NavigationStack {
            VStack(spacing: 30) {

                // Sección de bienvenida
                VStack(spacing: 8) {
                    Image(systemName: "swift")
                        .font(.system(size: 60))
                        .foregroundStyle(.orange)

                    Text("Mi Primera App")
                        .font(.largeTitle)
                        .fontWeight(.bold)

                    Text("¡Estoy aprendiendo SwiftUI!")
                        .font(.subheadline)
                        .foregroundStyle(.secondary)
                }

                Divider()

                // Campo de nombre
                VStack(alignment: .leading, spacing: 8) {
                    Text("¿Cómo te llamas?")
                        .font(.headline)

                    TextField("Tu nombre", text: $nombre)
                        .textFieldStyle(.roundedBorder)
                        .padding(.horizontal)
                }

                // Saludo dinámico
                if !nombre.isEmpty {
                    Text("¡Hola, \(nombre)! 👋")
                        .font(.title2)
                        .foregroundStyle(.blue)
                        .transition(.scale)
                }

                Divider()

                // Contador
                VStack(spacing: 16) {
                    Text("Contador: \(contador)")
                        .font(.title)
                        .monospacedDigit()

                    HStack(spacing: 20) {
                        Button {
                            if contador > 0 { contador -= 1 }
                        } label: {
                            Label("Restar", systemImage: "minus.circle.fill")
                                .font(.title2)
                        }
                        .tint(.red)

                        Button {
                            contador += 1
                        } label: {
                            Label("Sumar", systemImage: "plus.circle.fill")
                                .font(.title2)
                        }
                        .tint(.green)
                    }

                    Button("Reiniciar") {
                        contador = 0
                        nombre = ""
                    }
                    .buttonStyle(.bordered)
                }
            }
            .padding()
            .navigationTitle("SwiftUI desde Cero")
            .animation(.spring(), value: nombre)
        }
    }
}

#Preview {
    ContentView()
}
```

---

## 5. Cómo Crear un Proyecto en Xcode

1. Abre **Xcode**
2. **File → New → Project** (o ⌘ + ⇧ + N)
3. Selecciona **iOS → App**
4. Configura:
   - **Product Name:** `MiPrimeraApp`
   - **Team:** Tu Apple ID
   - **Organization Identifier:** `com.tunombre`
   - **Interface:** SwiftUI ✅
   - **Language:** Swift ✅
5. Elige dónde guardar y haz click en **Create**
6. Reemplaza el contenido de `ContentView.swift` con el código de arriba
7. Presiona **⌘ + R** para ejecutar en el simulador

---

## 6. Comparación Rápida UIKit vs SwiftUI

| Aspecto | UIKit | SwiftUI |
|---|---|---|
| Paradigma | Imperativo | Declarativo |
| Diseño | Storyboard o código | Solo código |
| Estado | Manual | Automático con @State |
| Preview | Simulador | En tiempo real en Xcode |
| Plataformas | Principalmente iOS | Todas las plataformas Apple |
| Año | 2008 | 2019 |
| Recomendado para | Apps legacy | Apps nuevas ✅ |

> 📝 **Nota:** No necesitas aprender UIKit para este curso. SwiftUI es el presente y futuro del desarrollo Apple.

---

## ✅ Resumen

- SwiftUI es **declarativo**: describes qué mostrar, no cómo construirlo
- Toda vista conforma el protocolo `View` y tiene una propiedad `body`
- `@main` marca el punto de entrada de la app
- `WindowGroup` es el contenedor principal de la app en iOS
- `#Preview` muestra una previsualización en Xcode sin ejecutar la app
- SwiftUI actualiza la UI **automáticamente** cuando cambia el estado

---

⬅️ [Módulo 02 — Manejo de Errores](../modulo-02-swift-intermedio/04-manejo-de-errores.md) | ➡️ [02 — Vistas y Modificadores](./02-vistas-y-modificadores.md)

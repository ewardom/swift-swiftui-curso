# 📐 Stacks y Layout en SwiftUI

## 🎯 Lo que aprenderás

- VStack, HStack y ZStack
- Spacer y Divider
- Alignment y spacing
- LazyVStack y LazyHStack
- LazyVGrid y Grid
- Proyecto: Pantalla de Login

---

## 1. VStack — Apilar Verticalmente

```swift
struct EjemploVStack: View {
    var body: some View {
        VStack {
            Text("Primero")
            Text("Segundo")
            Text("Tercero")
        }

        // Con spacing y alignment
        VStack(alignment: .leading, spacing: 12) {
            Text("Título")
                .font(.title)
                .fontWeight(.bold)
            Text("Subtítulo")
                .font(.subheadline)
                .foregroundStyle(.secondary)
            Text("Descripción más larga que ocupa varias líneas")
                .font(.body)
        }
        .padding()
    }
}
```

---

## 2. HStack — Apilar Horizontalmente

```swift
struct EjemploHStack: View {
    var body: some View {
        // Básico
        HStack {
            Image(systemName: "star.fill").foregroundStyle(.yellow)
            Text("4.8")
            Text("(2.3k reseñas)").foregroundStyle(.secondary)
        }

        // Con alignment vertical
        HStack(alignment: .top, spacing: 16) {
            Image(systemName: "person.circle.fill")
                .font(.largeTitle)
                .foregroundStyle(.blue)

            VStack(alignment: .leading) {
                Text("Carlos Mendoza").fontWeight(.semibold)
                Text("Hace 2 horas").font(.caption).foregroundStyle(.secondary)
                Text("¡Excelente curso de Swift!")
            }
        }
        .padding()
    }
}
```

---

## 3. ZStack — Apilar en Profundidad (Z)

```swift
struct EjemploZStack: View {
    var body: some View {
        // Superponer vistas
        ZStack {
            // Capa inferior
            RoundedRectangle(cornerRadius: 16)
                .fill(
                    LinearGradient(colors: [.blue, .purple],
                                 startPoint: .topLeading,
                                 endPoint: .bottomTrailing)
                )
                .frame(width: 300, height: 180)

            // Capa media
            VStack(alignment: .leading) {
                Text("Mi Tarjeta")
                    .font(.title2)
                    .fontWeight(.bold)
                    .foregroundStyle(.white)
                Spacer()
                HStack {
                    Text("**** **** **** 1234")
                        .foregroundStyle(.white.opacity(0.8))
                    Spacer()
                    Image(systemName: "creditcard.fill")
                        .foregroundStyle(.white)
                }
            }
            .padding(20)
            .frame(width: 300, height: 180)
        }

        // ZStack con alignment
        ZStack(alignment: .bottomTrailing) {
            Image(systemName: "photo")
                .resizable()
                .frame(width: 100, height: 100)
                .foregroundStyle(.gray.opacity(0.3))

            // Badge encima
            Circle()
                .fill(.green)
                .frame(width: 24, height: 24)
                .overlay(
                    Image(systemName: "plus")
                        .font(.caption)
                        .foregroundStyle(.white)
                )
        }
    }
}
```

---

## 4. Spacer y Divider

```swift
struct EjemploSpacerDivider: View {
    var body: some View {
        VStack {
            // Spacer empuja el contenido al máximo
            HStack {
                Text("Título")
                    .font(.headline)
                Spacer()  // empuja el botón a la derecha
                Button("Ver todo") { }
            }
            .padding()

            Divider()  // línea separadora

            // Spacer con tamaño fijo
            Text("Arriba")
            Spacer().frame(height: 40)  // 40pt de espacio fijo
            Text("Abajo")

            Divider()

            // Spacer para centrar
            VStack {
                Spacer()
                Text("Centrado verticalmente")
                Spacer()
            }
            .frame(height: 120)
            .background(.gray.opacity(0.1))
        }
    }
}
```

---

## 5. LazyVStack y LazyHStack

Para listas largas, usa Lazy stacks que solo renderizan los elementos visibles:

```swift
struct EjemploLazyStack: View {
    var body: some View {
        ScrollView {
            LazyVStack(spacing: 12) {
                ForEach(1...100, id: \.self) { numero in
                    HStack {
                        Image(systemName: "\(numero % 10 + 1).circle.fill")
                            .foregroundStyle(.blue)
                        Text("Elemento \(numero)")
                        Spacer()
                        Image(systemName: "chevron.right")
                            .foregroundStyle(.secondary)
                    }
                    .padding()
                    .background(.gray.opacity(0.05))
                    .clipShape(RoundedRectangle(cornerRadius: 10))
                }
            }
            .padding()
        }
    }
}
```

---

## 6. LazyVGrid — Cuadrículas

```swift
struct EjemploGrid: View {
    // Definir columnas
    let columnasFlexibles = [
        GridItem(.flexible()),
        GridItem(.flexible()),
        GridItem(.flexible())
    ]

    let columnasFijas = [
        GridItem(.fixed(100)),
        GridItem(.fixed(100)),
        GridItem(.fixed(100))
    ]

    let coloresApp = ["red", "orange", "yellow", "green", "blue", "purple",
                      "pink", "mint", "cyan", "indigo", "teal", "brown"]

    var body: some View {
        ScrollView {
            LazyVGrid(columns: columnasFlexibles, spacing: 16) {
                ForEach(coloresApp, id: \.self) { color in
                    RoundedRectangle(cornerRadius: 12)
                        .fill(colorDesdeNombre(color))
                        .frame(height: 80)
                        .overlay(
                            Text(color.capitalized)
                                .font(.caption)
                                .fontWeight(.semibold)
                                .foregroundStyle(.white)
                        )
                }
            }
            .padding()
        }
    }

    func colorDesdeNombre(_ nombre: String) -> Color {
        switch nombre {
        case "red":    return .red
        case "orange": return .orange
        case "yellow": return .yellow
        case "green":  return .green
        case "blue":   return .blue
        case "purple": return .purple
        case "pink":   return .pink
        case "mint":   return .mint
        case "cyan":   return .cyan
        case "indigo": return .indigo
        case "teal":   return .teal
        default:       return .brown
        }
    }
}
```

---

## 7. 🛠️ Proyecto: Pantalla de Login

Crea `LoginView.swift` en tu proyecto:

```swift
import SwiftUI

struct LoginView: View {
    @State private var email = ""
    @State private var contrasena = ""
    @State private var recordarme = false

    var body: some View {
        ZStack {
            // Fondo con gradiente
            LinearGradient(
                colors: [.blue.opacity(0.8), .purple.opacity(0.6)],
                startPoint: .topLeading,
                endPoint: .bottomTrailing
            )
            .ignoresSafeArea()

            VStack(spacing: 0) {
                Spacer()

                // Logo y título
                VStack(spacing: 12) {
                    Image(systemName: "swift")
                        .font(.system(size: 60))
                        .foregroundStyle(.white)

                    Text("SwiftApp")
                        .font(.largeTitle)
                        .fontWeight(.bold)
                        .foregroundStyle(.white)

                    Text("Inicia sesión para continuar")
                        .font(.subheadline)
                        .foregroundStyle(.white.opacity(0.8))
                }

                Spacer().frame(height: 50)

                // Tarjeta de login
                VStack(spacing: 20) {
                    // Campo Email
                    VStack(alignment: .leading, spacing: 6) {
                        Label("Email", systemImage: "envelope")
                            .font(.caption)
                            .foregroundStyle(.secondary)

                        TextField("tu@email.com", text: $email)
                            .keyboardType(.emailAddress)
                            .autocapitalization(.none)
                            .padding()
                            .background(.gray.opacity(0.1))
                            .clipShape(RoundedRectangle(cornerRadius: 10))
                    }

                    // Campo Contraseña
                    VStack(alignment: .leading, spacing: 6) {
                        Label("Contraseña", systemImage: "lock")
                            .font(.caption)
                            .foregroundStyle(.secondary)

                        SecureField("••••••••", text: $contrasena)
                            .padding()
                            .background(.gray.opacity(0.1))
                            .clipShape(RoundedRectangle(cornerRadius: 10))
                    }

                    // Recordarme y olvidé contraseña
                    HStack {
                        Toggle("Recordarme", isOn: $recordarme)
                            .font(.caption)
                        Spacer()
                        Button("¿Olvidaste tu contraseña?") { }
                            .font(.caption)
                            .foregroundStyle(.blue)
                    }

                    // Botón de login
                    Button {
                        print("Login: \(email)")
                    } label: {
                        HStack {
                            Image(systemName: "arrow.right.circle.fill")
                            Text("Iniciar Sesión")
                                .fontWeight(.semibold)
                        }
                        .frame(maxWidth: .infinity)
                        .padding()
                        .background(.blue)
                        .foregroundStyle(.white)
                        .clipShape(RoundedRectangle(cornerRadius: 12))
                    }
                    .disabled(email.isEmpty || contrasena.isEmpty)
                    .opacity(email.isEmpty || contrasena.isEmpty ? 0.6 : 1)

                    // Separador
                    HStack {
                        Rectangle().frame(height: 1).foregroundStyle(.gray.opacity(0.3))
                        Text("o").foregroundStyle(.secondary).font(.caption)
                        Rectangle().frame(height: 1).foregroundStyle(.gray.opacity(0.3))
                    }

                    // Botón de registro
                    HStack {
                        Text("¿No tienes cuenta?")
                            .font(.subheadline)
                            .foregroundStyle(.secondary)
                        Button("Regístrate") { }
                            .font(.subheadline)
                            .fontWeight(.semibold)
                    }
                }
                .padding(28)
                .background(.ultraThinMaterial)
                .clipShape(RoundedRectangle(cornerRadius: 24))
                .padding(.horizontal)

                Spacer()
            }
        }
    }
}

#Preview {
    LoginView()
}
```

---

## ✅ Resumen

| Stack | Dirección | Cuándo usar |
|---|---|---|
| `VStack` | Vertical ↕️ | Listas, formularios, páginas |
| `HStack` | Horizontal ↔️ | Barras, tarjetas, filas |
| `ZStack` | Profundidad ↙️↗️ | Overlays, badges, fondos |
| `LazyVStack` | Vertical 🔄 | Listas largas con scroll |
| `LazyVGrid` | Cuadrícula 🔲 | Galerías, menús de iconos |

---

⬅️ [02 — Vistas y Modificadores](./02-vistas-y-modificadores.md) | ➡️ [04 — Listas y Grids](./04-listas-y-grids.md)

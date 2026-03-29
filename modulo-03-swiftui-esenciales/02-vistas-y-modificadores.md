# 🖼️ Vistas y Modificadores en SwiftUI

## 🎯 Lo que aprenderás

- Vistas fundamentales: Text, Image, Button, Label
- Cómo funcionan los modificadores
- Por qué el orden de los modificadores importa
- SF Symbols
- Mini proyecto: Tarjeta de Perfil

---

## 1. Text — Mostrar Texto

```swift
import SwiftUI

struct EjemplosText: View {
    var body: some View {
        VStack(spacing: 12) {
            // Básico
            Text("Hola, SwiftUI")

            // Estilo de fuente
            Text("Título Grande").font(.largeTitle)
            Text("Título").font(.title)
            Text("Título 2").font(.title2)
            Text("Titular").font(.headline)
            Text("Cuerpo").font(.body)
            Text("Pie de página").font(.caption)

            // Peso y estilo
            Text("Negrita").fontWeight(.bold)
            Text("Itálica").italic()
            Text("Subrayado").underline()
            Text("Tachado").strikethrough()

            // Color
            Text("Azul").foregroundStyle(.blue)
            Text("Gradiente").foregroundStyle(
                LinearGradient(colors: [.purple, .blue],
                             startPoint: .leading,
                             endPoint: .trailing)
            )

            // Multilinea y alineación
            Text("Este es un texto largo que ocupa varias líneas en la pantalla")
                .multilineTextAlignment(.center)
                .lineLimit(2)

            // Interpolación
            let precio = 99.99
            Text("Precio: \(precio, format: .currency(code: "MXN"))")
        }
        .padding()
    }
}
```

---

## 2. Image — Mostrar Imágenes

```swift
struct EjemplosImage: View {
    var body: some View {
        VStack(spacing: 20) {
            // SF Symbol (iconos del sistema)
            Image(systemName: "heart.fill")
                .font(.largeTitle)
                .foregroundStyle(.red)

            // Imagen del proyecto (Assets.xcassets)
            // Image("nombre-imagen")
            //     .resizable()
            //     .scaledToFit()

            // Imagen redimensionable
            Image(systemName: "photo")
                .resizable()
                .scaledToFit()
                .frame(width: 100, height: 100)
                .foregroundStyle(.gray)

            // Imagen circular (perfil)
            Image(systemName: "person.circle.fill")
                .resizable()
                .scaledToFill()
                .frame(width: 80, height: 80)
                .clipShape(Circle())
                .foregroundStyle(.blue)

            // Con overlay
            Image(systemName: "star.fill")
                .resizable()
                .frame(width: 60, height: 60)
                .foregroundStyle(.yellow)
                .shadow(color: .orange, radius: 5)
        }
    }
}
```

---

## 3. Button — Botones Interactivos

```swift
struct EjemplosButton: View {
    @State private var contador = 0

    var body: some View {
        VStack(spacing: 16) {
            // Botón básico
            Button("Toca aquí") {
                contador += 1
            }

            // Con Label (icono + texto)
            Button {
                print("Guardando...")
            } label: {
                Label("Guardar", systemImage: "square.and.arrow.down")
            }

            // Estilos de botón
            Button("Filled") { }
                .buttonStyle(.borderedProminent)

            Button("Bordered") { }
                .buttonStyle(.bordered)

            Button("Plain") { }
                .buttonStyle(.plain)

            // Botón con color personalizado
            Button {
                contador = 0
            } label: {
                Text("Reiniciar")
                    .frame(maxWidth: .infinity)
                    .padding()
                    .background(.red)
                    .foregroundStyle(.white)
                    .clipShape(RoundedRectangle(cornerRadius: 12))
            }
            .padding(.horizontal)

            Text("Contador: \(contador)")
                .font(.headline)
        }
        .padding()
    }
}
```

---

## 4. Label — Icono + Texto

```swift
struct EjemplosLabel: View {
    var body: some View {
        VStack(spacing: 12) {
            Label("Favorito", systemImage: "heart.fill")
            Label("Compartir", systemImage: "square.and.arrow.up")
            Label("Configuración", systemImage: "gear")
            Label("Inicio", systemImage: "house.fill")
            Label("Buscar", systemImage: "magnifyingglass")

            // Solo icono
            Label("Eliminar", systemImage: "trash")
                .labelStyle(.iconOnly)
                .foregroundStyle(.red)

            // Solo texto
            Label("Perfil", systemImage: "person")
                .labelStyle(.titleOnly)
        }
    }
}
```

---

## 5. Modificadores y su Orden

⚠️ **El orden de los modificadores ES importante.** Cada modificador crea una nueva vista:

```swift
struct OrdenModificadores: View {
    var body: some View {
        VStack(spacing: 30) {
            // ✅ Padding ANTES del fondo — el fondo cubre el padding
            Text("Padding → Fondo")
                .padding()
                .background(.blue)
                .foregroundStyle(.white)

            // 🔄 Fondo ANTES del padding — padding queda fuera del fondo
            Text("Fondo → Padding")
                .background(.blue)
                .foregroundStyle(.white)
                .padding()

            // ✅ Correcto: padding, fondo, cornerRadius
            Text("Botón personalizado")
                .padding(.horizontal, 20)
                .padding(.vertical, 10)
                .background(.purple)
                .foregroundStyle(.white)
                .clipShape(Capsule())
        }
    }
}
```

---

## 6. Modificadores Más Usados

```swift
struct ModificadoresComunes: View {
    var body: some View {
        VStack(spacing: 16) {
            Text("Ejemplo")
                // Tipografía
                .font(.title)
                .fontWeight(.semibold)
                .italic()

                // Color
                .foregroundStyle(.primary)

                // Tamaño y posición
                .frame(width: 200, height: 50)
                .frame(maxWidth: .infinity)  // ancho máximo

                // Fondo y bordes
                .background(.blue.opacity(0.1))
                .border(.blue, width: 1)
                .clipShape(RoundedRectangle(cornerRadius: 10))

                // Espaciado
                .padding()
                .padding(.horizontal, 20)

                // Sombra
                .shadow(color: .black.opacity(0.2), radius: 4, x: 0, y: 2)

                // Visibilidad
                .opacity(0.8)
                // .hidden()
        }
    }
}
```

---

## 7. SF Symbols

Apple ofrece más de 5000 iconos gratuitos llamados SF Symbols:

```swift
struct SFSymbolsEjemplos: View {
    var body: some View {
        LazyVGrid(columns: Array(repeating: GridItem(), count: 4), spacing: 20) {
            ForEach([
                "house", "person", "gear", "heart",
                "star", "bell", "trash", "plus",
                "pencil", "magnifyingglass", "camera", "music.note"
            ], id: \.self) { simbolo in
                VStack(spacing: 4) {
                    Image(systemName: simbolo)
                        .font(.title2)
                    Text(simbolo)
                        .font(.caption2)
                        .lineLimit(1)
                }
            }
        }
        .padding()
    }
}
```

> 💡 **Tip:** Descarga la app **SF Symbols** (gratis en Mac App Store) para explorar todos los iconos disponibles. Puedes buscar por nombre, categoría o palabra clave.

---

## 8. 🛠️ Mini Proyecto: Tarjeta de Perfil

Crea un nuevo archivo `PerfilView.swift` en tu proyecto de Xcode:

```swift
import SwiftUI

struct TarjetaPerfil: View {
    // Datos del perfil
    var nombre: String
    var profesion: String
    var ciudad: String
    var seguidores: Int
    var siguiendo: Int

    var body: some View {
        VStack(spacing: 0) {
            // Header con gradiente
            ZStack {
                LinearGradient(
                    colors: [.blue, .purple],
                    startPoint: .topLeading,
                    endPoint: .bottomTrailing
                )
                .frame(height: 120)

                // Avatar
                Image(systemName: "person.circle.fill")
                    .resizable()
                    .frame(width: 90, height: 90)
                    .foregroundStyle(.white)
                    .background(Circle().fill(.white.opacity(0.2)))
                    .offset(y: 45)
            }

            // Espacio para el avatar que sobresale
            Spacer().frame(height: 50)

            // Información del perfil
            VStack(spacing: 6) {
                Text(nombre)
                    .font(.title2)
                    .fontWeight(.bold)

                Text(profesion)
                    .font(.subheadline)
                    .foregroundStyle(.secondary)

                Label(ciudad, systemImage: "mappin.circle.fill")
                    .font(.caption)
                    .foregroundStyle(.secondary)
            }
            .padding(.top, 4)

            Divider()
                .padding(.vertical, 16)

            // Estadísticas
            HStack(spacing: 40) {
                StatView(valor: seguidores, etiqueta: "Seguidores")
                StatView(valor: siguiendo, etiqueta: "Siguiendo")
            }

            // Botones de acción
            HStack(spacing: 12) {
                Button {
                } label: {
                    Text("Seguir")
                        .fontWeight(.semibold)
                        .frame(maxWidth: .infinity)
                        .padding(.vertical, 10)
                        .background(.blue)
                        .foregroundStyle(.white)
                        .clipShape(RoundedRectangle(cornerRadius: 10))
                }

                Button {
                } label: {
                    Text("Mensaje")
                        .fontWeight(.semibold)
                        .frame(maxWidth: .infinity)
                        .padding(.vertical, 10)
                        .background(.gray.opacity(0.15))
                        .foregroundStyle(.primary)
                        .clipShape(RoundedRectangle(cornerRadius: 10))
                }
            }
            .padding(.horizontal)
            .padding(.top, 16)
            .padding(.bottom, 24)
        }
        .background(Color(.systemBackground))
        .clipShape(RoundedRectangle(cornerRadius: 20))
        .shadow(color: .black.opacity(0.1), radius: 10, x: 0, y: 4)
        .padding()
    }
}

// Vista auxiliar para estadísticas
struct StatView: View {
    var valor: Int
    var etiqueta: String

    var body: some View {
        VStack(spacing: 4) {
            Text("\(valor)")
                .font(.title3)
                .fontWeight(.bold)
            Text(etiqueta)
                .font(.caption)
                .foregroundStyle(.secondary)
        }
    }
}

#Preview {
    TarjetaPerfil(
        nombre: "Ana García",
        profesion: "Desarrolladora iOS",
        ciudad: "Ciudad de México",
        seguidores: 1240,
        siguiendo: 380
    )
    .background(Color(.systemGroupedBackground))
}
```

---

## ✅ Resumen

| Vista | Uso |
|---|---|
| `Text` | Mostrar texto con estilos |
| `Image` | Imágenes y SF Symbols |
| `Button` | Acciones del usuario |
| `Label` | Icono + texto juntos |

**Reglas de modificadores:**
- El orden **sí importa** — cada uno crea una nueva capa
- `padding()` antes de `background()` incluye el padding en el fondo
- `clipShape()` recorta todo lo que está antes

---

⬅️ [01 — Introducción a SwiftUI](./01-introduccion-swiftui.md) | ➡️ [03 — Stacks y Layout](./03-stacks-layout.md)

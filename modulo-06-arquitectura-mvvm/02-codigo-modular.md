# 🧩 Código Modular en SwiftUI

## 🎯 Lo que aprenderás

- Crear vistas reutilizables
- ViewModifiers personalizados
- Extensions para limpiar código
- Componentes de un sistema de diseño
- Buenas prácticas de organización

---

## 1. Vistas Reutilizables

En lugar de repetir el mismo código en varias vistas, extráelo en componentes:

```swift
import SwiftUI

// ❌ Código repetido
struct VistaMala: View {
    var body: some View {
        VStack {
            // Botón azul repetido en 5 vistas distintas
            Button("Guardar") { }
                .frame(maxWidth: .infinity)
                .padding()
                .background(.blue)
                .foregroundStyle(.white)
                .clipShape(RoundedRectangle(cornerRadius: 12))
                .padding(.horizontal)
        }
    }
}

// ✅ Componente reutilizable
struct BotonPrincipal: View {
    let titulo: String
    let icono: String?
    let color: Color
    let accion: () -> Void

    init(_ titulo: String,
         icono: String? = nil,
         color: Color = .blue,
         accion: @escaping () -> Void) {
        self.titulo = titulo
        self.icono = icono
        self.color = color
        self.accion = accion
    }

    var body: some View {
        Button(action: accion) {
            HStack(spacing: 8) {
                if let icono {
                    Image(systemName: icono)
                }
                Text(titulo)
                    .fontWeight(.semibold)
            }
            .frame(maxWidth: .infinity)
            .padding()
            .background(color)
            .foregroundStyle(.white)
            .clipShape(RoundedRectangle(cornerRadius: 12))
        }
        .padding(.horizontal)
    }
}

// Uso:
struct VistaConComponente: View {
    var body: some View {
        VStack(spacing: 12) {
            BotonPrincipal("Guardar", icono: "square.and.arrow.down") {
                print("Guardando...")
            }
            BotonPrincipal("Eliminar", icono: "trash", color: .red) {
                print("Eliminando...")
            }
            BotonPrincipal("Compartir", icono: "square.and.arrow.up",
                           color: .green) {
                print("Compartiendo...")
            }
        }
    }
}
```

---

## 2. Sistema de Componentes Completo

```swift
// MARK: - Tarjeta base reutilizable
struct Tarjeta<Contenido: View>: View {
    let contenido: () -> Contenido

    init(@ViewBuilder contenido: @escaping () -> Contenido) {
        self.contenido = contenido
    }

    var body: some View {
        contenido()
            .padding()
            .background(.background)
            .clipShape(RoundedRectangle(cornerRadius: 16))
            .shadow(color: .black.opacity(0.08), radius: 8, x: 0, y: 2)
    }
}

// MARK: - Badge / Etiqueta
struct Badge: View {
    let texto: String
    let color: Color

    var body: some View {
        Text(texto)
            .font(.caption2.bold())
            .padding(.horizontal, 8)
            .padding(.vertical, 4)
            .background(color.opacity(0.15))
            .foregroundStyle(color)
            .clipShape(Capsule())
    }
}

// MARK: - Avatar
struct Avatar: View {
    let iniciales: String
    let tamaño: CGFloat
    let color: Color

    init(_ iniciales: String, tamaño: CGFloat = 44, color: Color = .blue) {
        self.iniciales = String(iniciales.prefix(2)).uppercased()
        self.tamaño = tamaño
        self.color = color
    }

    var body: some View {
        Circle()
            .fill(color.gradient)
            .frame(width: tamaño, height: tamaño)
            .overlay(
                Text(iniciales)
                    .font(.system(size: tamaño * 0.38, weight: .semibold))
                    .foregroundStyle(.white)
            )
    }
}

// MARK: - Separador con texto
struct SeparadorConTexto: View {
    let texto: String

    var body: some View {
        HStack(spacing: 12) {
            Rectangle()
                .frame(height: 1)
                .foregroundStyle(.gray.opacity(0.3))
            Text(texto)
                .font(.caption)
                .foregroundStyle(.secondary)
                .fixedSize()
            Rectangle()
                .frame(height: 1)
                .foregroundStyle(.gray.opacity(0.3))
        }
    }
}

// MARK: - Fila de información
struct FilaInfo: View {
    let etiqueta: String
    let valor: String
    let icono: String?

    init(_ etiqueta: String, valor: String, icono: String? = nil) {
        self.etiqueta = etiqueta
        self.valor = valor
        self.icono = icono
    }

    var body: some View {
        HStack {
            if let icono {
                Image(systemName: icono)
                    .foregroundStyle(.secondary)
                    .frame(width: 20)
            }
            Text(etiqueta)
                .foregroundStyle(.secondary)
            Spacer()
            Text(valor)
                .fontWeight(.medium)
        }
        .padding(.vertical, 4)
    }
}

// MARK: - Vista de uso de todos los componentes
struct ShowroomComponentes: View {
    var body: some View {
        ScrollView {
            VStack(spacing: 20) {
                // Tarjeta con avatar y badges
                Tarjeta {
                    HStack(spacing: 14) {
                        Avatar("Ana García", tamaño: 56, color: .purple)

                        VStack(alignment: .leading, spacing: 6) {
                            Text("Ana García")
                                .fontWeight(.semibold)
                            HStack(spacing: 6) {
                                Badge(texto: "Admin", color: .blue)
                                Badge(texto: "Pro", color: .orange)
                            }
                        }
                        Spacer()
                    }
                }

                // Tarjeta de información
                Tarjeta {
                    VStack(spacing: 0) {
                        FilaInfo("Versión", valor: "2.1.0", icono: "info.circle")
                        Divider()
                        FilaInfo("Dispositivo", valor: "iPhone 17 Pro", icono: "iphone")
                        Divider()
                        FilaInfo("Almacenamiento", valor: "12.4 MB", icono: "externaldrive")
                    }
                }

                // Separador
                SeparadorConTexto(texto: "o continúa con")

                // Botones
                BotonPrincipal("Iniciar sesión", icono: "person.fill") { }
                BotonPrincipal("Crear cuenta", icono: "person.badge.plus",
                               color: .green) { }
            }
            .padding()
        }
        .navigationTitle("Componentes")
    }
}
```

---

## 3. ViewModifiers Personalizados

```swift
// MARK: - Modificador: estilo de tarjeta
struct EstiloTarjeta: ViewModifier {
    var color: Color = .white
    var radio: CGFloat = 16
    var sombra: CGFloat = 8

    func body(content: Content) -> some View {
        content
            .padding()
            .background(color)
            .clipShape(RoundedRectangle(cornerRadius: radio))
            .shadow(color: .black.opacity(0.08),
                    radius: sombra, x: 0, y: 2)
    }
}

// MARK: - Modificador: shimmer (cargando)
struct Shimmer: ViewModifier {
    @State private var fase: CGFloat = 0

    func body(content: Content) -> some View {
        content
            .overlay(
                LinearGradient(
                    colors: [.clear, .white.opacity(0.6), .clear],
                    startPoint: .leading,
                    endPoint: .trailing
                )
                .offset(x: fase * 400 - 200)
                .animation(.linear(duration: 1.2).repeatForever(autoreverses: false),
                           value: fase)
            )
            .onAppear { fase = 1 }
            .clipped()
    }
}

// MARK: - Modificador: borde condicional
struct BordeCondicional: ViewModifier {
    let condicion: Bool
    let color: Color
    let ancho: CGFloat

    func body(content: Content) -> some View {
        content.overlay(
            RoundedRectangle(cornerRadius: 10)
                .stroke(condicion ? color : .clear, lineWidth: ancho)
        )
    }
}

// MARK: - Extensiones para uso fluido
extension View {
    func estiloTarjeta(color: Color = .white,
                       radio: CGFloat = 16) -> some View {
        modifier(EstiloTarjeta(color: color, radio: radio))
    }

    func shimmer() -> some View {
        modifier(Shimmer())
    }

    func bordeCondicional(_ condicion: Bool,
                          color: Color = .blue,
                          ancho: CGFloat = 2) -> some View {
        modifier(BordeCondicional(condicion: condicion,
                                  color: color, ancho: ancho))
    }

    // Ocultar condicionalmente
    @ViewBuilder
    func visible(_ condicion: Bool) -> some View {
        if condicion { self } else { EmptyView() }
    }
}

// Uso de los modificadores
struct UsoModificadores: View {
    @State private var seleccionado = false
    @State private var cargando = true

    var body: some View {
        VStack(spacing: 20) {
            // Tarjeta con modificador propio
            Text("Contenido en tarjeta")
                .estiloTarjeta()

            // Shimmer de carga
            if cargando {
                RoundedRectangle(cornerRadius: 12)
                    .fill(.gray.opacity(0.3))
                    .frame(height: 60)
                    .shimmer()
            }

            // Borde condicional (resaltado si seleccionado)
            Text("Tócame")
                .padding()
                .background(.blue.opacity(0.08))
                .clipShape(RoundedRectangle(cornerRadius: 10))
                .bordeCondicional(seleccionado, color: .blue)
                .onTapGesture { seleccionado.toggle() }

            // Vista condicional
            Text("¡Estás seleccionado! ✅")
                .visible(seleccionado)

            Button(cargando ? "Ocultar shimmer" : "Mostrar shimmer") {
                cargando.toggle()
            }
            .buttonStyle(.bordered)
        }
        .padding()
    }
}
```

---

## 4. Extensions Útiles

```swift
// MARK: - Extension de Color
extension Color {
    // Color desde hex
    init(hex: String) {
        let hex = hex.trimmingCharacters(in: .alphanumerics.inverted)
        var int: UInt64 = 0
        Scanner(string: hex).scanHexInt64(&int)
        let r = Double((int >> 16) & 0xFF) / 255
        let g = Double((int >> 8) & 0xFF) / 255
        let b = Double(int & 0xFF) / 255
        self.init(red: r, green: g, blue: b)
    }

    static let appAzul = Color(hex: "007AFF")
    static let appVerde = Color(hex: "34C759")
    static let appRojo = Color(hex: "FF3B30")
}

// MARK: - Extension de String
extension String {
    var esEmailValido: Bool {
        contains("@") && contains(".") && count > 5
    }

    var esContrasenaSegura: Bool {
        count >= 8
    }

    var iniciales: String {
        split(separator: " ")
            .prefix(2)
            .compactMap { $0.first }
            .map(String.init)
            .joined()
            .uppercased()
    }
}

// MARK: - Extension de Date
extension Date {
    var esHoy: Bool {
        Calendar.current.isDateInToday(self)
    }

    var esAyer: Bool {
        Calendar.current.isDateInYesterday(self)
    }

    var textoRelativo: String {
        if esHoy { return "Hoy" }
        if esAyer { return "Ayer" }
        let formatter = DateFormatter()
        formatter.dateStyle = .medium
        formatter.locale = Locale(identifier: "es_MX")
        return formatter.string(from: self)
    }
}

// MARK: - Extension de Array
extension Array {
    // Acceso seguro a índices
    subscript(safe index: Index) -> Element? {
        indices.contains(index) ? self[index] : nil
    }
}

// Uso de extensions
struct UsoExtensions: View {
    let email = "usuario@email.com"
    let nombre = "Ana García López"
    let fecha = Date()

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            // Avatar con iniciales
            Avatar(nombre.iniciales, color: .appAzul)

            // Email válido
            Label(
                email.esEmailValido ? "Email válido" : "Email inválido",
                systemImage: email.esEmailValido
                    ? "checkmark.circle.fill" : "xmark.circle.fill"
            )
            .foregroundStyle(email.esEmailValido ? .green : .red)

            // Fecha relativa
            Text("Fecha: \(fecha.textoRelativo)")

            // Color personalizado
            Circle()
                .fill(Color.appVerde)
                .frame(width: 40, height: 40)
        }
        .padding()
        .estiloTarjeta()
        .padding()
    }
}

#Preview {
    ScrollView {
        ShowroomComponentes()
        UsoModificadores()
        UsoExtensions()
    }
}
```

---

## ✅ Resumen — Código Modular

| Técnica | Beneficio |
|---|---|
| Vistas reutilizables | Menos repetición, consistencia visual |
| ViewModifier | Encapsular estilos complejos |
| Extensions | Código más limpio y expresivo |
| Sistema de diseño | Identidad visual consistente |

**Regla de oro:** Si copias y pegas código de vista más de dos veces, conviértelo en un componente.

---

⬅️ [01 — Patrón MVVM](./01-patron-mvvm.md) | ➡️ [Módulo 07 — Animaciones](../modulo-07-avanzado/01-animaciones.md)

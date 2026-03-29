# 🌍 @EnvironmentObject en SwiftUI

## 🎯 Lo que aprenderás

- Qué es el Environment en SwiftUI
- `@EnvironmentObject` para estado global
- `@Environment` para valores del sistema
- Ejemplo: tema global de la app
- Proyecto: Carrito de compras

---

## 1. ¿Por qué @EnvironmentObject?

Cuando tienes datos que muchas vistas necesitan (usuario autenticado, tema, carrito de compras), pasar `@Binding` por cada nivel se vuelve tedioso. `@EnvironmentObject` lo inyecta globalmente:

```
Sin @EnvironmentObject — "prop drilling":
VistaPrincipal(usuario: usuario)
  └─ VistaPerfil(usuario: usuario)
       └─ VistaConfig(usuario: usuario)
            └─ VistaAvatar(usuario: usuario)   ← lo necesita aquí

Con @EnvironmentObject:
VistaPrincipal
  └─ VistaPerfil
       └─ VistaConfig
            └─ VistaAvatar   ← accede directamente
```

---

## 2. Configurar @EnvironmentObject

```swift
import SwiftUI

// 1. Crear el ObservableObject
class SesionUsuario: ObservableObject {
    @Published var nombre = ""
    @Published var email = ""
    @Published var estaAutenticado = false
    @Published var fotoPerfil: String = "person.circle.fill"

    func iniciarSesion(nombre: String, email: String) {
        self.nombre = nombre
        self.email = email
        self.estaAutenticado = true
    }

    func cerrarSesion() {
        nombre = ""
        email = ""
        estaAutenticado = false
    }
}

// 2. Inyectarlo en el punto de entrada
@main
struct MiApp: App {
    @StateObject private var sesion = SesionUsuario()

    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(sesion)   // ← inyectar
        }
    }
}

// 3. Usarlo en cualquier vista hija
struct PerfilView: View {
    @EnvironmentObject var sesion: SesionUsuario  // ← acceder

    var body: some View {
        VStack {
            Image(systemName: sesion.fotoPerfil)
                .font(.system(size: 60))
            Text(sesion.nombre)
                .font(.title)
            Text(sesion.email)
                .foregroundStyle(.secondary)
            Button("Cerrar Sesión") {
                sesion.cerrarSesion()
            }
            .buttonStyle(.bordered)
            .tint(.red)
        }
    }
}
```

---

## 3. @Environment — Valores del Sistema

`@Environment` da acceso a valores del entorno de SwiftUI:

```swift
struct VistaAdaptativa: View {
    // Valores del sistema
    @Environment(\.colorScheme) var colorScheme
    @Environment(\.locale) var locale
    @Environment(\.dynamicTypeSize) var tamañoTexto
    @Environment(\.horizontalSizeClass) var sizeClass
    @Environment(\.dismiss) var dismiss
    @Environment(\.openURL) var openURL

    var body: some View {
        VStack(spacing: 16) {
            // Adaptar según el tema
            Text("Tema actual")
                .foregroundStyle(colorScheme == .dark ? .white : .black)
                .padding()
                .background(colorScheme == .dark ? .gray.opacity(0.3) : .gray.opacity(0.1))
                .clipShape(RoundedRectangle(cornerRadius: 10))

            // Detectar iPad vs iPhone
            Text(sizeClass == .regular ? "Pantalla grande (iPad)" : "Pantalla compacta (iPhone)")
                .font(.caption)

            // Abrir URLs
            Button("Abrir Apple.com") {
                openURL(URL(string: "https://apple.com")!)
            }

            // Cerrar vista actual
            Button("Cerrar") { dismiss() }
        }
        .padding()
    }
}
```

---

## 4. Proyecto: Carrito de Compras

```swift
import SwiftUI

// MARK: - Modelos
struct ProductoTienda: Identifiable {
    let id = UUID()
    var nombre: String
    var precio: Double
    var icono: String
    var color: Color
}

struct ItemCarrito: Identifiable {
    let id = UUID()
    var producto: ProductoTienda
    var cantidad: Int

    var subtotal: Double { producto.precio * Double(cantidad) }
}

// MARK: - ViewModel Global (Carrito)
class CarritoViewModel: ObservableObject {
    @Published var items: [ItemCarrito] = []

    var totalItems: Int { items.reduce(0) { $0 + $1.cantidad } }

    var total: Double { items.reduce(0) { $0 + $1.subtotal } }

    var isEmpty: Bool { items.isEmpty }

    func agregar(_ producto: ProductoTienda) {
        if let index = items.firstIndex(where: { $0.producto.id == producto.id }) {
            items[index].cantidad += 1
        } else {
            items.append(ItemCarrito(producto: producto, cantidad: 1))
        }
    }

    func quitar(_ producto: ProductoTienda) {
        if let index = items.firstIndex(where: { $0.producto.id == producto.id }) {
            if items[index].cantidad > 1 {
                items[index].cantidad -= 1
            } else {
                items.remove(at: index)
            }
        }
    }

    func eliminar(_ item: ItemCarrito) {
        items.removeAll { $0.id == item.id }
    }

    func vaciar() { items.removeAll() }

    func cantidad(de producto: ProductoTienda) -> Int {
        items.first(where: { $0.producto.id == producto.id })?.cantidad ?? 0
    }
}

// MARK: - Catálogo de Productos
struct CatalogoView: View {
    @EnvironmentObject var carrito: CarritoViewModel
    @State private var mostrarCarrito = false

    let productos = [
        ProductoTienda(nombre: "iPhone 17", precio: 999.99, icono: "iphone", color: .blue),
        ProductoTienda(nombre: "MacBook Air", precio: 1299.99, icono: "laptopcomputer", color: .gray),
        ProductoTienda(nombre: "AirPods Pro", precio: 249.99, icono: "airpodspro", color: .white),
        ProductoTienda(nombre: "iPad Pro", precio: 799.99, icono: "ipad", color: .purple),
        ProductoTienda(nombre: "Apple Watch", precio: 399.99, icono: "applewatch", color: .red),
        ProductoTienda(nombre: "Apple TV", precio: 129.99, icono: "appletv", color: .black)
    ]

    let columnas = [GridItem(.flexible()), GridItem(.flexible())]

    var body: some View {
        NavigationStack {
            ScrollView {
                LazyVGrid(columns: columnas, spacing: 16) {
                    ForEach(productos) { producto in
                        TarjetaProducto(producto: producto)
                    }
                }
                .padding()
            }
            .navigationTitle("🛍️ Tienda Apple")
            .toolbar {
                ToolbarItem(placement: .topBarTrailing) {
                    Button {
                        mostrarCarrito = true
                    } label: {
                        ZStack(alignment: .topTrailing) {
                            Image(systemName: "cart")
                                .font(.title3)
                            if carrito.totalItems > 0 {
                                Text("\(carrito.totalItems)")
                                    .font(.caption2.bold())
                                    .foregroundStyle(.white)
                                    .padding(4)
                                    .background(.red)
                                    .clipShape(Circle())
                                    .offset(x: 8, y: -8)
                            }
                        }
                    }
                }
            }
            .sheet(isPresented: $mostrarCarrito) {
                CarritoView()
            }
        }
    }
}

// MARK: - Tarjeta de Producto
struct TarjetaProducto: View {
    let producto: ProductoTienda
    @EnvironmentObject var carrito: CarritoViewModel

    var cantidadEnCarrito: Int { carrito.cantidad(de: producto) }

    var body: some View {
        VStack(spacing: 12) {
            // Icono
            RoundedRectangle(cornerRadius: 16)
                .fill(producto.color.opacity(0.15))
                .frame(height: 100)
                .overlay(
                    Image(systemName: producto.icono)
                        .font(.system(size: 40))
                        .foregroundStyle(producto.color)
                )

            // Info
            VStack(spacing: 4) {
                Text(producto.nombre)
                    .font(.subheadline.bold())
                    .lineLimit(1)
                Text("$\(producto.precio, specifier: "%.2f")")
                    .font(.caption)
                    .foregroundStyle(.secondary)
            }

            // Controles
            if cantidadEnCarrito == 0 {
                Button("Agregar") {
                    withAnimation { carrito.agregar(producto) }
                }
                .buttonStyle(.borderedProminent)
                .tint(producto.color)
                .font(.caption)
            } else {
                HStack(spacing: 12) {
                    Button {
                        withAnimation { carrito.quitar(producto) }
                    } label: {
                        Image(systemName: "minus.circle.fill")
                            .foregroundStyle(.red)
                    }

                    Text("\(cantidadEnCarrito)")
                        .font(.headline)
                        .frame(minWidth: 24)

                    Button {
                        withAnimation { carrito.agregar(producto) }
                    } label: {
                        Image(systemName: "plus.circle.fill")
                            .foregroundStyle(.green)
                    }
                }
            }
        }
        .padding()
        .background(.gray.opacity(0.06))
        .clipShape(RoundedRectangle(cornerRadius: 16))
    }
}

// MARK: - Vista del Carrito
struct CarritoView: View {
    @EnvironmentObject var carrito: CarritoViewModel
    @Environment(\.dismiss) private var dismiss
    @State private var mostrarConfirmacion = false

    var body: some View {
        NavigationStack {
            Group {
                if carrito.isEmpty {
                    VStack(spacing: 16) {
                        Image(systemName: "cart.badge.minus")
                            .font(.system(size: 60))
                            .foregroundStyle(.secondary)
                        Text("Tu carrito está vacío")
                            .font(.title3)
                        Button("Seguir comprando") { dismiss() }
                            .buttonStyle(.borderedProminent)
                    }
                } else {
                    List {
                        ForEach(carrito.items) { item in
                            HStack {
                                Image(systemName: item.producto.icono)
                                    .font(.title2)
                                    .foregroundStyle(item.producto.color)
                                    .frame(width: 44)

                                VStack(alignment: .leading, spacing: 2) {
                                    Text(item.producto.nombre)
                                        .fontWeight(.medium)
                                    Text("$\(item.producto.precio, specifier: "%.2f") c/u")
                                        .font(.caption)
                                        .foregroundStyle(.secondary)
                                }

                                Spacer()

                                VStack(alignment: .trailing, spacing: 2) {
                                    Text("x\(item.cantidad)")
                                        .font(.subheadline)
                                        .foregroundStyle(.secondary)
                                    Text("$\(item.subtotal, specifier: "%.2f")")
                                        .fontWeight(.semibold)
                                }
                            }
                            .swipeActions {
                                Button(role: .destructive) {
                                    carrito.eliminar(item)
                                } label: {
                                    Label("Eliminar", systemImage: "trash")
                                }
                            }
                        }

                        Section {
                            HStack {
                                Text("Total (\(carrito.totalItems) artículos)")
                                    .fontWeight(.semibold)
                                Spacer()
                                Text("$\(carrito.total, specifier: "%.2f")")
                                    .font(.title3.bold())
                                    .foregroundStyle(.blue)
                            }
                        }
                    }
                }
            }
            .navigationTitle("🛒 Mi Carrito")
            .navigationBarTitleDisplayMode(.inline)
            .toolbar {
                ToolbarItem(placement: .topBarLeading) {
                    Button("Cerrar") { dismiss() }
                }
                if !carrito.isEmpty {
                    ToolbarItem(placement: .topBarTrailing) {
                        Button("Vaciar") { mostrarConfirmacion = true }
                            .foregroundStyle(.red)
                    }
                }
                if !carrito.isEmpty {
                    ToolbarItem(placement: .bottomBar) {
                        Button {
                            print("Compra realizada: $\(carrito.total)")
                            carrito.vaciar()
                            dismiss()
                        } label: {
                            Text("Comprar — $\(carrito.total, specifier: "%.2f")")
                                .fontWeight(.semibold)
                                .frame(maxWidth: .infinity)
                                .padding()
                                .background(.blue)
                                .foregroundStyle(.white)
                                .clipShape(RoundedRectangle(cornerRadius: 12))
                        }
                        .padding(.horizontal)
                    }
                }
            }
            .confirmationDialog("¿Vaciar carrito?",
                               isPresented: $mostrarConfirmacion,
                               titleVisibility: .visible) {
                Button("Vaciar carrito", role: .destructive) {
                    carrito.vaciar()
                }
                Button("Cancelar", role: .cancel) { }
            }
        }
    }
}

// MARK: - App Entry Point
struct TiendaApp: App {
    @StateObject private var carrito = CarritoViewModel()

    var body: some Scene {
        WindowGroup {
            CatalogoView()
                .environmentObject(carrito)
        }
    }
}

#Preview {
    CatalogoView()
        .environmentObject(CarritoViewModel())
}
```

---

## ✅ Resumen

| Wrapper | Uso | Dónde se inyecta |
|---|---|---|
| `@EnvironmentObject` | Estado global compartido | Con `.environmentObject()` |
| `@Environment` | Valores del sistema SwiftUI | Automáticamente por SwiftUI |

**Cuándo usar cada uno:**
- `@State` → estado local simple
- `@StateObject` / `@ObservedObject` → ViewModel de pantalla
- `@EnvironmentObject` → estado global (sesión, carrito, tema)

---

⬅️ [02 — @ObservedObject](./02-observedobject-stateobject.md) | ➡️ [Módulo 05 — Formularios](../modulo-05-interaccion-usuario/01-formularios-inputs.md)

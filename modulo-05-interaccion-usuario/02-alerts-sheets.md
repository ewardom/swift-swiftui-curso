# 🔔 Alerts, Sheets y Modales en SwiftUI

## 🎯 Lo que aprenderás

- `Alert` para mensajes importantes
- `ConfirmationDialog` para opciones
- `Sheet` y `FullScreenCover`
- `Popover` para iPad
- Flujo completo de confirmación de borrado

---

## 1. Alert

```swift
import SwiftUI

struct EjemplosAlert: View {
    @State private var mostrarAlerta = false
    @State private var mostrarError = false
    @State private var mostrarConfirmacion = false
    @State private var mensaje = ""

    var body: some View {
        VStack(spacing: 16) {
            // Alert básico
            Button("Mostrar Alerta") {
                mostrarAlerta = true
            }
            .buttonStyle(.borderedProminent)
            .alert("Información", isPresented: $mostrarAlerta) {
                Button("OK") { }
            } message: {
                Text("Esta es una alerta informativa.")
            }

            // Alert de error
            Button("Mostrar Error") {
                mostrarError = true
            }
            .buttonStyle(.bordered)
            .tint(.red)
            .alert("Error", isPresented: $mostrarError) {
                Button("Reintentar") { print("Reintentando...") }
                Button("Cancelar", role: .cancel) { }
            } message: {
                Text("No se pudo conectar al servidor.\nVerifica tu conexión.")
            }

            // Alert con campo de texto
            Button("Pedir nombre") {
                mostrarConfirmacion = true
            }
            .buttonStyle(.bordered)
            .alert("¿Cuál es tu nombre?",
                   isPresented: $mostrarConfirmacion) {
                TextField("Nombre", text: $mensaje)
                Button("Aceptar") {
                    print("Nombre: \(mensaje)")
                }
                Button("Cancelar", role: .cancel) { }
            }

            if !mensaje.isEmpty {
                Text("Hola, \(mensaje)! 👋")
                    .font(.headline)
            }
        }
        .padding()
    }
}
```

---

## 2. ConfirmationDialog

Ideal para acciones destructivas o múltiples opciones:

```swift
struct EjemploConfirmationDialog: View {
    @State private var mostrarOpciones = false
    @State private var mostrarBorrar = false
    @State private var accionSeleccionada = ""

    var body: some View {
        VStack(spacing: 20) {
            Text(accionSeleccionada.isEmpty
                 ? "Sin acción" : "Acción: \(accionSeleccionada)")
                .font(.headline)

            // Opciones de compartir
            Button("Opciones de imagen") {
                mostrarOpciones = true
            }
            .buttonStyle(.borderedProminent)
            .confirmationDialog("¿Qué deseas hacer?",
                               isPresented: $mostrarOpciones,
                               titleVisibility: .visible) {
                Button("Guardar en Fotos") {
                    accionSeleccionada = "Guardada"
                }
                Button("Compartir") {
                    accionSeleccionada = "Compartida"
                }
                Button("Copiar enlace") {
                    accionSeleccionada = "Copiada"
                }
                Button("Cancelar", role: .cancel) { }
            }

            // Confirmación de borrado
            Button("Eliminar cuenta") {
                mostrarBorrar = true
            }
            .buttonStyle(.bordered)
            .tint(.red)
            .confirmationDialog("¿Eliminar cuenta?",
                               isPresented: $mostrarBorrar,
                               titleVisibility: .visible) {
                Button("Eliminar permanentemente",
                       role: .destructive) {
                    accionSeleccionada = "Cuenta eliminada"
                }
                Button("Cancelar", role: .cancel) { }
            } message: {
                Text("Esta acción no se puede deshacer.")
            }
        }
        .padding()
    }
}
```

---

## 3. Sheet

```swift
struct EjemploSheet: View {
    @State private var mostrarSheet = false
    @State private var mostrarSheetDetente = false

    var body: some View {
        VStack(spacing: 20) {
            // Sheet estándar
            Button("Abrir Sheet") {
                mostrarSheet = true
            }
            .buttonStyle(.borderedProminent)
            .sheet(isPresented: $mostrarSheet) {
                ContenidoSheet()
            }

            // Sheet con tamaño personalizado (iOS 16+)
            Button("Sheet Mediano") {
                mostrarSheetDetente = true
            }
            .buttonStyle(.bordered)
            .sheet(isPresented: $mostrarSheetDetente) {
                ContenidoSheet()
                    .presentationDetents([.medium, .large])
                    .presentationDragIndicator(.visible)
            }
        }
    }
}

struct ContenidoSheet: View {
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        NavigationStack {
            VStack(spacing: 20) {
                Image(systemName: "star.fill")
                    .font(.system(size: 60))
                    .foregroundStyle(.yellow)

                Text("¡Gracias por usar la app!")
                    .font(.title2.bold())

                Text("Tu opinión nos ayuda a mejorar.")
                    .foregroundStyle(.secondary)
                    .multilineTextAlignment(.center)

                Button("Cerrar") { dismiss() }
                    .buttonStyle(.borderedProminent)
            }
            .padding()
            .navigationTitle("Valoración")
            .navigationBarTitleDisplayMode(.inline)
            .toolbar {
                ToolbarItem(placement: .topBarTrailing) {
                    Button("Cerrar") { dismiss() }
                }
            }
        }
    }
}
```

---

## 4. FullScreenCover y Popover

```swift
struct EjemploModales: View {
    @State private var mostrarFullScreen = false
    @State private var mostrarPopover = false

    var body: some View {
        HStack(spacing: 20) {
            // FullScreenCover
            Button("Full Screen") {
                mostrarFullScreen = true
            }
            .buttonStyle(.borderedProminent)
            .fullScreenCover(isPresented: $mostrarFullScreen) {
                PantallaCompleta(mostrar: $mostrarFullScreen)
            }

            // Popover (ideal para iPad, en iPhone se comporta como sheet)
            Button("Popover") {
                mostrarPopover = true
            }
            .buttonStyle(.bordered)
            .popover(isPresented: $mostrarPopover,
                     arrowEdge: .top) {
                VStack(spacing: 12) {
                    Text("💡 Consejo del día")
                        .font(.headline)
                    Text("Usa guard let para\nvalidar datos al inicio\nde tus funciones.")
                        .multilineTextAlignment(.center)
                        .font(.subheadline)
                    Button("Entendido") { mostrarPopover = false }
                        .buttonStyle(.borderedProminent)
                }
                .padding()
                .frame(width: 240)
                .presentationCompactAdaptation(.popover)
            }
        }
    }
}

struct PantallaCompleta: View {
    @Binding var mostrar: Bool

    var body: some View {
        ZStack {
            LinearGradient(colors: [.indigo, .purple],
                          startPoint: .top,
                          endPoint: .bottom)
                .ignoresSafeArea()

            VStack(spacing: 24) {
                Image(systemName: "checkmark.seal.fill")
                    .font(.system(size: 80))
                    .foregroundStyle(.white)

                Text("¡Operación Exitosa!")
                    .font(.largeTitle.bold())
                    .foregroundStyle(.white)

                Text("Todo salió perfecto.")
                    .foregroundStyle(.white.opacity(0.8))

                Button {
                    mostrar = false
                } label: {
                    Text("Continuar")
                        .fontWeight(.semibold)
                        .frame(width: 200)
                        .padding()
                        .background(.white)
                        .foregroundStyle(.indigo)
                        .clipShape(RoundedRectangle(cornerRadius: 14))
                }
            }
        }
    }
}
```

---

## 5. 🛠️ Proyecto: Flujo de Confirmación de Borrado

```swift
import SwiftUI

struct Archivo: Identifiable {
    let id = UUID()
    var nombre: String
    var tipo: String
    var tamaño: String
    var icono: String
    var color: Color
}

struct GestorArchivos: View {
    @State private var archivos: [Archivo] = [
        Archivo(nombre: "Proyecto_Final.swift", tipo: "Swift",
                tamaño: "24 KB", icono: "swift", color: .orange),
        Archivo(nombre: "Diseño_App.fig", tipo: "Figma",
                tamaño: "2.4 MB", icono: "paintpalette", color: .purple),
        Archivo(nombre: "Grabacion.mp4", tipo: "Video",
                tamaño: "145 MB", icono: "video", color: .red),
        Archivo(nombre: "Notas.md", tipo: "Markdown",
                tamaño: "8 KB", icono: "doc.text", color: .blue),
        Archivo(nombre: "Recursos.zip", tipo: "ZIP",
                tamaño: "56 MB", icono: "archivebox", color: .gray)
    ]

    @State private var archivoAEliminar: Archivo?
    @State private var mostrarConfirmacion = false
    @State private var mostrarDetalle: Archivo?
    @State private var mostrarVaciarPapelera = false
    @State private var archivosEliminados: [Archivo] = []
    @State private var mostrarPapelera = false
    @State private var ultimoEliminado: Archivo?
    @State private var mostrarDeshacer = false

    var body: some View {
        NavigationStack {
            List {
                ForEach(archivos) { archivo in
                    FilaArchivo(archivo: archivo)
                        .contentShape(Rectangle())
                        .onTapGesture {
                            mostrarDetalle = archivo
                        }
                        .swipeActions(edge: .trailing) {
                            // Botón eliminar con confirmación
                            Button(role: .destructive) {
                                archivoAEliminar = archivo
                                mostrarConfirmacion = true
                            } label: {
                                Label("Eliminar", systemImage: "trash")
                            }

                            Button {
                                compartir(archivo)
                            } label: {
                                Label("Compartir",
                                      systemImage: "square.and.arrow.up")
                            }
                            .tint(.blue)
                        }
                        .swipeActions(edge: .leading) {
                            Button {
                                favorito(archivo)
                            } label: {
                                Label("Favorito", systemImage: "star")
                            }
                            .tint(.yellow)
                        }
                }
            }
            .navigationTitle("📁 Mis Archivos")
            .toolbar {
                ToolbarItem(placement: .topBarTrailing) {
                    Button {
                        mostrarPapelera = true
                    } label: {
                        Label("Papelera (\(archivosEliminados.count))",
                              systemImage: "trash")
                    }
                }
            }
            // Confirmación de borrado individual
            .confirmationDialog(
                "¿Eliminar \"\(archivoAEliminar?.nombre ?? "")\"?",
                isPresented: $mostrarConfirmacion,
                titleVisibility: .visible
            ) {
                Button("Mover a papelera", role: .destructive) {
                    if let archivo = archivoAEliminar {
                        moverAPapelera(archivo)
                    }
                }
                Button("Cancelar", role: .cancel) {
                    archivoAEliminar = nil
                }
            } message: {
                Text("El archivo se moverá a la papelera.")
            }
            // Sheet de detalle
            .sheet(item: $mostrarDetalle) { archivo in
                DetalleArchivoView(archivo: archivo)
                    .presentationDetents([.medium])
            }
            // Sheet de papelera
            .sheet(isPresented: $mostrarPapelera) {
                PapeleraView(
                    archivos: $archivosEliminados,
                    alRestaurar: { restaurar($0) }
                )
            }
            // Banner "deshacer"
            .overlay(alignment: .bottom) {
                if mostrarDeshacer, let archivo = ultimoEliminado {
                    BannerDeshacer(nombreArchivo: archivo.nombre) {
                        restaurar(archivo)
                        mostrarDeshacer = false
                    }
                    .transition(.move(edge: .bottom).combined(with: .opacity))
                    .padding(.bottom, 8)
                }
            }
            .animation(.spring(), value: mostrarDeshacer)
        }
    }

    func moverAPapelera(_ archivo: Archivo) {
        withAnimation {
            archivos.removeAll { $0.id == archivo.id }
            archivosEliminados.append(archivo)
            ultimoEliminado = archivo
            mostrarDeshacer = true
        }
        // Ocultar banner después de 4 segundos
        DispatchQueue.main.asyncAfter(deadline: .now() + 4) {
            withAnimation { mostrarDeshacer = false }
        }
    }

    func restaurar(_ archivo: Archivo) {
        withAnimation {
            archivosEliminados.removeAll { $0.id == archivo.id }
            archivos.append(archivo)
            mostrarDeshacer = false
        }
    }

    func compartir(_ archivo: Archivo) {
        print("Compartiendo: \(archivo.nombre)")
    }

    func favorito(_ archivo: Archivo) {
        print("Favorito: \(archivo.nombre)")
    }
}

struct FilaArchivo: View {
    let archivo: Archivo

    var body: some View {
        HStack(spacing: 14) {
            RoundedRectangle(cornerRadius: 10)
                .fill(archivo.color.opacity(0.15))
                .frame(width: 44, height: 44)
                .overlay(
                    Image(systemName: archivo.icono)
                        .foregroundStyle(archivo.color)
                )

            VStack(alignment: .leading, spacing: 3) {
                Text(archivo.nombre)
                    .fontWeight(.medium)
                    .lineLimit(1)
                HStack(spacing: 6) {
                    Text(archivo.tipo)
                    Text("·")
                    Text(archivo.tamaño)
                }
                .font(.caption)
                .foregroundStyle(.secondary)
            }
        }
        .padding(.vertical, 4)
    }
}

struct DetalleArchivoView: View {
    let archivo: Archivo
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        NavigationStack {
            VStack(spacing: 24) {
                RoundedRectangle(cornerRadius: 20)
                    .fill(archivo.color.gradient)
                    .frame(width: 100, height: 100)
                    .overlay(
                        Image(systemName: archivo.icono)
                            .font(.system(size: 44))
                            .foregroundStyle(.white)
                    )

                VStack(spacing: 6) {
                    Text(archivo.nombre)
                        .font(.headline)
                    Text(archivo.tipo)
                        .foregroundStyle(.secondary)
                }

                VStack(spacing: 0) {
                    InfoRow(etiqueta: "Tamaño", valor: archivo.tamaño)
                    Divider()
                    InfoRow(etiqueta: "Tipo", valor: archivo.tipo)
                }
                .background(.gray.opacity(0.08))
                .clipShape(RoundedRectangle(cornerRadius: 12))
                .padding(.horizontal)

                Spacer()
            }
            .padding(.top, 30)
            .navigationTitle("Detalles")
            .navigationBarTitleDisplayMode(.inline)
            .toolbar {
                ToolbarItem(placement: .topBarTrailing) {
                    Button("Listo") { dismiss() }
                }
            }
        }
    }
}

struct InfoRow: View {
    let etiqueta: String
    let valor: String

    var body: some View {
        HStack {
            Text(etiqueta)
                .foregroundStyle(.secondary)
            Spacer()
            Text(valor)
                .fontWeight(.medium)
        }
        .padding()
    }
}

struct PapeleraView: View {
    @Binding var archivos: [Archivo]
    let alRestaurar: (Archivo) -> Void
    @Environment(\.dismiss) private var dismiss
    @State private var confirmarVaciar = false

    var body: some View {
        NavigationStack {
            Group {
                if archivos.isEmpty {
                    VStack(spacing: 16) {
                        Image(systemName: "trash")
                            .font(.system(size: 50))
                            .foregroundStyle(.secondary)
                        Text("Papelera vacía")
                            .font(.title3)
                    }
                } else {
                    List(archivos) { archivo in
                        FilaArchivo(archivo: archivo)
                            .swipeActions {
                                Button {
                                    alRestaurar(archivo)
                                } label: {
                                    Label("Restaurar",
                                          systemImage: "arrow.uturn.backward")
                                }
                                .tint(.green)
                            }
                    }
                }
            }
            .navigationTitle("Papelera")
            .navigationBarTitleDisplayMode(.inline)
            .toolbar {
                ToolbarItem(placement: .topBarLeading) {
                    Button("Cerrar") { dismiss() }
                }
                if !archivos.isEmpty {
                    ToolbarItem(placement: .topBarTrailing) {
                        Button("Vaciar") { confirmarVaciar = true }
                            .foregroundStyle(.red)
                    }
                }
            }
            .confirmationDialog("¿Vaciar papelera?",
                               isPresented: $confirmarVaciar,
                               titleVisibility: .visible) {
                Button("Eliminar todo", role: .destructive) {
                    archivos.removeAll()
                }
                Button("Cancelar", role: .cancel) { }
            } message: {
                Text("Se eliminarán \(archivos.count) archivos permanentemente.")
            }
        }
    }
}

struct BannerDeshacer: View {
    let nombreArchivo: String
    let alDeshacer: () -> Void

    var body: some View {
        HStack {
            Image(systemName: "trash")
                .foregroundStyle(.secondary)
            Text("\"\(nombreArchivo)\" eliminado")
                .font(.subheadline)
                .lineLimit(1)
            Spacer()
            Button("Deshacer", action: alDeshacer)
                .fontWeight(.semibold)
                .foregroundStyle(.blue)
        }
        .padding()
        .background(.regularMaterial)
        .clipShape(RoundedRectangle(cornerRadius: 14))
        .shadow(radius: 4)
        .padding(.horizontal)
    }
}

#Preview {
    GestorArchivos()
}
```

---

## ✅ Resumen

| Modal | Cuándo usar |
|---|---|
| `Alert` | Información importante, 1-2 botones |
| `ConfirmationDialog` | Múltiples opciones o acciones destructivas |
| `Sheet` | Flujos secundarios, formularios |
| `FullScreenCover` | Onboarding, login, cámaras |
| `Popover` | Contexto adicional (iPad) |

---

⬅️ [01 — Formularios](./01-formularios-inputs.md) | ➡️ [Módulo 06 — MVVM](../modulo-06-arquitectura-mvvm/01-patron-mvvm.md)

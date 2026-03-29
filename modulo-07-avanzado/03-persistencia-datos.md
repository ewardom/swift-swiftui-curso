# 💾 Persistencia de Datos en SwiftUI

## 🎯 Lo que aprenderás

- `@AppStorage` para datos simples
- **SwiftData** moderno (iOS 17+)
- `@Model`, `@Query` y `ModelContainer`
- Proyecto: Lista de Tareas persistente

---

## 1. @AppStorage — Preferencias Simples

```swift
import SwiftUI

struct ConfiguracionView: View {
    @AppStorage("nombreUsuario") private var nombre = ""
    @AppStorage("modoOscuro") private var modoOscuro = false
    @AppStorage("tamañoFuente") private var tamaño = 16.0
    @AppStorage("vecesAbierta") private var vecesAbierta = 0

    var body: some View {
        Form {
            Section("Perfil") {
                TextField("Tu nombre", text: $nombre)
            }
            Section("Apariencia") {
                Toggle("Modo Oscuro", isOn: $modoOscuro)
                Slider(value: $tamaño, in: 12...24, step: 1) {
                    Text("Fuente: \(Int(tamaño))pt")
                }
            }
            Section {
                LabeledContent("Veces abierta", value: "\(vecesAbierta)")
            }
        }
        .navigationTitle("Configuración")
        .onAppear { vecesAbierta += 1 }
    }
}
```

---

## 2. SwiftData — Persistencia Completa (iOS 17+)

```swift
import SwiftUI
import SwiftData

// 1. Definir el modelo
@Model
class TareaSD {
    var titulo: String
    var notas: String
    var fecha: Date
    var completada: Bool
    var prioridad: Int  // 0=baja, 1=media, 2=alta

    init(titulo: String, notas: String = "",
         fecha: Date = Date(),
         completada: Bool = false,
         prioridad: Int = 1) {
        self.titulo = titulo
        self.notas = notas
        self.fecha = fecha
        self.completada = completada
        self.prioridad = prioridad
    }

    var nombrePrioridad: String {
        ["Baja","Media","Alta"][safe: prioridad] ?? "Media"
    }

    var colorPrioridad: Color {
        [Color.green, .orange, .red][safe: prioridad] ?? .orange
    }
}

// 2. Configurar en la app
@main
struct TareasSDApp: App {
    var body: some Scene {
        WindowGroup { TareasSDView() }
            .modelContainer(for: TareaSD.self)
    }
}
```

---

## 3. 🛠️ Proyecto: Lista de Tareas con SwiftData

```swift
import SwiftUI
import SwiftData

struct TareasSDView: View {
    @Query(sort: \TareaSD.fecha, order: .reverse)
    private var tareas: [TareaSD]

    @Environment(\.modelContext) private var contexto

    @State private var mostrarNueva = false
    @State private var busqueda = ""
    @State private var filtro = 0  // 0=todos, 1=pendientes, 2=completadas

    var tareasFiltradas: [TareaSD] {
        tareas.filter { t in
            let coincide = busqueda.isEmpty ||
                t.titulo.localizedCaseInsensitiveContains(busqueda)
            let estado = filtro == 0 ? true
                       : filtro == 1 ? !t.completada
                       : t.completada
            return coincide && estado
        }
    }

    var pendientes: Int { tareas.filter { !$0.completada }.count }

    var body: some View {
        NavigationStack {
            VStack(spacing: 0) {
                Picker("Filtro", selection: $filtro) {
                    Text("Todas").tag(0)
                    Text("Pendientes").tag(1)
                    Text("Completadas").tag(2)
                }
                .pickerStyle(.segmented)
                .padding()

                if tareasFiltradas.isEmpty {
                    VStack(spacing: 16) {
                        Spacer()
                        Image(systemName: filtro == 2
                              ? "checkmark.circle" : "tray")
                            .font(.system(size: 50))
                            .foregroundStyle(.secondary)
                        Text(filtro == 2
                             ? "Sin tareas completadas"
                             : "Sin tareas. ¡Agrega una!")
                            .foregroundStyle(.secondary)
                        if filtro == 0 {
                            Button("Agregar tarea") {
                                mostrarNueva = true
                            }
                            .buttonStyle(.borderedProminent)
                        }
                        Spacer()
                    }
                } else {
                    List {
                        ForEach(tareasFiltradas) { tarea in
                            NavigationLink {
                                DetalleSDView(tarea: tarea)
                            } label: {
                                FilaTareaSD(tarea: tarea)
                            }
                            .swipeActions(edge: .trailing) {
                                Button(role: .destructive) {
                                    contexto.delete(tarea)
                                } label: {
                                    Label("Eliminar", systemImage: "trash")
                                }
                            }
                            .swipeActions(edge: .leading) {
                                Button {
                                    withAnimation {
                                        tarea.completada.toggle()
                                    }
                                } label: {
                                    Label(
                                        tarea.completada ? "Reabrir" : "Completar",
                                        systemImage: tarea.completada
                                            ? "arrow.uturn.left" : "checkmark"
                                    )
                                }
                                .tint(tarea.completada ? .orange : .green)
                            }
                        }
                    }
                }
            }
            .navigationTitle("Tareas (\(pendientes))")
            .searchable(text: $busqueda, prompt: "Buscar...")
            .toolbar {
                ToolbarItem(placement: .topBarTrailing) {
                    Button {
                        mostrarNueva = true
                    } label: {
                        Image(systemName: "plus")
                    }
                }
                if tareas.filter(\.completada).count > 0 {
                    ToolbarItem(placement: .topBarLeading) {
                        Button("Limpiar") {
                            tareas.filter(\.completada)
                                .forEach { contexto.delete($0) }
                        }
                        .foregroundStyle(.red)
                        .font(.caption)
                    }
                }
            }
            .sheet(isPresented: $mostrarNueva) {
                NuevaTareaSDView()
            }
        }
    }
}

// MARK: - Fila
struct FilaTareaSD: View {
    let tarea: TareaSD

    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: tarea.completada
                  ? "checkmark.circle.fill" : "circle")
                .font(.title3)
                .foregroundStyle(tarea.completada ? .green : .gray)
                .onTapGesture { withAnimation { tarea.completada.toggle() } }

            VStack(alignment: .leading, spacing: 3) {
                Text(tarea.titulo)
                    .strikethrough(tarea.completada)
                    .foregroundStyle(tarea.completada ? .secondary : .primary)
                    .lineLimit(1)

                HStack(spacing: 6) {
                    Circle()
                        .fill(tarea.colorPrioridad)
                        .frame(width: 6, height: 6)
                    Text(tarea.nombrePrioridad)
                        .font(.caption2)
                        .foregroundStyle(tarea.colorPrioridad)
                    Text("·").foregroundStyle(.tertiary)
                    Text(tarea.fecha, style: .date)
                        .font(.caption2)
                        .foregroundStyle(.secondary)
                }
            }
        }
        .padding(.vertical, 4)
    }
}

// MARK: - Nueva Tarea
struct NuevaTareaSDView: View {
    @Environment(\.modelContext) private var contexto
    @Environment(\.dismiss) private var dismiss

    @State private var titulo = ""
    @State private var notas = ""
    @State private var fecha = Date()
    @State private var prioridad = 1

    var body: some View {
        NavigationStack {
            Form {
                Section("Tarea") {
                    TextField("¿Qué necesitas hacer?", text: $titulo)
                    TextEditor(text: $notas)
                        .frame(minHeight: 80)
                        .overlay(alignment: .topLeading) {
                            if notas.isEmpty {
                                Text("Notas...")
                                    .foregroundStyle(.tertiary)
                                    .padding(8)
                                    .allowsHitTesting(false)
                            }
                        }
                }
                Section("Detalles") {
                    DatePicker("Fecha", selection: $fecha,
                               displayedComponents: [.date, .hourAndMinute])
                    Picker("Prioridad", selection: $prioridad) {
                        Text("🟢 Baja").tag(0)
                        Text("🟡 Media").tag(1)
                        Text("🔴 Alta").tag(2)
                    }
                    .pickerStyle(.segmented)
                }
            }
            .navigationTitle("Nueva Tarea")
            .navigationBarTitleDisplayMode(.inline)
            .toolbar {
                ToolbarItem(placement: .cancellationAction) {
                    Button("Cancelar") { dismiss() }
                }
                ToolbarItem(placement: .confirmationAction) {
                    Button("Guardar") {
                        let nueva = TareaSD(
                            titulo: titulo.trimmingCharacters(in: .whitespaces),
                            notas: notas,
                            fecha: fecha,
                            prioridad: prioridad
                        )
                        contexto.insert(nueva)
                        dismiss()
                    }
                    .disabled(titulo.trimmingCharacters(in: .whitespaces).isEmpty)
                }
            }
        }
    }
}

// MARK: - Detalle / Edición
struct DetalleSDView: View {
    @Bindable var tarea: TareaSD

    var body: some View {
        Form {
            Section("Tarea") {
                TextField("Título", text: $tarea.titulo)
                TextEditor(text: $tarea.notas)
                    .frame(minHeight: 100)
            }
            Section("Detalles") {
                DatePicker("Fecha", selection: $tarea.fecha,
                           displayedComponents: [.date, .hourAndMinute])
                Picker("Prioridad", selection: $tarea.prioridad) {
                    Text("🟢 Baja").tag(0)
                    Text("🟡 Media").tag(1)
                    Text("🔴 Alta").tag(2)
                }
                Toggle("Completada", isOn: $tarea.completada)
            }
        }
        .navigationTitle("Editar Tarea")
        .navigationBarTitleDisplayMode(.inline)
    }
}

// Extension helper
extension Array {
    subscript(safe index: Index) -> Element? {
        indices.contains(index) ? self[index] : nil
    }
}

#Preview {
    TareasSDView()
        .modelContainer(for: TareaSD.self, inMemory: true)
}
```

---

## ✅ Resumen

| Técnica | Cuándo usar |
|---|---|
| `@AppStorage` | Preferencias simples (Bool, String, Int) |
| `SwiftData @Model` | Datos complejos que necesitan persistir |
| `@Query` | Leer datos de SwiftData en vistas |
| `modelContext.insert()` | Crear nuevos objetos |
| `modelContext.delete()` | Eliminar objetos |
| `@Bindable` | Editar propiedades de un @Model en la vista |

---

⬅️ [02 — Networking](./02-networking-json.md) | ➡️ [Módulo 08 — Publicación](../modulo-08-publicacion/01-app-store.md)

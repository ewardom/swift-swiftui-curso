# 📋 Listas y Grids en SwiftUI

## 🎯 Lo que aprenderás

- Crear listas con `List` y `ForEach`
- Protocolo `Identifiable`
- Secciones en listas
- Swipe actions (deslizar para acciones)
- Grids con `LazyVGrid`
- Proyecto: Lista de Tareas

---

## 1. List Básico

```swift
import SwiftUI

struct ListaBasica: View {
    let frutas = ["🍎 Manzana", "🍊 Naranja", "🍋 Limón", "🍇 Uva", "🍓 Fresa"]

    var body: some View {
        List(frutas, id: \.self) { fruta in
            Text(fruta)
        }
    }
}
```

---

## 2. ForEach e Identifiable

La forma más común y flexible de crear listas con modelos propios:

```swift
// Modelo que conforma Identifiable
struct Contacto: Identifiable {
    let id = UUID()   // identificador único automático
    var nombre: String
    var telefono: String
    var esFavorito: Bool = false
}

struct ListaContactos: View {
    let contactos = [
        Contacto(nombre: "Ana García", telefono: "555-1111", esFavorito: true),
        Contacto(nombre: "Luis Pérez", telefono: "555-2222"),
        Contacto(nombre: "María López", telefono: "555-3333", esFavorito: true),
        Contacto(nombre: "Carlos Ruiz", telefono: "555-4444")
    ]

    var body: some View {
        NavigationStack {
            List {
                ForEach(contactos) { contacto in
                    HStack(spacing: 12) {
                        // Avatar
                        Circle()
                            .fill(.blue.gradient)
                            .frame(width: 44, height: 44)
                            .overlay(
                                Text(String(contacto.nombre.prefix(1)))
                                    .font(.headline)
                                    .foregroundStyle(.white)
                            )

                        // Info
                        VStack(alignment: .leading, spacing: 2) {
                            Text(contacto.nombre)
                                .fontWeight(.medium)
                            Text(contacto.telefono)
                                .font(.caption)
                                .foregroundStyle(.secondary)
                        }

                        Spacer()

                        // Favorito
                        if contacto.esFavorito {
                            Image(systemName: "star.fill")
                                .foregroundStyle(.yellow)
                        }
                    }
                    .padding(.vertical, 4)
                }
            }
            .navigationTitle("Contactos")
        }
    }
}
```

---

## 3. List con @State y Mutaciones

Para modificar la lista, necesitamos `@State`:

```swift
struct ListaEditable: View {
    @State private var contactos = [
        Contacto(nombre: "Ana García", telefono: "555-1111"),
        Contacto(nombre: "Luis Pérez", telefono: "555-2222"),
        Contacto(nombre: "María López", telefono: "555-3333")
    ]

    var body: some View {
        NavigationStack {
            List {
                ForEach(contactos) { contacto in
                    Text(contacto.nombre)
                }
                // Eliminar con swipe
                .onDelete(perform: eliminar)
                // Reordenar con drag
                .onMove(perform: mover)
            }
            .navigationTitle("Contactos")
            .toolbar {
                EditButton()   // activa modo edición
            }
        }
    }

    func eliminar(en offsets: IndexSet) {
        contactos.remove(atOffsets: offsets)
    }

    func mover(desde source: IndexSet, hasta destination: Int) {
        contactos.move(fromOffsets: source, toOffset: destination)
    }
}
```

---

## 4. Secciones en List

```swift
struct TareaSeccionada: Identifiable {
    let id = UUID()
    var titulo: String
    var completada: Bool = false
    var prioridad: String
}

struct ListaConSecciones: View {
    @State private var tareasPendientes = [
        TareaSeccionada(titulo: "Aprender SwiftUI", prioridad: "alta"),
        TareaSeccionada(titulo: "Hacer ejercicio", prioridad: "media"),
        TareaSeccionada(titulo: "Leer un libro", prioridad: "baja")
    ]

    @State private var tareasCompletadas = [
        TareaSeccionada(titulo: "Instalar Xcode", prioridad: "alta", completada: true),
        TareaSeccionada(titulo: "Crear proyecto", prioridad: "media", completada: true)
    ]

    var body: some View {
        NavigationStack {
            List {
                Section {
                    ForEach(tareasPendientes) { tarea in
                        FilaTarea(tarea: tarea)
                    }
                } header: {
                    Label("Pendientes (\(tareasPendientes.count))",
                          systemImage: "circle")
                        .foregroundStyle(.orange)
                }

                Section {
                    ForEach(tareasCompletadas) { tarea in
                        FilaTarea(tarea: tarea)
                    }
                } header: {
                    Label("Completadas (\(tareasCompletadas.count))",
                          systemImage: "checkmark.circle.fill")
                        .foregroundStyle(.green)
                } footer: {
                    Text("Swipe para eliminar tareas completadas")
                        .font(.caption)
                }
            }
            .navigationTitle("Mis Tareas")
        }
    }
}

struct FilaTarea: View {
    let tarea: TareaSeccionada

    var body: some View {
        HStack {
            Image(systemName: tarea.completada
                  ? "checkmark.circle.fill"
                  : "circle")
                .foregroundStyle(tarea.completada ? .green : .gray)

            Text(tarea.titulo)
                .strikethrough(tarea.completada)
                .foregroundStyle(tarea.completada ? .secondary : .primary)

            Spacer()

            // Prioridad
            Circle()
                .fill(colorPrioridad(tarea.prioridad))
                .frame(width: 8, height: 8)
        }
    }

    func colorPrioridad(_ prioridad: String) -> Color {
        switch prioridad {
        case "alta":  return .red
        case "media": return .orange
        default:      return .green
        }
    }
}
```

---

## 5. Swipe Actions

```swift
struct ListaConSwipe: View {
    @State private var elementos = [
        "Revisar emails",
        "Llamar al cliente",
        "Preparar presentación",
        "Revisar código",
        "Actualizar documentación"
    ]
    @State private var favoritos: Set<String> = []

    var body: some View {
        NavigationStack {
            List {
                ForEach(elementos, id: \.self) { elemento in
                    Text(elemento)
                        // Acciones al deslizar a la izquierda
                        .swipeActions(edge: .trailing) {
                            Button(role: .destructive) {
                                eliminar(elemento)
                            } label: {
                                Label("Eliminar", systemImage: "trash")
                            }

                            Button {
                                archivar(elemento)
                            } label: {
                                Label("Archivar", systemImage: "archivebox")
                            }
                            .tint(.orange)
                        }
                        // Acciones al deslizar a la derecha
                        .swipeActions(edge: .leading) {
                            Button {
                                toggleFavorito(elemento)
                            } label: {
                                Label(
                                    favoritos.contains(elemento) ? "Quitar" : "Favorito",
                                    systemImage: favoritos.contains(elemento)
                                        ? "star.slash" : "star"
                                )
                            }
                            .tint(.yellow)
                        }
                }
            }
            .navigationTitle("Tareas")
        }
    }

    func eliminar(_ elemento: String) {
        elementos.removeAll { $0 == elemento }
    }

    func archivar(_ elemento: String) {
        print("Archivando: \(elemento)")
    }

    func toggleFavorito(_ elemento: String) {
        if favoritos.contains(elemento) {
            favoritos.remove(elemento)
        } else {
            favoritos.insert(elemento)
        }
    }
}
```

---

## 6. LazyVGrid — Galería

```swift
struct GaleriaApps: View {
    struct AppItem: Identifiable {
        let id = UUID()
        var nombre: String
        var icono: String
        var color: Color
    }

    let apps = [
        AppItem(nombre: "Mensajes", icono: "message.fill", color: .green),
        AppItem(nombre: "FaceTime", icono: "video.fill", color: .green),
        AppItem(nombre: "Mapas", icono: "map.fill", color: .green),
        AppItem(nombre: "Safari", icono: "safari.fill", color: .blue),
        AppItem(nombre: "Fotos", icono: "photo.fill", color: .orange),
        AppItem(nombre: "Cámara", icono: "camera.fill", color: .gray),
        AppItem(nombre: "Música", icono: "music.note", color: .red),
        AppItem(nombre: "Podcast", icono: "mic.fill", color: .purple),
        AppItem(nombre: "News", icono: "newspaper.fill", color: .red)
    ]

    let columnas = [
        GridItem(.adaptive(minimum: 80))
    ]

    var body: some View {
        NavigationStack {
            ScrollView {
                LazyVGrid(columns: columnas, spacing: 20) {
                    ForEach(apps) { app in
                        VStack(spacing: 8) {
                            RoundedRectangle(cornerRadius: 16)
                                .fill(app.color.gradient)
                                .frame(width: 64, height: 64)
                                .overlay(
                                    Image(systemName: app.icono)
                                        .font(.title2)
                                        .foregroundStyle(.white)
                                )
                                .shadow(radius: 2)

                            Text(app.nombre)
                                .font(.caption)
                                .lineLimit(1)
                        }
                    }
                }
                .padding()
            }
            .navigationTitle("Mis Apps")
        }
    }
}
```

---

## 7. 🛠️ Proyecto: Lista de Tareas Completa

```swift
import SwiftUI

struct Tarea: Identifiable {
    let id = UUID()
    var titulo: String
    var completada: Bool = false
    var prioridad: Prioridad = .media

    enum Prioridad: String, CaseIterable {
        case alta = "Alta"
        case media = "Media"
        case baja = "Baja"

        var color: Color {
            switch self {
            case .alta:  return .red
            case .media: return .orange
            case .baja:  return .green
            }
        }

        var icono: String {
            switch self {
            case .alta:  return "exclamationmark.3"
            case .media: return "exclamationmark.2"
            case .baja:  return "exclamationmark"
            }
        }
    }
}

struct ListaDeTareas: View {
    @State private var tareas: [Tarea] = [
        Tarea(titulo: "Aprender SwiftUI", prioridad: .alta),
        Tarea(titulo: "Crear primera app", prioridad: .alta),
        Tarea(titulo: "Leer documentación", prioridad: .media),
        Tarea(titulo: "Ver WWDC videos", prioridad: .baja)
    ]
    @State private var nuevaTarea = ""
    @State private var mostrarFormulario = false
    @State private var prioridadSeleccionada: Tarea.Prioridad = .media

    var tareasOrdenadas: [Tarea] {
        tareas.sorted {
            if $0.completada != $1.completada { return !$0.completada }
            return $0.prioridad.rawValue < $1.prioridad.rawValue
        }
    }

    var contadorPendientes: Int {
        tareas.filter { !$0.completada }.count
    }

    var body: some View {
        NavigationStack {
            List {
                ForEach(tareasOrdenadas) { tarea in
                    FilaTareaCompleta(tarea: tarea) {
                        toggleTarea(tarea)
                    }
                    .swipeActions(edge: .trailing) {
                        Button(role: .destructive) {
                            eliminarTarea(tarea)
                        } label: {
                            Label("Eliminar", systemImage: "trash")
                        }
                    }
                }
            }
            .navigationTitle("Tareas (\(contadorPendientes))")
            .toolbar {
                ToolbarItem(placement: .topBarTrailing) {
                    Button {
                        mostrarFormulario = true
                    } label: {
                        Image(systemName: "plus")
                    }
                }
            }
            .sheet(isPresented: $mostrarFormulario) {
                FormularioNuevaTarea(
                    titulo: $nuevaTarea,
                    prioridad: $prioridadSeleccionada
                ) {
                    agregarTarea()
                }
            }
        }
    }

    func toggleTarea(_ tarea: Tarea) {
        if let index = tareas.firstIndex(where: { $0.id == tarea.id }) {
            tareas[index].completada.toggle()
        }
    }

    func eliminarTarea(_ tarea: Tarea) {
        tareas.removeAll { $0.id == tarea.id }
    }

    func agregarTarea() {
        guard !nuevaTarea.isEmpty else { return }
        tareas.append(Tarea(titulo: nuevaTarea, prioridad: prioridadSeleccionada))
        nuevaTarea = ""
        prioridadSeleccionada = .media
        mostrarFormulario = false
    }
}

struct FilaTareaCompleta: View {
    let tarea: Tarea
    let alTocar: () -> Void

    var body: some View {
        Button(action: alTocar) {
            HStack(spacing: 12) {
                Image(systemName: tarea.completada
                      ? "checkmark.circle.fill" : "circle")
                    .font(.title3)
                    .foregroundStyle(tarea.completada ? .green : .gray)

                VStack(alignment: .leading, spacing: 2) {
                    Text(tarea.titulo)
                        .strikethrough(tarea.completada)
                        .foregroundStyle(tarea.completada ? .secondary : .primary)

                    Label(tarea.prioridad.rawValue,
                          systemImage: tarea.prioridad.icono)
                        .font(.caption)
                        .foregroundStyle(tarea.prioridad.color)
                }

                Spacer()
            }
        }
        .buttonStyle(.plain)
    }
}

struct FormularioNuevaTarea: View {
    @Binding var titulo: String
    @Binding var prioridad: Tarea.Prioridad
    let alGuardar: () -> Void
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        NavigationStack {
            Form {
                Section("Nueva tarea") {
                    TextField("¿Qué necesitas hacer?", text: $titulo)
                }

                Section("Prioridad") {
                    Picker("Prioridad", selection: $prioridad) {
                        ForEach(Tarea.Prioridad.allCases, id: \.self) { p in
                            Label(p.rawValue, systemImage: p.icono)
                                .foregroundStyle(p.color)
                                .tag(p)
                        }
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
                    Button("Guardar") { alGuardar() }
                        .disabled(titulo.isEmpty)
                }
            }
        }
    }
}

#Preview {
    ListaDeTareas()
}
```

---

## ✅ Resumen

| Componente | Uso |
|---|---|
| `List` | Lista con estilo nativo de iOS |
| `ForEach` | Iterar sobre colecciones en vistas |
| `Identifiable` | Protocolo para identificar elementos únicos |
| `Section` | Agrupar elementos con cabecera/pie |
| `.onDelete` | Habilitar eliminar con swipe |
| `.onMove` | Habilitar reordenar |
| `.swipeActions` | Acciones personalizadas al deslizar |
| `LazyVGrid` | Cuadrícula de elementos |

---

⬅️ [03 — Stacks y Layout](./03-stacks-layout.md) | ➡️ [05 — Navegación](./05-navegacion.md)

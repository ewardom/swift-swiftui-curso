# 📡 @ObservedObject y @StateObject en SwiftUI

## 🎯 Lo que aprenderás

- `ObservableObject` y `@Published`
- Diferencia clave entre `@StateObject` y `@ObservedObject`
- Cuándo usar cada uno
- Patrón ViewModel con SwiftUI
- Proyecto: App de Notas con ViewModel

---

## 1. ObservableObject y @Published

Cuando el estado es complejo y necesitas compartirlo entre vistas, creas una clase que conforma `ObservableObject`:

```swift
import SwiftUI
import Combine

// La clase debe conformar ObservableObject
class ContadorViewModel: ObservableObject {
    // @Published notifica a las vistas cuando cambia
    @Published var contador = 0
    @Published var historial: [String] = []

    func incrementar() {
        contador += 1
        historial.append("➕ Incrementado a \(contador)")
    }

    func decrementar() {
        guard contador > 0 else { return }
        contador -= 1
        historial.append("➖ Decrementado a \(contador)")
    }

    func reiniciar() {
        historial.append("🔄 Reiniciado desde \(contador)")
        contador = 0
    }
}
```

---

## 2. @StateObject — El Propietario

Usa `@StateObject` en la vista que **crea y posee** el ViewModel. SwiftUI garantiza que solo se crea una vez:

```swift
struct ContadorView: View {
    // @StateObject — esta vista ES la dueña del ViewModel
    @StateObject private var viewModel = ContadorViewModel()

    var body: some View {
        NavigationStack {
            VStack(spacing: 24) {
                // Contador principal
                VStack(spacing: 8) {
                    Text("\(viewModel.contador)")
                        .font(.system(size: 80, weight: .bold, design: .rounded))
                        .foregroundStyle(.blue)
                        .contentTransition(.numericText())

                    Text(viewModel.contador == 0 ? "Sin cambios"
                         : viewModel.contador > 0 ? "Incrementado" : "Decrementado")
                        .font(.subheadline)
                        .foregroundStyle(.secondary)
                }
                .padding(30)
                .background(.blue.opacity(0.08))
                .clipShape(Circle())

                // Controles
                HStack(spacing: 20) {
                    Button {
                        withAnimation { viewModel.decrementar() }
                    } label: {
                        Image(systemName: "minus.circle.fill")
                            .font(.system(size: 50))
                            .foregroundStyle(.red)
                    }
                    .disabled(viewModel.contador == 0)

                    Button {
                        withAnimation { viewModel.reiniciar() }
                    } label: {
                        Image(systemName: "arrow.counterclockwise.circle.fill")
                            .font(.system(size: 50))
                            .foregroundStyle(.orange)
                    }

                    Button {
                        withAnimation { viewModel.incrementar() }
                    } label: {
                        Image(systemName: "plus.circle.fill")
                            .font(.system(size: 50))
                            .foregroundStyle(.green)
                    }
                }

                Divider()

                // Historial — pasa el ViewModel como ObservedObject
                HistorialView(viewModel: viewModel)
            }
            .padding()
            .navigationTitle("Contador")
        }
    }
}
```

---

## 3. @ObservedObject — El Observador

Usa `@ObservedObject` en vistas **hijas** que reciben el ViewModel pero no lo crean:

```swift
struct HistorialView: View {
    // @ObservedObject — recibe el ViewModel, NO lo crea
    @ObservedObject var viewModel: ContadorViewModel

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack {
                Text("Historial")
                    .font(.headline)
                Spacer()
                if !viewModel.historial.isEmpty {
                    Button("Limpiar") {
                        viewModel.historial.removeAll()
                    }
                    .font(.caption)
                    .foregroundStyle(.red)
                }
            }

            if viewModel.historial.isEmpty {
                Text("Sin acciones todavía")
                    .font(.caption)
                    .foregroundStyle(.secondary)
                    .frame(maxWidth: .infinity, alignment: .center)
                    .padding()
            } else {
                ScrollView {
                    LazyVStack(alignment: .leading, spacing: 6) {
                        ForEach(viewModel.historial.reversed(), id: \.self) { entrada in
                            Text(entrada)
                                .font(.caption)
                                .padding(.horizontal, 10)
                                .padding(.vertical, 6)
                                .background(.gray.opacity(0.1))
                                .clipShape(RoundedRectangle(cornerRadius: 8))
                        }
                    }
                }
                .frame(maxHeight: 200)
            }
        }
        .padding()
        .background(.gray.opacity(0.05))
        .clipShape(RoundedRectangle(cornerRadius: 12))
    }
}
```

---

## 4. Diferencia Clave: @StateObject vs @ObservedObject

```
┌─────────────────────────────────────────────────────┐
│  @StateObject                                        │
│  • La vista CREA el objeto                           │
│  • SwiftUI lo mantiene vivo aunque la vista          │
│    se reconstruya                                    │
│  • Úsalo en la vista "raíz" del ViewModel            │
│                                                      │
│  @ObservedObject                                     │
│  • La vista RECIBE el objeto creado por otra vista   │
│  • Si la vista padre se reconstruye, el objeto       │
│    puede recrearse ⚠️                               │
│  • Úsalo en vistas hijas                             │
└─────────────────────────────────────────────────────┘

// ✅ Correcto
struct VistaPadre: View {
    @StateObject private var vm = MiViewModel()   // CREA
    var body: some View {
        VistaHija(vm: vm)
    }
}

struct VistaHija: View {
    @ObservedObject var vm: MiViewModel   // RECIBE
}

// ❌ Incorrecto — se recreará con cada rebuild
struct VistaHija: View {
    @StateObject private var vm = MiViewModel()  // ← no debería crearlo aquí
}
```

---

## 5. Proyecto: App de Notas con ViewModel

```swift
import SwiftUI

// MARK: - Modelo
struct Nota: Identifiable, Codable {
    let id: UUID
    var titulo: String
    var contenido: String
    var fecha: Date
    var color: String

    init(titulo: String, contenido: String = "", color: String = "blue") {
        self.id = UUID()
        self.titulo = titulo
        self.contenido = contenido
        self.fecha = Date()
        self.color = color
    }

    var colorSwiftUI: Color {
        switch color {
        case "blue":   return .blue
        case "green":  return .green
        case "orange": return .orange
        case "purple": return .purple
        case "red":    return .red
        default:       return .blue
        }
    }
}

// MARK: - ViewModel
class NotasViewModel: ObservableObject {
    @Published var notas: [Nota] = []
    @Published var busqueda = ""
    @Published var ordenAscendente = true

    var notasFiltradas: [Nota] {
        let filtradas = busqueda.isEmpty
            ? notas
            : notas.filter {
                $0.titulo.localizedCaseInsensitiveContains(busqueda) ||
                $0.contenido.localizedCaseInsensitiveContains(busqueda)
            }
        return filtradas.sorted {
            ordenAscendente
                ? $0.fecha < $1.fecha
                : $0.fecha > $1.fecha
        }
    }

    var totalNotas: Int { notas.count }

    func agregarNota(titulo: String, contenido: String, color: String) {
        let nueva = Nota(titulo: titulo, contenido: contenido, color: color)
        notas.append(nueva)
    }

    func eliminarNota(_ nota: Nota) {
        notas.removeAll { $0.id == nota.id }
    }

    func actualizarNota(_ nota: Nota, titulo: String, contenido: String) {
        if let index = notas.firstIndex(where: { $0.id == nota.id }) {
            notas[index].titulo = titulo
            notas[index].contenido = contenido
        }
    }

    func toggleOrden() {
        ordenAscendente.toggle()
    }
}

// MARK: - Vista Principal
struct NotasView: View {
    @StateObject private var viewModel = NotasViewModel()
    @State private var mostrarNuevaNota = false

    let colores = ["blue", "green", "orange", "purple", "red"]

    var body: some View {
        NavigationStack {
            Group {
                if viewModel.notasFiltradas.isEmpty {
                    VacioView(mostrarNuevaNota: $mostrarNuevaNota)
                } else {
                    ListaNotasView(viewModel: viewModel)
                }
            }
            .navigationTitle("📝 Mis Notas")
            .searchable(text: $viewModel.busqueda,
                       prompt: "Buscar notas...")
            .toolbar {
                ToolbarItem(placement: .topBarLeading) {
                    Button {
                        viewModel.toggleOrden()
                    } label: {
                        Image(systemName: viewModel.ordenAscendente
                              ? "arrow.up.circle" : "arrow.down.circle")
                    }
                }
                ToolbarItem(placement: .topBarTrailing) {
                    Button {
                        mostrarNuevaNota = true
                    } label: {
                        Image(systemName: "square.and.pencil")
                    }
                }
            }
            .sheet(isPresented: $mostrarNuevaNota) {
                NuevaNoteView(viewModel: viewModel)
            }
        }
    }
}

// MARK: - Lista de Notas
struct ListaNotasView: View {
    @ObservedObject var viewModel: NotasViewModel

    var body: some View {
        List {
            ForEach(viewModel.notasFiltradas) { nota in
                NavigationLink {
                    DetalleNotaView(nota: nota, viewModel: viewModel)
                } label: {
                    FilaNotaView(nota: nota)
                }
                .swipeActions(edge: .trailing) {
                    Button(role: .destructive) {
                        viewModel.eliminarNota(nota)
                    } label: {
                        Label("Eliminar", systemImage: "trash")
                    }
                }
            }
        }
    }
}

// MARK: - Fila de Nota
struct FilaNotaView: View {
    let nota: Nota

    var body: some View {
        HStack(spacing: 12) {
            RoundedRectangle(cornerRadius: 8)
                .fill(nota.colorSwiftUI.gradient)
                .frame(width: 6)
                .frame(height: 50)

            VStack(alignment: .leading, spacing: 4) {
                Text(nota.titulo)
                    .fontWeight(.medium)
                    .lineLimit(1)

                Text(nota.contenido.isEmpty ? "Sin contenido" : nota.contenido)
                    .font(.caption)
                    .foregroundStyle(.secondary)
                    .lineLimit(2)
            }

            Spacer()

            Text(nota.fecha, style: .date)
                .font(.caption2)
                .foregroundStyle(.tertiary)
        }
        .padding(.vertical, 4)
    }
}

// MARK: - Nueva Nota
struct NuevaNoteView: View {
    @ObservedObject var viewModel: NotasViewModel
    @Environment(\.dismiss) private var dismiss

    @State private var titulo = ""
    @State private var contenido = ""
    @State private var colorSeleccionado = "blue"

    let colores = ["blue", "green", "orange", "purple", "red"]

    var body: some View {
        NavigationStack {
            Form {
                Section("Título") {
                    TextField("Título de la nota", text: $titulo)
                }

                Section("Contenido") {
                    TextEditor(text: $contenido)
                        .frame(minHeight: 150)
                }

                Section("Color") {
                    HStack(spacing: 12) {
                        ForEach(colores, id: \.self) { color in
                            let c = colorDesdeNombre(color)
                            Circle()
                                .fill(c)
                                .frame(width: 36, height: 36)
                                .overlay(
                                    Image(systemName: "checkmark")
                                        .foregroundStyle(.white)
                                        .opacity(colorSeleccionado == color ? 1 : 0)
                                )
                                .onTapGesture {
                                    colorSeleccionado = color
                                }
                        }
                    }
                    .padding(.vertical, 4)
                }
            }
            .navigationTitle("Nueva Nota")
            .navigationBarTitleDisplayMode(.inline)
            .toolbar {
                ToolbarItem(placement: .cancellationAction) {
                    Button("Cancelar") { dismiss() }
                }
                ToolbarItem(placement: .confirmationAction) {
                    Button("Guardar") {
                        viewModel.agregarNota(
                            titulo: titulo,
                            contenido: contenido,
                            color: colorSeleccionado
                        )
                        dismiss()
                    }
                    .disabled(titulo.isEmpty)
                }
            }
        }
    }

    func colorDesdeNombre(_ nombre: String) -> Color {
        switch nombre {
        case "blue":   return .blue
        case "green":  return .green
        case "orange": return .orange
        case "purple": return .purple
        default:       return .red
        }
    }
}

// MARK: - Detalle de Nota
struct DetalleNotaView: View {
    let nota: Nota
    @ObservedObject var viewModel: NotasViewModel
    @State private var titulo: String
    @State private var contenido: String
    @State private var editando = false

    init(nota: Nota, viewModel: NotasViewModel) {
        self.nota = nota
        self.viewModel = viewModel
        self._titulo = State(initialValue: nota.titulo)
        self._contenido = State(initialValue: nota.contenido)
    }

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 16) {
                if editando {
                    TextField("Título", text: $titulo)
                        .font(.title2.bold())
                        .textFieldStyle(.roundedBorder)
                    TextEditor(text: $contenido)
                        .frame(minHeight: 200)
                        .overlay(
                            RoundedRectangle(cornerRadius: 8)
                                .stroke(.gray.opacity(0.3))
                        )
                } else {
                    Text(titulo)
                        .font(.title2.bold())
                    Text(contenido.isEmpty ? "Sin contenido" : contenido)
                        .foregroundStyle(contenido.isEmpty ? .secondary : .primary)
                }

                Text(nota.fecha, style: .date)
                    .font(.caption)
                    .foregroundStyle(.secondary)
            }
            .padding()
        }
        .navigationBarTitleDisplayMode(.inline)
        .toolbar {
            ToolbarItem(placement: .topBarTrailing) {
                Button(editando ? "Guardar" : "Editar") {
                    if editando {
                        viewModel.actualizarNota(nota,
                                                titulo: titulo,
                                                contenido: contenido)
                    }
                    editando.toggle()
                }
            }
        }
    }
}

// MARK: - Vista Vacía
struct VacioView: View {
    @Binding var mostrarNuevaNota: Bool

    var body: some View {
        VStack(spacing: 16) {
            Image(systemName: "note.text")
                .font(.system(size: 60))
                .foregroundStyle(.secondary)
            Text("Sin notas")
                .font(.title2.bold())
            Text("Crea tu primera nota tocando el botón")
                .foregroundStyle(.secondary)
                .multilineTextAlignment(.center)
            Button("Crear Nota") { mostrarNuevaNota = true }
                .buttonStyle(.borderedProminent)
        }
        .padding()
    }
}

#Preview {
    NotasView()
}
```

---

## ✅ Resumen

| Wrapper | Quién lo usa | Cuándo |
|---|---|---|
| `@StateObject` | Vista propietaria | Crea el ViewModel |
| `@ObservedObject` | Vista hija | Recibe el ViewModel |
| `@Published` | ViewModel | Propiedades que actualizan la UI |
| `ObservableObject` | ViewModel | Protocolo que habilita la observación |

---

⬅️ [01 — @State y @Binding](./01-state-binding.md) | ➡️ [03 — @EnvironmentObject](./03-environmentobject.md)

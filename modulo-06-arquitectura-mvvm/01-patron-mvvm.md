# 🏗️ Patrón MVVM en SwiftUI

## 🎯 Lo que aprenderás

- Qué es MVVM y por qué usarlo
- Model, View y ViewModel en SwiftUI
- Separación de responsabilidades
- Estructura de carpetas recomendada
- Proyecto: App de Películas con MVVM

---

## 1. ¿Qué es MVVM?

**MVVM** (Model-View-ViewModel) es un patrón de arquitectura que separa tu código en tres capas:

```
┌─────────────────────────────────────────────────┐
│                    MVVM                          │
│                                                  │
│  MODEL          VIEW MODEL         VIEW          │
│  ┌──────┐      ┌──────────┐      ┌──────┐       │
│  │Datos │ ───► │ Lógica   │ ───► │  UI  │       │
│  │Struct│      │ @Published│     │SwiftUI│       │
│  └──────┘      └──────────┘      └──────┘       │
│                     ▲                 │          │
│                     └─────────────────┘          │
│                    Acciones del usuario           │
└─────────────────────────────────────────────────┘
```

| Capa | Responsabilidad | En Swift |
|---|---|---|
| **Model** | Datos y reglas de negocio | `struct`, `enum` |
| **ViewModel** | Lógica, estado, llamadas a red | `class ObservableObject` |
| **View** | Solo mostrar la UI | `struct View` |

---

## 2. Estructura de Carpetas Recomendada

```
MiApp/
├── App/
│   └── MiAppApp.swift
├── Models/
│   ├── Pelicula.swift
│   └── Genero.swift
├── ViewModels/
│   ├── PeliculasViewModel.swift
│   └── DetallePeliculaViewModel.swift
├── Views/
│   ├── Peliculas/
│   │   ├── PeliculasView.swift
│   │   ├── FilaPelicula.swift
│   │   └── DetallePeliculaView.swift
│   └── Shared/
│       ├── TarjetaView.swift
│       └── ErrorView.swift
├── Services/
│   └── PeliculasService.swift
└── Utilities/
    └── Extensions.swift
```

---

## 3. Proyecto: App de Películas con MVVM

```swift
import SwiftUI

// MARK: - MODELS
struct Pelicula: Identifiable, Codable, Hashable {
    let id: Int
    var titulo: String
    var director: String
    var año: Int
    var calificacion: Double
    var genero: Genero
    var sinopsis: String
    var duracionMinutos: Int
    var esFavorita: Bool = false

    enum Genero: String, Codable, CaseIterable {
        case accion = "Acción"
        case drama = "Drama"
        case comedia = "Comedia"
        case cienciaFiccion = "Ciencia Ficción"
        case terror = "Terror"
        case animacion = "Animación"

        var icono: String {
            switch self {
            case .accion:        return "bolt.fill"
            case .drama:         return "theatermasks"
            case .comedia:       return "face.smiling"
            case .cienciaFiccion: return "sparkles"
            case .terror:        return "moon.fill"
            case .animacion:     return "paintpalette"
            }
        }

        var color: Color {
            switch self {
            case .accion:        return .red
            case .drama:         return .purple
            case .comedia:       return .yellow
            case .cienciaFiccion: return .blue
            case .terror:        return .gray
            case .animacion:     return .orange
            }
        }
    }

    var duracionFormateada: String {
        let horas = duracionMinutos / 60
        let minutos = duracionMinutos % 60
        return horas > 0 ? "\(horas)h \(minutos)min" : "\(minutos)min"
    }

    var estrellasFormateadas: String {
        String(format: "%.1f", calificacion)
    }
}

// MARK: - SERVICE (Datos o Red)
class PeliculasService {
    static let shared = PeliculasService()
    private init() {}

    // Simulamos datos — en una app real llamaríamos a una API
    func obtenerPeliculas() async throws -> [Pelicula] {
        // Simulamos un pequeño delay de red
        try await Task.sleep(for: .seconds(1))
        return [
            Pelicula(id: 1, titulo: "Inception", director: "Christopher Nolan",
                     año: 2010, calificacion: 8.8, genero: .cienciaFiccion,
                     sinopsis: "Un ladrón roba secretos a través de los sueños.",
                     duracionMinutos: 148),
            Pelicula(id: 2, titulo: "El Padrino", director: "Francis Ford Coppola",
                     año: 1972, calificacion: 9.2, genero: .drama,
                     sinopsis: "La saga de la familia mafiosa Corleone.",
                     duracionMinutos: 175),
            Pelicula(id: 3, titulo: "Toy Story", director: "John Lasseter",
                     año: 1995, calificacion: 8.3, genero: .animacion,
                     sinopsis: "Los juguetes cobran vida cuando los humanos no están.",
                     duracionMinutos: 81),
            Pelicula(id: 4, titulo: "Interstellar", director: "Christopher Nolan",
                     año: 2014, calificacion: 8.6, genero: .cienciaFiccion,
                     sinopsis: "Un viaje a través de un agujero de gusano.",
                     duracionMinutos: 169),
            Pelicula(id: 5, titulo: "Coco", director: "Lee Unkrich",
                     año: 2017, calificacion: 8.4, genero: .animacion,
                     sinopsis: "Un niño viaja a la tierra de los muertos.",
                     duracionMinutos: 105)
        ]
    }

    func toggleFavorita(_ pelicula: inout Pelicula) {
        pelicula.esFavorita.toggle()
    }
}

// MARK: - VIEW MODEL
@MainActor
class PeliculasViewModel: ObservableObject {
    // Estado de la UI
    @Published var peliculas: [Pelicula] = []
    @Published var cargando = false
    @Published var error: String?
    @Published var busqueda = ""
    @Published var generoSeleccionado: Pelicula.Genero?
    @Published var soloFavoritas = false
    @Published var ordenamiento: Ordenamiento = .calificacion

    enum Ordenamiento: String, CaseIterable {
        case titulo = "Título"
        case calificacion = "Calificación"
        case año = "Año"
    }

    private let servicio = PeliculasService.shared

    // Peliculas filtradas y ordenadas (propiedad computada)
    var peliculasFiltradas: [Pelicula] {
        var resultado = peliculas

        // Filtro de búsqueda
        if !busqueda.isEmpty {
            resultado = resultado.filter {
                $0.titulo.localizedCaseInsensitiveContains(busqueda) ||
                $0.director.localizedCaseInsensitiveContains(busqueda)
            }
        }

        // Filtro de género
        if let genero = generoSeleccionado {
            resultado = resultado.filter { $0.genero == genero }
        }

        // Filtro de favoritas
        if soloFavoritas {
            resultado = resultado.filter { $0.esFavorita }
        }

        // Ordenamiento
        switch ordenamiento {
        case .titulo:
            resultado.sort { $0.titulo < $1.titulo }
        case .calificacion:
            resultado.sort { $0.calificacion > $1.calificacion }
        case .año:
            resultado.sort { $0.año > $1.año }
        }

        return resultado
    }

    var totalFavoritas: Int { peliculas.filter(\.esFavorita).count }

    // MARK: - Intenciones (lo que la vista puede pedir)
    func cargarPeliculas() async {
        cargando = true
        error = nil

        do {
            peliculas = try await servicio.obtenerPeliculas()
        } catch {
            self.error = "No se pudieron cargar las películas."
        }

        cargando = false
    }

    func toggleFavorita(_ pelicula: Pelicula) {
        guard let index = peliculas.firstIndex(where: { $0.id == pelicula.id })
        else { return }
        peliculas[index].esFavorita.toggle()
    }

    func limpiarFiltros() {
        busqueda = ""
        generoSeleccionado = nil
        soloFavoritas = false
        ordenamiento = .calificacion
    }
}

// MARK: - VIEWS

// Vista Principal
struct PeliculasView: View {
    @StateObject private var viewModel = PeliculasViewModel()

    var body: some View {
        NavigationStack {
            Group {
                if viewModel.cargando {
                    VistasCargando()
                } else if let error = viewModel.error {
                    VistaError(mensaje: error) {
                        Task { await viewModel.cargarPeliculas() }
                    }
                } else if viewModel.peliculasFiltradas.isEmpty {
                    VistaSinResultados {
                        viewModel.limpiarFiltros()
                    }
                } else {
                    ListaPeliculasView(viewModel: viewModel)
                }
            }
            .navigationTitle("🎬 Películas")
            .searchable(text: $viewModel.busqueda,
                       prompt: "Buscar título o director...")
            .toolbar {
                FiltrosToolbar(viewModel: viewModel)
            }
            .task {
                if viewModel.peliculas.isEmpty {
                    await viewModel.cargarPeliculas()
                }
            }
            .refreshable {
                await viewModel.cargarPeliculas()
            }
        }
    }
}

// Lista de Películas
struct ListaPeliculasView: View {
    @ObservedObject var viewModel: PeliculasViewModel

    var body: some View {
        List(viewModel.peliculasFiltradas) { pelicula in
            NavigationLink(value: pelicula) {
                FilaPelicula(pelicula: pelicula) {
                    viewModel.toggleFavorita(pelicula)
                }
            }
        }
        .navigationDestination(for: Pelicula.self) { pelicula in
            DetallePeliculaView(
                pelicula: pelicula,
                alToggleFavorita: { viewModel.toggleFavorita(pelicula) }
            )
        }
    }
}

// Fila de película
struct FilaPelicula: View {
    let pelicula: Pelicula
    let alToggleFavorita: () -> Void

    var body: some View {
        HStack(spacing: 12) {
            // Póster simulado
            RoundedRectangle(cornerRadius: 8)
                .fill(pelicula.genero.color.gradient)
                .frame(width: 56, height: 80)
                .overlay(
                    Image(systemName: pelicula.genero.icono)
                        .font(.title2)
                        .foregroundStyle(.white)
                )

            VStack(alignment: .leading, spacing: 5) {
                Text(pelicula.titulo)
                    .fontWeight(.semibold)
                    .lineLimit(1)

                Text(pelicula.director)
                    .font(.caption)
                    .foregroundStyle(.secondary)

                HStack(spacing: 8) {
                    Label(pelicula.estrellasFormateadas,
                          systemImage: "star.fill")
                        .font(.caption)
                        .foregroundStyle(.orange)

                    Text("·")
                        .foregroundStyle(.tertiary)

                    Text("\(pelicula.año)")
                        .font(.caption)
                        .foregroundStyle(.secondary)

                    Text("·")
                        .foregroundStyle(.tertiary)

                    Text(pelicula.duracionFormateada)
                        .font(.caption)
                        .foregroundStyle(.secondary)
                }

                Text(pelicula.genero.rawValue)
                    .font(.caption2)
                    .padding(.horizontal, 8)
                    .padding(.vertical, 3)
                    .background(pelicula.genero.color.opacity(0.15))
                    .foregroundStyle(pelicula.genero.color)
                    .clipShape(Capsule())
            }

            Spacer()

            Button {
                withAnimation { alToggleFavorita() }
            } label: {
                Image(systemName: pelicula.esFavorita
                      ? "heart.fill" : "heart")
                    .foregroundStyle(pelicula.esFavorita ? .red : .gray)
            }
            .buttonStyle(.plain)
        }
        .padding(.vertical, 6)
    }
}

// Detalle
struct DetallePeliculaView: View {
    let pelicula: Pelicula
    let alToggleFavorita: () -> Void

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 20) {
                ZStack {
                    RoundedRectangle(cornerRadius: 20)
                        .fill(pelicula.genero.color.gradient)
                        .frame(height: 220)
                    VStack(spacing: 12) {
                        Image(systemName: pelicula.genero.icono)
                            .font(.system(size: 70))
                            .foregroundStyle(.white)
                        Text(pelicula.genero.rawValue)
                            .font(.caption)
                            .foregroundStyle(.white.opacity(0.8))
                            .padding(.horizontal, 12)
                            .padding(.vertical, 4)
                            .background(.white.opacity(0.2))
                            .clipShape(Capsule())
                    }
                }
                .padding(.horizontal)

                VStack(alignment: .leading, spacing: 16) {
                    HStack {
                        Label(pelicula.estrellasFormateadas,
                              systemImage: "star.fill")
                            .foregroundStyle(.orange)
                        Spacer()
                        Text(pelicula.duracionFormateada)
                            .foregroundStyle(.secondary)
                        Text("·")
                            .foregroundStyle(.tertiary)
                        Text("\(pelicula.año)")
                            .foregroundStyle(.secondary)
                    }
                    .font(.subheadline)

                    Divider()

                    VStack(alignment: .leading, spacing: 6) {
                        Text("Director")
                            .font(.caption)
                            .foregroundStyle(.secondary)
                            .textCase(.uppercase)
                        Text(pelicula.director)
                            .fontWeight(.medium)
                    }

                    Divider()

                    VStack(alignment: .leading, spacing: 6) {
                        Text("Sinopsis")
                            .font(.caption)
                            .foregroundStyle(.secondary)
                            .textCase(.uppercase)
                        Text(pelicula.sinopsis)
                            .foregroundStyle(.secondary)
                    }
                }
                .padding(.horizontal)
            }
        }
        .navigationTitle(pelicula.titulo)
        .navigationBarTitleDisplayMode(.large)
        .toolbar {
            ToolbarItem(placement: .topBarTrailing) {
                Button {
                    withAnimation { alToggleFavorita() }
                } label: {
                    Image(systemName: pelicula.esFavorita
                          ? "heart.fill" : "heart")
                        .foregroundStyle(pelicula.esFavorita ? .red : .gray)
                }
            }
        }
    }
}

// Toolbar de filtros
struct FiltrosToolbar: ToolbarContent {
    @ObservedObject var viewModel: PeliculasViewModel

    var body: some ToolbarContent {
        ToolbarItem(placement: .topBarTrailing) {
            Menu {
                // Ordenamiento
                Section("Ordenar por") {
                    ForEach(PeliculasViewModel.Ordenamiento.allCases,
                            id: \.self) { orden in
                        Button {
                            viewModel.ordenamiento = orden
                        } label: {
                            Label(orden.rawValue,
                                  systemImage: viewModel.ordenamiento == orden
                                  ? "checkmark" : "")
                        }
                    }
                }

                Divider()

                // Filtros
                Button {
                    viewModel.soloFavoritas.toggle()
                } label: {
                    Label("Solo favoritas",
                          systemImage: viewModel.soloFavoritas
                          ? "heart.fill" : "heart")
                }

                if viewModel.generoSeleccionado != nil
                    || viewModel.soloFavoritas {
                    Button("Limpiar filtros", role: .destructive) {
                        viewModel.limpiarFiltros()
                    }
                }
            } label: {
                Image(systemName: "line.3.horizontal.decrease.circle")
            }
        }
    }
}

// Vistas de estado
struct VistasCargando: View {
    var body: some View {
        VStack(spacing: 16) {
            ProgressView().scaleEffect(1.5)
            Text("Cargando películas...")
                .foregroundStyle(.secondary)
        }
    }
}

struct VistaError: View {
    let mensaje: String
    let alReintentar: () -> Void

    var body: some View {
        VStack(spacing: 16) {
            Image(systemName: "exclamationmark.triangle")
                .font(.system(size: 50))
                .foregroundStyle(.orange)
            Text(mensaje)
                .multilineTextAlignment(.center)
                .foregroundStyle(.secondary)
            Button("Reintentar", action: alReintentar)
                .buttonStyle(.borderedProminent)
        }
        .padding()
    }
}

struct VistaSinResultados: View {
    let alLimpiar: () -> Void

    var body: some View {
        VStack(spacing: 16) {
            Image(systemName: "magnifyingglass")
                .font(.system(size: 50))
                .foregroundStyle(.secondary)
            Text("Sin resultados")
                .font(.title3)
            Button("Limpiar filtros", action: alLimpiar)
                .buttonStyle(.bordered)
        }
    }
}

#Preview {
    PeliculasView()
}
```

---

## ✅ Resumen — Reglas MVVM en SwiftUI

| ✅ Haz esto | ❌ Evita esto |
|---|---|
| Lógica en el ViewModel | Lógica en la View |
| Views simples y declarativas | Views con if-let complejos |
| @StateObject en vista raíz | @StateObject en vistas hijas |
| Un ViewModel por pantalla | Un ViewModel para todo |
| Modelos como struct | Modelos como class |

---

⬅️ [Módulo 05 — Alerts y Sheets](../modulo-05-interaccion-usuario/02-alerts-sheets.md) | ➡️ [02 — Código Modular](./02-codigo-modular.md)

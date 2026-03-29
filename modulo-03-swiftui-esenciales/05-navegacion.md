# 🗺️ Navegación en SwiftUI

## 🎯 Lo que aprenderás

- `NavigationStack` moderno (iOS 16+)
- `NavigationLink` para navegar entre vistas
- Pasar datos entre vistas
- `navigationTitle` y `toolbar`
- `sheet()` y `fullScreenCover()`
- Proyecto: App de 3 pantallas

---

## 1. NavigationStack

```swift
import SwiftUI

struct AppPrincipal: View {
    var body: some View {
        NavigationStack {
            VStack {
                Text("Pantalla Principal")
                    .font(.title)

                NavigationLink("Ir a Detalles") {
                    PantallaDetalle()
                }
                .buttonStyle(.borderedProminent)
            }
            .navigationTitle("Inicio")
            .navigationBarTitleDisplayMode(.large)
        }
    }
}

struct PantallaDetalle: View {
    var body: some View {
        Text("¡Estás en Detalles!")
            .font(.title)
            .navigationTitle("Detalles")
            .navigationBarTitleDisplayMode(.inline)
    }
}
```

---

## 2. NavigationLink con Datos

```swift
struct Pelicula: Identifiable {
    let id = UUID()
    var titulo: String
    var director: String
    var año: Int
    var calificacion: Double
    var sinopsis: String
    var genero: String
}

struct ListaPeliculas: View {
    let peliculas = [
        Pelicula(titulo: "El Padrino", director: "Coppola",
                 año: 1972, calificacion: 9.2,
                 sinopsis: "La saga de la familia Corleone.",
                 genero: "Drama"),
        Pelicula(titulo: "Interstellar", director: "Nolan",
                 año: 2014, calificacion: 8.6,
                 sinopsis: "Un viaje a través del espacio-tiempo.",
                 genero: "Ciencia Ficción"),
        Pelicula(titulo: "El Señor de los Anillos", director: "Jackson",
                 año: 2001, calificacion: 8.8,
                 sinopsis: "La batalla por la Tierra Media.",
                 genero: "Fantasía")
    ]

    var body: some View {
        NavigationStack {
            List(peliculas) { pelicula in
                // NavigationLink pasa la película a la vista destino
                NavigationLink(value: pelicula) {
                    HStack {
                        VStack(alignment: .leading, spacing: 4) {
                            Text(pelicula.titulo)
                                .fontWeight(.semibold)
                            Text(pelicula.director)
                                .font(.caption)
                                .foregroundStyle(.secondary)
                        }
                        Spacer()
                        HStack(spacing: 2) {
                            Image(systemName: "star.fill")
                                .foregroundStyle(.yellow)
                                .font(.caption)
                            Text(String(format: "%.1f", pelicula.calificacion))
                                .font(.caption)
                        }
                    }
                    .padding(.vertical, 4)
                }
            }
            .navigationTitle("🎬 Películas")
            // Definir el destino para el tipo Pelicula
            .navigationDestination(for: Pelicula.self) { pelicula in
                DetallePelicula(pelicula: pelicula)
            }
        }
    }
}

struct DetallePelicula: View {
    let pelicula: Pelicula

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 20) {
                // Header
                ZStack {
                    RoundedRectangle(cornerRadius: 16)
                        .fill(.blue.gradient)
                        .frame(height: 200)
                    VStack {
                        Image(systemName: "film")
                            .font(.system(size: 60))
                            .foregroundStyle(.white)
                        Text(pelicula.genero)
                            .font(.caption)
                            .foregroundStyle(.white.opacity(0.8))
                            .padding(.horizontal, 12)
                            .padding(.vertical, 4)
                            .background(.white.opacity(0.2))
                            .clipShape(Capsule())
                    }
                }

                // Info
                VStack(alignment: .leading, spacing: 12) {
                    HStack {
                        InfoChip(icono: "person.fill",
                                 texto: pelicula.director)
                        InfoChip(icono: "calendar",
                                 texto: "\(pelicula.año)")
                        InfoChip(icono: "star.fill",
                                 texto: String(format: "%.1f",
                                              pelicula.calificacion))
                    }

                    Text("Sinopsis")
                        .font(.headline)

                    Text(pelicula.sinopsis)
                        .foregroundStyle(.secondary)
                }
                .padding(.horizontal)
            }
        }
        .navigationTitle(pelicula.titulo)
        .navigationBarTitleDisplayMode(.large)
        .toolbar {
            ToolbarItem(placement: .topBarTrailing) {
                Button {
                } label: {
                    Image(systemName: "heart")
                }
            }
        }
    }
}

struct InfoChip: View {
    var icono: String
    var texto: String

    var body: some View {
        HStack(spacing: 4) {
            Image(systemName: icono)
                .font(.caption)
            Text(texto)
                .font(.caption)
        }
        .padding(.horizontal, 10)
        .padding(.vertical, 6)
        .background(.gray.opacity(0.12))
        .clipShape(Capsule())
    }
}
```

---

## 3. NavigationStack con Path (Navegación Programática)

```swift
struct AppConPath: View {
    @State private var path = NavigationPath()

    var body: some View {
        NavigationStack(path: $path) {
            VStack(spacing: 20) {
                Text("Pantalla 1")
                    .font(.largeTitle)

                Button("Ir a Pantalla 2") {
                    path.append("pantalla2")
                }
                .buttonStyle(.borderedProminent)

                Button("Ir directo a Pantalla 3") {
                    path.append("pantalla2")
                    path.append("pantalla3")
                }
                .buttonStyle(.bordered)

                Button("Volver al inicio") {
                    path.removeLast(path.count)
                }
                .buttonStyle(.bordered)
                .tint(.red)
                .disabled(path.isEmpty)
            }
            .navigationTitle("Inicio")
            .navigationDestination(for: String.self) { pantalla in
                switch pantalla {
                case "pantalla2":
                    Pantalla2(path: $path)
                case "pantalla3":
                    Pantalla3(path: $path)
                default:
                    Text("Pantalla desconocida")
                }
            }
        }
    }
}

struct Pantalla2: View {
    @Binding var path: NavigationPath

    var body: some View {
        VStack(spacing: 20) {
            Text("Pantalla 2").font(.largeTitle)
            Button("Ir a Pantalla 3") { path.append("pantalla3") }
                .buttonStyle(.borderedProminent)
            Button("Volver al inicio") { path.removeLast(path.count) }
                .buttonStyle(.bordered).tint(.red)
        }
        .navigationTitle("Pantalla 2")
    }
}

struct Pantalla3: View {
    @Binding var path: NavigationPath

    var body: some View {
        VStack(spacing: 20) {
            Text("Pantalla 3").font(.largeTitle)
            Text("¡Llegaste al final!").foregroundStyle(.secondary)
            Button("Volver al inicio") { path.removeLast(path.count) }
                .buttonStyle(.borderedProminent).tint(.red)
        }
        .navigationTitle("Pantalla 3")
    }
}
```

---

## 4. Sheet y FullScreenCover

```swift
struct EjemploSheets: View {
    @State private var mostrarSheet = false
    @State private var mostrarFullScreen = false

    var body: some View {
        VStack(spacing: 20) {
            Button("Mostrar Sheet") {
                mostrarSheet = true
            }
            .buttonStyle(.borderedProminent)

            Button("Mostrar Full Screen") {
                mostrarFullScreen = true
            }
            .buttonStyle(.bordered)
        }
        // Sheet — se desliza desde abajo, se puede cerrar con swipe
        .sheet(isPresented: $mostrarSheet) {
            SheetContenido()
        }
        // FullScreenCover — ocupa toda la pantalla
        .fullScreenCover(isPresented: $mostrarFullScreen) {
            FullScreenContenido(mostrar: $mostrarFullScreen)
        }
    }
}

struct SheetContenido: View {
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        NavigationStack {
            VStack {
                Text("Soy un Sheet")
                    .font(.title)
                Text("Desliza hacia abajo para cerrar")
                    .foregroundStyle(.secondary)
            }
            .navigationTitle("Sheet")
            .navigationBarTitleDisplayMode(.inline)
            .toolbar {
                ToolbarItem(placement: .topBarTrailing) {
                    Button("Cerrar") { dismiss() }
                }
            }
        }
    }
}

struct FullScreenContenido: View {
    @Binding var mostrar: Bool

    var body: some View {
        ZStack {
            Color.blue.ignoresSafeArea()
            VStack(spacing: 20) {
                Text("Full Screen Cover")
                    .font(.largeTitle)
                    .foregroundStyle(.white)
                Button("Cerrar") { mostrar = false }
                    .buttonStyle(.bordered)
                    .tint(.white)
            }
        }
    }
}
```

---

## 5. Toolbar Personalizado

```swift
struct VistaConToolbar: View {
    @State private var busqueda = ""

    var body: some View {
        NavigationStack {
            List(1...20, id: \.self) { i in
                Text("Elemento \(i)")
            }
            .navigationTitle("Mi Lista")
            .searchable(text: $busqueda, prompt: "Buscar...")
            .toolbar {
                // Botón en la esquina superior derecha
                ToolbarItem(placement: .topBarTrailing) {
                    Button {
                    } label: {
                        Image(systemName: "plus")
                    }
                }

                // Botón en la esquina superior izquierda
                ToolbarItem(placement: .topBarLeading) {
                    Button("Editar") { }
                }

                // Barra inferior
                ToolbarItemGroup(placement: .bottomBar) {
                    Button {
                    } label: {
                        Label("Inicio", systemImage: "house")
                    }
                    Spacer()
                    Button {
                    } label: {
                        Label("Favoritos", systemImage: "star")
                    }
                    Spacer()
                    Button {
                    } label: {
                        Label("Perfil", systemImage: "person")
                    }
                }
            }
        }
    }
}
```

---

## 6. 🛠️ Proyecto: App de 3 Pantallas

```swift
import SwiftUI

// MARK: - Modelo
struct Curso: Identifiable {
    let id = UUID()
    var titulo: String
    var descripcion: String
    var icono: String
    var color: Color
    var lecciones: [Leccion]
    var completado: Bool = false
}

struct Leccion: Identifiable {
    let id = UUID()
    var titulo: String
    var duracion: String
    var completada: Bool = false
}

// MARK: - Datos de ejemplo
extension Curso {
    static let ejemplos = [
        Curso(titulo: "Swift Básico", descripcion: "Aprende los fundamentos de Swift",
              icono: "swift", color: .orange,
              lecciones: [
                Leccion(titulo: "Variables y Constantes", duracion: "15 min"),
                Leccion(titulo: "Tipos de Datos", duracion: "20 min"),
                Leccion(titulo: "Control de Flujo", duracion: "25 min")
              ]),
        Curso(titulo: "SwiftUI", descripcion: "Crea interfaces modernas",
              icono: "iphone", color: .blue,
              lecciones: [
                Leccion(titulo: "Vistas y Modificadores", duracion: "30 min"),
                Leccion(titulo: "Stacks y Layout", duracion: "25 min"),
                Leccion(titulo: "Navegación", duracion: "35 min")
              ]),
        Curso(titulo: "Estado y Datos", descripcion: "Maneja el estado de tu app",
              icono: "arrow.triangle.2.circlepath", color: .purple,
              lecciones: [
                Leccion(titulo: "@State y @Binding", duracion: "20 min"),
                Leccion(titulo: "@ObservedObject", duracion: "25 min"),
                Leccion(titulo: "@EnvironmentObject", duracion: "20 min")
              ])
    ]
}

// MARK: - Pantalla 1: Lista de Cursos
struct PantallaCursos: View {
    @State private var cursos = Curso.ejemplos

    var body: some View {
        NavigationStack {
            List(cursos) { curso in
                NavigationLink(value: curso) {
                    FilaCurso(curso: curso)
                }
            }
            .navigationTitle("📚 Mis Cursos")
            .navigationDestination(for: Curso.self) { curso in
                PantallaLecciones(curso: curso)
            }
        }
    }
}

struct FilaCurso: View {
    let curso: Curso

    var body: some View {
        HStack(spacing: 14) {
            RoundedRectangle(cornerRadius: 12)
                .fill(curso.color.gradient)
                .frame(width: 50, height: 50)
                .overlay(
                    Image(systemName: curso.icono)
                        .foregroundStyle(.white)
                        .font(.title3)
                )

            VStack(alignment: .leading, spacing: 3) {
                Text(curso.titulo).fontWeight(.semibold)
                Text(curso.descripcion)
                    .font(.caption)
                    .foregroundStyle(.secondary)
                Text("\(curso.lecciones.count) lecciones")
                    .font(.caption2)
                    .foregroundStyle(curso.color)
            }
        }
        .padding(.vertical, 4)
    }
}

// MARK: - Pantalla 2: Lecciones
struct PantallaLecciones: View {
    let curso: Curso

    var body: some View {
        List(curso.lecciones) { leccion in
            NavigationLink(value: leccion) {
                FilaLeccion(leccion: leccion)
            }
        }
        .navigationTitle(curso.titulo)
        .navigationBarTitleDisplayMode(.inline)
        .navigationDestination(for: Leccion.self) { leccion in
            PantallaContenido(leccion: leccion, colorCurso: curso.color)
        }
    }
}

struct FilaLeccion: View {
    let leccion: Leccion

    var body: some View {
        HStack {
            Image(systemName: leccion.completada
                  ? "checkmark.circle.fill" : "play.circle")
                .foregroundStyle(leccion.completada ? .green : .blue)
                .font(.title3)

            VStack(alignment: .leading, spacing: 2) {
                Text(leccion.titulo)
                Text(leccion.duracion)
                    .font(.caption)
                    .foregroundStyle(.secondary)
            }
        }
        .padding(.vertical, 4)
    }
}

// MARK: - Pantalla 3: Contenido de Lección
struct PantallaContenido: View {
    let leccion: Leccion
    let colorCurso: Color
    @State private var progreso: Double = 0
    @State private var completada = false

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 24) {
                // Header
                ZStack {
                    RoundedRectangle(cornerRadius: 16)
                        .fill(colorCurso.gradient)
                        .frame(height: 160)
                    VStack(spacing: 8) {
                        Image(systemName: "play.circle.fill")
                            .font(.system(size: 50))
                            .foregroundStyle(.white)
                        Text(leccion.duracion)
                            .foregroundStyle(.white.opacity(0.8))
                    }
                }
                .padding(.horizontal)

                // Contenido simulado
                VStack(alignment: .leading, spacing: 16) {
                    Text("Contenido de la lección")
                        .font(.headline)

                    Text("En esta lección aprenderás los conceptos fundamentales de \(leccion.titulo). El contenido está diseñado para que puedas practicar inmediatamente lo que aprendes.")
                        .foregroundStyle(.secondary)

                    // Progreso
                    VStack(alignment: .leading, spacing: 8) {
                        HStack {
                            Text("Tu progreso")
                                .font(.subheadline)
                                .fontWeight(.medium)
                            Spacer()
                            Text("\(Int(progreso * 100))%")
                                .font(.subheadline)
                                .foregroundStyle(colorCurso)
                        }
                        ProgressView(value: progreso)
                            .tint(colorCurso)
                        Slider(value: $progreso)
                            .tint(colorCurso)
                        Text("Mueve el slider para simular tu avance")
                            .font(.caption)
                            .foregroundStyle(.secondary)
                    }
                    .padding()
                    .background(.gray.opacity(0.08))
                    .clipShape(RoundedRectangle(cornerRadius: 12))

                    // Botón completar
                    Button {
                        completada = true
                        progreso = 1.0
                    } label: {
                        Label(
                            completada ? "¡Lección Completada!" : "Marcar como completada",
                            systemImage: completada ? "checkmark.circle.fill" : "circle"
                        )
                        .frame(maxWidth: .infinity)
                        .padding()
                        .background(completada ? Color.green : colorCurso)
                        .foregroundStyle(.white)
                        .clipShape(RoundedRectangle(cornerRadius: 12))
                    }
                }
                .padding(.horizontal)
            }
            .padding(.vertical)
        }
        .navigationTitle(leccion.titulo)
        .navigationBarTitleDisplayMode(.inline)
    }
}

#Preview {
    PantallaCursos()
}
```

---

## ✅ Resumen

| Componente | Uso |
|---|---|
| `NavigationStack` | Contenedor de navegación principal |
| `NavigationLink` | Enlace a otra vista |
| `navigationDestination` | Define el destino por tipo |
| `NavigationPath` | Navegación programática |
| `.sheet` | Modal deslizable desde abajo |
| `.fullScreenCover` | Modal a pantalla completa |
| `.toolbar` | Botones en barras de navegación |
| `@Environment(\.dismiss)` | Cerrar la vista actual |

---

⬅️ [04 — Listas y Grids](./04-listas-y-grids.md) | ➡️ [Módulo 04 — @State y @Binding](../modulo-04-estado-y-datos/01-state-binding.md)

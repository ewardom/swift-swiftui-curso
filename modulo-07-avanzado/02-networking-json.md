# 🌐 Networking y JSON en SwiftUI

## 🎯 Lo que aprenderás

- `URLSession` con `async/await`
- `Codable` para decodificar JSON
- Manejo de errores de red
- Mostrar datos de una API real
- Proyecto: App de Posts con JSONPlaceholder

---

## 1. async/await — Código Asíncrono Moderno

```swift
import SwiftUI

// async/await hace el código asíncrono legible
struct VistaBasica: View {
    @State private var resultado = "Esperando..."

    var body: some View {
        Text(resultado)
            .padding()
            .task {
                do {
                    let url = URL(string:
                        "https://jsonplaceholder.typicode.com/posts/1")!
                    let (data, _) = try await URLSession.shared.data(from: url)
                    resultado = "✅ \(data.count) bytes recibidos"
                } catch {
                    resultado = "❌ \(error.localizedDescription)"
                }
            }
    }
}
```

---

## 2. Codable — Decodificar JSON

```swift
// JSON que recibiremos:
// { "userId": 1, "id": 1, "title": "...", "body": "..." }

struct Post: Codable, Identifiable {
    let userId: Int
    let id: Int
    let title: String
    let body: String
}

struct Comentario: Codable, Identifiable {
    let postId: Int
    let id: Int
    let name: String
    let email: String
    let body: String
}

// Decodificar array de posts
func cargarPosts() async throws -> [Post] {
    let url = URL(string: "https://jsonplaceholder.typicode.com/posts")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode([Post].self, from: data)
}
```

---

## 3. ViewModel con Estados de Carga

```swift
// Estados posibles de una carga
enum EstadoCarga<T> {
    case idle
    case cargando
    case exito(T)
    case error(String)
}

@MainActor
class PostsViewModel: ObservableObject {
    @Published var estado: EstadoCarga<[Post]> = .idle
    @Published var busqueda = ""

    private let baseURL = "https://jsonplaceholder.typicode.com"

    var postsFiltrados: [Post] {
        guard case .exito(let posts) = estado else { return [] }
        if busqueda.isEmpty { return posts }
        return posts.filter {
            $0.title.localizedCaseInsensitiveContains(busqueda)
        }
    }

    func cargarPosts() async {
        estado = .cargando
        do {
            let url = URL(string: "\(baseURL)/posts")!
            let (data, _) = try await URLSession.shared.data(from: url)
            let posts = try JSONDecoder().decode([Post].self, from: data)
            estado = .exito(posts)
        } catch {
            estado = .error("No se pudieron cargar los posts.")
        }
    }

    func cargarComentarios(postId: Int) async throws -> [Comentario] {
        let url = URL(string: "\(baseURL)/posts/\(postId)/comments")!
        let (data, _) = try await URLSession.shared.data(from: url)
        return try JSONDecoder().decode([Comentario].self, from: data)
    }
}
```

---

## 4. 🛠️ Proyecto: App de Posts

```swift
import SwiftUI

// MARK: - Vista Principal
struct PostsView: View {
    @StateObject private var viewModel = PostsViewModel()

    var body: some View {
        NavigationStack {
            Group {
                switch viewModel.estado {
                case .idle:
                    Color.clear.onAppear {
                        Task { await viewModel.cargarPosts() }
                    }

                case .cargando:
                    VStack(spacing: 16) {
                        ProgressView().scaleEffect(1.5)
                        Text("Cargando posts...")
                            .foregroundStyle(.secondary)
                    }

                case .exito:
                    ListaPostsView(viewModel: viewModel)

                case .error(let msg):
                    VStack(spacing: 16) {
                        Image(systemName: "wifi.slash")
                            .font(.system(size: 50))
                            .foregroundStyle(.red)
                        Text(msg)
                            .foregroundStyle(.secondary)
                            .multilineTextAlignment(.center)
                        Button("Reintentar") {
                            Task { await viewModel.cargarPosts() }
                        }
                        .buttonStyle(.borderedProminent)
                    }
                    .padding()
                }
            }
            .navigationTitle("📰 Posts")
            .searchable(text: $viewModel.busqueda,
                       prompt: "Buscar posts...")
            .task { await viewModel.cargarPosts() }
            .refreshable { await viewModel.cargarPosts() }
        }
    }
}

// MARK: - Lista
struct ListaPostsView: View {
    @ObservedObject var viewModel: PostsViewModel

    var body: some View {
        List(viewModel.postsFiltrados) { post in
            NavigationLink(value: post) {
                FilaPost(post: post)
            }
        }
        .navigationDestination(for: Post.self) { post in
            DetallePostView(post: post, viewModel: viewModel)
        }
    }
}

// MARK: - Fila
struct FilaPost: View {
    let post: Post

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            HStack {
                Text("Usuario \(post.userId)")
                    .font(.caption.bold())
                    .padding(.horizontal, 8).padding(.vertical, 3)
                    .background(.blue).foregroundStyle(.white)
                    .clipShape(Capsule())
                Spacer()
                Text("#\(post.id)").font(.caption2).foregroundStyle(.tertiary)
            }
            Text(post.title.capitalized)
                .font(.subheadline.bold()).lineLimit(2)
            Text(post.body)
                .font(.caption).foregroundStyle(.secondary).lineLimit(2)
        }
        .padding(.vertical, 4)
    }
}

// MARK: - Detalle
struct DetallePostView: View {
    let post: Post
    @ObservedObject var viewModel: PostsViewModel
    @State private var comentarios: [Comentario] = []
    @State private var cargando = false

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 20) {
                // Post
                VStack(alignment: .leading, spacing: 10) {
                    Label("Usuario \(post.userId)",
                          systemImage: "person.circle.fill")
                        .foregroundStyle(.blue).font(.caption)
                    Text(post.title.capitalized).font(.title3.bold())
                    Text(post.body).foregroundStyle(.secondary)
                }
                .padding()
                .background(.gray.opacity(0.06))
                .clipShape(RoundedRectangle(cornerRadius: 12))

                // Comentarios
                VStack(alignment: .leading, spacing: 10) {
                    Text("💬 Comentarios (\(comentarios.count))")
                        .font(.headline)

                    if cargando {
                        ProgressView()
                    } else {
                        ForEach(comentarios) { c in
                            VStack(alignment: .leading, spacing: 4) {
                                Text(c.email).font(.caption.bold())
                                    .foregroundStyle(.blue)
                                Text(c.name).font(.caption.bold())
                                Text(c.body).font(.caption)
                                    .foregroundStyle(.secondary)
                            }
                            .padding(10)
                            .background(.gray.opacity(0.06))
                            .clipShape(RoundedRectangle(cornerRadius: 10))
                        }
                    }
                }
            }
            .padding()
        }
        .navigationTitle("Post #\(post.id)")
        .navigationBarTitleDisplayMode(.inline)
        .task {
            cargando = true
            comentarios = (try? await viewModel.cargarComentarios(
                postId: post.id)) ?? []
            cargando = false
        }
    }
}

#Preview { PostsView() }
```

---

## ✅ Resumen

| Concepto | Uso |
|---|---|
| `async/await` | Código asíncrono sin callbacks |
| `.task { }` | Ejecutar async al aparecer la vista |
| `.refreshable { }` | Pull-to-refresh |
| `URLSession.shared.data(from:)` | Descargar datos |
| `Codable` | Decodificar JSON automáticamente |
| `JSONDecoder()` | Convertir Data → structs Swift |

---

⬅️ [01 — Animaciones](./01-animaciones.md) | ➡️ [03 — Persistencia con SwiftData](./03-persistencia-datos.md)

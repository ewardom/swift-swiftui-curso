# ✨ Animaciones en SwiftUI

## 🎯 Lo que aprenderás

- `withAnimation` y `.animation()`
- Tipos de animaciones y curvas
- Transitions entre vistas
- `matchedGeometryEffect`
- Proyecto: Pantalla de Onboarding animada

---

## 1. withAnimation — Animar Cambios de Estado

```swift
import SwiftUI

struct EjemploWithAnimation: View {
    @State private var expandido = false
    @State private var rotacion: Double = 0
    @State private var opacidad: Double = 1
    @State private var escala: CGFloat = 1

    var body: some View {
        VStack(spacing: 30) {
            // Animar con withAnimation al tocar
            RoundedRectangle(cornerRadius: 16)
                .fill(.blue.gradient)
                .frame(height: expandido ? 200 : 80)
                .overlay(
                    Text(expandido ? "Expandido ▲" : "Comprimido ▼")
                        .foregroundStyle(.white)
                        .fontWeight(.semibold)
                )
                .onTapGesture {
                    withAnimation(.spring(duration: 0.5)) {
                        expandido.toggle()
                    }
                }

            // Rotación animada
            Image(systemName: "arrow.triangle.2.circlepath")
                .font(.system(size: 40))
                .foregroundStyle(.purple)
                .rotationEffect(.degrees(rotacion))

            Button("Rotar") {
                withAnimation(.linear(duration: 0.6)) {
                    rotacion += 180
                }
            }
            .buttonStyle(.bordered)

            // Opacidad y escala
            Circle()
                .fill(.orange)
                .frame(width: 80, height: 80)
                .opacity(opacidad)
                .scaleEffect(escala)

            HStack(spacing: 12) {
                Button("Pulsar") {
                    withAnimation(.spring(bounce: 0.6)) {
                        escala = escala == 1 ? 1.5 : 1
                    }
                }
                .buttonStyle(.bordered)

                Button("Desvanecer") {
                    withAnimation(.easeInOut(duration: 0.4)) {
                        opacidad = opacidad == 1 ? 0.2 : 1
                    }
                }
                .buttonStyle(.bordered)
            }
        }
        .padding()
    }
}
```

---

## 2. Tipos de Animación

```swift
struct TiposAnimacion: View {
    @State private var activo = false

    var body: some View {
        VStack(spacing: 20) {
            Text("Toca un botón para ver la animación")
                .font(.caption)
                .foregroundStyle(.secondary)

            let desplazamiento: CGFloat = activo ? 100 : 0

            // Spring — rebote natural
            Circle().fill(.blue).frame(width: 40, height: 40)
                .offset(x: desplazamiento)
                .animation(.spring(duration: 0.5, bounce: 0.5), value: activo)

            // EaseInOut — suave entrada y salida
            Circle().fill(.green).frame(width: 40, height: 40)
                .offset(x: desplazamiento)
                .animation(.easeInOut(duration: 0.5), value: activo)

            // Linear — velocidad constante
            Circle().fill(.orange).frame(width: 40, height: 40)
                .offset(x: desplazamiento)
                .animation(.linear(duration: 0.5), value: activo)

            // EaseOut — rápido al inicio, lento al final
            Circle().fill(.red).frame(width: 40, height: 40)
                .offset(x: desplazamiento)
                .animation(.easeOut(duration: 0.5), value: activo)

            // Interpolating spring — más control
            Circle().fill(.purple).frame(width: 40, height: 40)
                .offset(x: desplazamiento)
                .animation(.interpolatingSpring(stiffness: 300, damping: 15),
                           value: activo)

            Button(activo ? "← Regresar" : "Ir →") {
                activo.toggle()
            }
            .buttonStyle(.borderedProminent)
        }
        .padding()
    }
}
```

---

## 3. Transitions — Animaciones de Aparición

```swift
struct EjemploTransitions: View {
    @State private var mostrar = false

    var body: some View {
        VStack(spacing: 20) {
            Toggle("Mostrar elementos", isOn: $mostrar.animation())
                .padding()

            if mostrar {
                // Opacity
                Text("🌟 Opacity")
                    .padding().background(.blue.opacity(0.1))
                    .clipShape(RoundedRectangle(cornerRadius: 10))
                    .transition(.opacity)

                // Slide desde arriba
                Text("⬇️ Slide")
                    .padding().background(.green.opacity(0.1))
                    .clipShape(RoundedRectangle(cornerRadius: 10))
                    .transition(.move(edge: .top))

                // Scale
                Text("🔍 Scale")
                    .padding().background(.orange.opacity(0.1))
                    .clipShape(RoundedRectangle(cornerRadius: 10))
                    .transition(.scale)

                // Combinada
                Text("✨ Combinada")
                    .padding().background(.purple.opacity(0.1))
                    .clipShape(RoundedRectangle(cornerRadius: 10))
                    .transition(.scale.combined(with: .opacity))

                // Asimétrica (diferente entrada y salida)
                Text("🔄 Asimétrica")
                    .padding().background(.red.opacity(0.1))
                    .clipShape(RoundedRectangle(cornerRadius: 10))
                    .transition(.asymmetric(
                        insertion: .move(edge: .leading),
                        removal: .move(edge: .trailing)
                    ))
            }
        }
        .animation(.spring(duration: 0.4), value: mostrar)
    }
}
```

---

## 4. Animaciones de Estado con .animation()

```swift
struct AnimacionEstado: View {
    @State private var progreso: Double = 0
    @State private var color: Color = .blue
    @State private var completado = false

    var body: some View {
        VStack(spacing: 24) {
            // Barra de progreso animada
            VStack(alignment: .leading, spacing: 8) {
                HStack {
                    Text("Progreso")
                    Spacer()
                    Text("\(Int(progreso * 100))%")
                        .fontWeight(.semibold)
                        .foregroundStyle(color)
                        .contentTransition(.numericText())
                }

                GeometryReader { geo in
                    ZStack(alignment: .leading) {
                        Capsule()
                            .fill(.gray.opacity(0.2))
                            .frame(height: 12)
                        Capsule()
                            .fill(color.gradient)
                            .frame(width: geo.size.width * progreso, height: 12)
                    }
                }
                .frame(height: 12)
            }
            .padding()
            .background(.gray.opacity(0.06))
            .clipShape(RoundedRectangle(cornerRadius: 12))

            // Checkmark animado
            ZStack {
                Circle()
                    .stroke(.gray.opacity(0.2), lineWidth: 4)
                    .frame(width: 80, height: 80)
                Circle()
                    .trim(from: 0, to: progreso)
                    .stroke(color.gradient, style: StrokeStyle(
                        lineWidth: 4, lineCap: .round
                    ))
                    .frame(width: 80, height: 80)
                    .rotationEffect(.degrees(-90))
                    .animation(.easeInOut(duration: 0.4), value: progreso)

                if completado {
                    Image(systemName: "checkmark")
                        .font(.title2.bold())
                        .foregroundStyle(.green)
                        .transition(.scale.combined(with: .opacity))
                }
            }

            // Controles
            Slider(value: $progreso) {
                Text("Progreso")
            }
            .tint(color)
            .onChange(of: progreso) { _, nuevo in
                withAnimation(.spring()) {
                    color = nuevo < 0.33 ? .red
                           : nuevo < 0.66 ? .orange : .green
                    completado = nuevo >= 1.0
                }
            }

            Button("Completar") {
                withAnimation(.spring(duration: 0.6, bounce: 0.3)) {
                    progreso = 1.0
                    color = .green
                    completado = true
                }
            }
            .buttonStyle(.borderedProminent)
            .tint(.green)

            Button("Reiniciar") {
                withAnimation {
                    progreso = 0
                    color = .blue
                    completado = false
                }
            }
            .buttonStyle(.bordered)
        }
        .padding()
        .animation(.easeInOut(duration: 0.3), value: color)
    }
}
```

---

## 5. matchedGeometryEffect

Anima un elemento entre dos vistas distintas:

```swift
struct MatchedGeometryDemo: View {
    @Namespace private var animacionNS
    @State private var expandido = false

    var body: some View {
        VStack {
            if expandido {
                // Vista expandida
                VStack(alignment: .leading, spacing: 12) {
                    HStack {
                        Circle()
                            .fill(.blue.gradient)
                            .frame(width: 60, height: 60)
                            .overlay(
                                Image(systemName: "swift")
                                    .foregroundStyle(.white)
                                    .font(.title2)
                            )
                            .matchedGeometryEffect(id: "avatar", in: animacionNS)

                        VStack(alignment: .leading) {
                            Text("Swift & SwiftUI")
                                .font(.headline)
                                .matchedGeometryEffect(id: "titulo", in: animacionNS)
                            Text("Lenguaje de Apple")
                                .font(.caption)
                                .foregroundStyle(.secondary)
                        }
                        Spacer()
                        Button {
                            withAnimation(.spring(duration: 0.5)) {
                                expandido = false
                            }
                        } label: {
                            Image(systemName: "xmark.circle.fill")
                                .foregroundStyle(.secondary)
                        }
                    }

                    Text("Swift es un lenguaje de programación moderno, seguro y expresivo creado por Apple. SwiftUI es su framework para crear interfaces declarativas en todas las plataformas Apple.")
                        .font(.subheadline)
                        .foregroundStyle(.secondary)

                    BotonPrincipal("Aprender más",
                                   icono: "book.fill") { }
                }
                .padding()
                .background(.background)
                .clipShape(RoundedRectangle(cornerRadius: 20))
                .shadow(radius: 10)
                .padding()

            } else {
                // Vista compacta (tarjeta)
                HStack(spacing: 12) {
                    Circle()
                        .fill(.blue.gradient)
                        .frame(width: 44, height: 44)
                        .overlay(
                            Image(systemName: "swift")
                                .foregroundStyle(.white)
                        )
                        .matchedGeometryEffect(id: "avatar", in: animacionNS)

                    Text("Swift & SwiftUI")
                        .fontWeight(.semibold)
                        .matchedGeometryEffect(id: "titulo", in: animacionNS)

                    Spacer()

                    Image(systemName: "chevron.right")
                        .foregroundStyle(.secondary)
                }
                .padding()
                .background(.gray.opacity(0.08))
                .clipShape(RoundedRectangle(cornerRadius: 14))
                .padding(.horizontal)
                .onTapGesture {
                    withAnimation(.spring(duration: 0.5)) {
                        expandido = true
                    }
                }
            }
        }
    }
}
```

---

## 6. 🛠️ Proyecto: Pantalla de Onboarding Animada

```swift
import SwiftUI

struct PaginaOnboarding {
    let icono: String
    let color: Color
    let titulo: String
    let descripcion: String
}

struct OnboardingView: View {
    @AppStorage("onboardingCompletado") private var completado = false
    @State private var paginaActual = 0
    @State private var mostrarBoton = false

    let paginas: [PaginaOnboarding] = [
        PaginaOnboarding(icono: "swift", color: .orange,
                        titulo: "Aprende Swift",
                        descripcion: "Domina el lenguaje de programación más moderno de Apple desde cero."),
        PaginaOnboarding(icono: "iphone", color: .blue,
                        titulo: "Crea Apps",
                        descripcion: "Construye aplicaciones hermosas con SwiftUI para iPhone, iPad y Mac."),
        PaginaOnboarding(icono: "star.fill", color: .purple,
                        titulo: "Publica tu App",
                        descripcion: "Comparte tu creación con millones de usuarios en la App Store.")
    ]

    var esUltimaPagina: Bool {
        paginaActual == paginas.count - 1
    }

    var body: some View {
        ZStack {
            // Fondo animado
            paginas[paginaActual].color
                .opacity(0.08)
                .ignoresSafeArea()
                .animation(.easeInOut(duration: 0.5), value: paginaActual)

            VStack(spacing: 0) {
                // Botón saltar
                HStack {
                    Spacer()
                    Button("Saltar") {
                        withAnimation { completado = true }
                    }
                    .foregroundStyle(.secondary)
                    .padding()
                    .visible(!esUltimaPagina)
                }

                Spacer()

                // Contenido de cada página
                TabView(selection: $paginaActual) {
                    ForEach(paginas.indices, id: \.self) { index in
                        PaginaContenido(pagina: paginas[index])
                            .tag(index)
                    }
                }
                .tabViewStyle(.page(indexDisplayMode: .never))
                .animation(.spring(duration: 0.5), value: paginaActual)

                Spacer()

                // Indicadores de página
                HStack(spacing: 8) {
                    ForEach(paginas.indices, id: \.self) { index in
                        Capsule()
                            .fill(index == paginaActual
                                  ? paginas[paginaActual].color
                                  : .gray.opacity(0.3))
                            .frame(width: index == paginaActual ? 24 : 8,
                                   height: 8)
                            .animation(.spring(duration: 0.3), value: paginaActual)
                    }
                }
                .padding(.bottom, 32)

                // Botón de acción
                VStack(spacing: 12) {
                    if esUltimaPagina {
                        BotonPrincipal("¡Empezar ahora! 🚀",
                                       icono: "arrow.right.circle.fill") {
                            withAnimation(.spring(duration: 0.5)) {
                                completado = true
                            }
                        }
                        .transition(.move(edge: .bottom).combined(with: .opacity))
                    } else {
                        BotonPrincipal("Siguiente",
                                       icono: "arrow.right",
                                       color: paginas[paginaActual].color) {
                            withAnimation(.spring(duration: 0.4, bounce: 0.2)) {
                                paginaActual += 1
                            }
                        }
                    }
                }
                .animation(.spring(duration: 0.4), value: esUltimaPagina)
                .padding(.bottom, 40)
            }
        }
        .onAppear {
            withAnimation(.spring(duration: 0.6).delay(0.3)) {
                mostrarBoton = true
            }
        }
    }
}

struct PaginaContenido: View {
    let pagina: PaginaOnboarding
    @State private var aparecer = false

    var body: some View {
        VStack(spacing: 32) {
            // Ícono animado
            ZStack {
                Circle()
                    .fill(pagina.color.opacity(0.15))
                    .frame(width: 160, height: 160)
                    .scaleEffect(aparecer ? 1 : 0.5)

                Circle()
                    .fill(pagina.color.opacity(0.25))
                    .frame(width: 120, height: 120)
                    .scaleEffect(aparecer ? 1 : 0.5)

                Image(systemName: pagina.icono)
                    .font(.system(size: 60))
                    .foregroundStyle(pagina.color)
                    .scaleEffect(aparecer ? 1 : 0.3)
                    .rotationEffect(.degrees(aparecer ? 0 : -30))
            }
            .animation(.spring(duration: 0.7, bounce: 0.4), value: aparecer)

            // Texto
            VStack(spacing: 16) {
                Text(pagina.titulo)
                    .font(.largeTitle.bold())
                    .multilineTextAlignment(.center)
                    .offset(y: aparecer ? 0 : 30)
                    .opacity(aparecer ? 1 : 0)
                    .animation(.spring(duration: 0.6).delay(0.15),
                               value: aparecer)

                Text(pagina.descripcion)
                    .font(.body)
                    .foregroundStyle(.secondary)
                    .multilineTextAlignment(.center)
                    .padding(.horizontal, 32)
                    .offset(y: aparecer ? 0 : 30)
                    .opacity(aparecer ? 1 : 0)
                    .animation(.spring(duration: 0.6).delay(0.25),
                               value: aparecer)
            }
        }
        .onAppear { aparecer = true }
        .onDisappear { aparecer = false }
    }
}

#Preview {
    OnboardingView()
}
```

---

## ✅ Resumen

| Técnica | Cuándo usar |
|---|---|
| `withAnimation { }` | Animar un cambio de estado puntual |
| `.animation(_, value:)` | Animar automáticamente cuando cambia un valor |
| `.transition()` | Animar aparición/desaparición de vistas |
| `matchedGeometryEffect` | Animar un elemento entre dos estados/vistas |
| `.spring()` | Animación natural con rebote |
| `.easeInOut()` | Animación suave sin rebote |

---

⬅️ [Módulo 06 — Código Modular](../modulo-06-arquitectura-mvvm/02-codigo-modular.md) | ➡️ [02 — Networking y JSON](./02-networking-json.md)

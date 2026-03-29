# 📝 Formularios e Inputs en SwiftUI

## 🎯 Lo que aprenderás

- `TextField` y `SecureField`
- `Toggle`, `Slider`, `Stepper`
- `Picker` con distintos estilos
- `DatePicker`
- `Form` y `Section`
- Validación básica en tiempo real
- Proyecto: Formulario de Registro completo

---

## 1. TextField y SecureField

```swift
import SwiftUI

struct EjemplosTextField: View {
    @State private var nombre = ""
    @State private var email = ""
    @State private var contrasena = ""
    @State private var bio = ""
    @State private var cantidad = ""

    var body: some View {
        Form {
            Section("Básico") {
                // TextField simple
                TextField("Tu nombre", text: $nombre)

                // Con ícono
                HStack {
                    Image(systemName: "envelope")
                        .foregroundStyle(.secondary)
                    TextField("Email", text: $email)
                        .keyboardType(.emailAddress)
                        .autocapitalization(.none)
                        .autocorrectionDisabled()
                }

                // Contraseña
                SecureField("Contraseña", text: $contrasena)

                // Número
                TextField("Cantidad", text: $cantidad)
                    .keyboardType(.numberPad)
            }

            Section("Texto largo") {
                // TextEditor para texto multilínea
                TextEditor(text: $bio)
                    .frame(minHeight: 100)
            }

            Section("Vista previa") {
                if !nombre.isEmpty {
                    Text("Nombre: \(nombre)")
                }
                if !email.isEmpty {
                    Text("Email: \(email)")
                }
            }
        }
    }
}
```

---

## 2. Toggle

```swift
struct EjemplosToggle: View {
    @State private var notificaciones = true
    @State private var modoOscuro = false
    @State private var sincronizar = true
    @State private var ubicacion = false

    var body: some View {
        Form {
            Section("Preferencias") {
                Toggle("Notificaciones", isOn: $notificaciones)
                Toggle("Modo Oscuro", isOn: $modoOscuro)
                Toggle("Sincronizar datos", isOn: $sincronizar)
                Toggle(isOn: $ubicacion) {
                    Label("Ubicación", systemImage: "location.fill")
                }
            }

            Section("Estilos") {
                // Estilo switch (por defecto)
                Toggle("Switch", isOn: $notificaciones)
                    .toggleStyle(.switch)

                // Estilo botón
                Toggle("Botón", isOn: $modoOscuro)
                    .toggleStyle(.button)
                    .tint(.purple)
            }
        }
    }
}
```

---

## 3. Slider y Stepper

```swift
struct EjemplosSliderStepper: View {
    @State private var brillo: Double = 0.5
    @State private var volumen: Double = 70
    @State private var personas: Int = 1
    @State private var vasos: Int = 8

    var body: some View {
        Form {
            Section("Slider") {
                VStack(alignment: .leading, spacing: 8) {
                    Text("Brillo: \(Int(brillo * 100))%")
                    HStack {
                        Image(systemName: "sun.min")
                        Slider(value: $brillo, in: 0...1)
                        Image(systemName: "sun.max")
                    }
                }

                VStack(alignment: .leading, spacing: 8) {
                    Text("Volumen: \(Int(volumen))")
                    HStack {
                        Image(systemName: "speaker")
                        Slider(value: $volumen, in: 0...100, step: 5) {
                            Text("Volumen")
                        } minimumValueLabel: {
                            Text("0")
                        } maximumValueLabel: {
                            Text("100")
                        }
                        Image(systemName: "speaker.wave.3")
                    }
                }
            }

            Section("Stepper") {
                Stepper("Personas: \(personas)",
                        value: $personas, in: 1...20)

                Stepper(value: $vasos, in: 0...20, step: 2) {
                    Label("Vasos de agua: \(vasos)",
                          systemImage: "drop.fill")
                        .foregroundStyle(.blue)
                }
            }
        }
    }
}
```

---

## 4. Picker

```swift
struct EjemplosPicker: View {
    @State private var pais = "México"
    @State private var idioma = "Español"
    @State private var color = "Azul"
    @State private var mes = 1

    let paises = ["México", "España", "Argentina", "Colombia", "Chile"]
    let idiomas = ["Español", "Inglés", "Francés", "Portugués"]
    let colores = ["Azul", "Rojo", "Verde", "Naranja", "Morado"]
    let meses = ["Ene","Feb","Mar","Abr","May","Jun",
                 "Jul","Ago","Sep","Oct","Nov","Dic"]

    var body: some View {
        Form {
            // Estilo menú (por defecto en Form)
            Section("Menú") {
                Picker("País", selection: $pais) {
                    ForEach(paises, id: \.self) { Text($0) }
                }

                Picker("Idioma", selection: $idioma) {
                    ForEach(idiomas, id: \.self) {
                        Text($0).tag($0)
                    }
                }
            }

            // Estilo segmented
            Section("Segmentado") {
                Picker("Color", selection: $color) {
                    ForEach(colores.prefix(3), id: \.self) {
                        Text($0)
                    }
                }
                .pickerStyle(.segmented)
            }

            // Estilo wheel
            Section("Rueda") {
                Picker("Mes", selection: $mes) {
                    ForEach(1...12, id: \.self) { i in
                        Text(meses[i - 1]).tag(i)
                    }
                }
                .pickerStyle(.wheel)
                .frame(height: 120)
            }

            Section("Selección actual") {
                Text("País: \(pais)")
                Text("Mes: \(meses[mes - 1])")
            }
        }
    }
}
```

---

## 5. DatePicker

```swift
struct EjemplosDatePicker: View {
    @State private var fechaNacimiento = Date()
    @State private var fechaReunion = Date()
    @State private var soloFecha = Date()

    var body: some View {
        Form {
            Section("Fecha y hora") {
                DatePicker("Reunión",
                           selection: $fechaReunion)
            }

            Section("Solo fecha") {
                DatePicker("Nacimiento",
                           selection: $fechaNacimiento,
                           in: ...Date(),
                           displayedComponents: .date)
            }

            Section("Solo hora") {
                DatePicker("Hora",
                           selection: $soloFecha,
                           displayedComponents: .hourAndMinute)
            }

            Section("Estilo gráfico") {
                DatePicker("Fecha",
                           selection: $fechaReunion,
                           displayedComponents: .date)
                    .datePickerStyle(.graphical)
            }
        }
    }
}
```

---

## 6. Form y Section

```swift
struct EjemploForm: View {
    @State private var nombre = ""
    @State private var activo = true

    var body: some View {
        Form {
            // Section con header
            Section("Datos") {
                TextField("Nombre", text: $nombre)
            }

            // Section con header y footer
            Section {
                Toggle("Activo", isOn: $activo)
            } header: {
                Text("Estado")
            } footer: {
                Text("Al desactivar, no recibirás notificaciones.")
                    .font(.caption)
            }
        }
    }
}
```

---

## 7. 🛠️ Proyecto: Formulario de Registro

```swift
import SwiftUI

struct FormularioRegistroCompleto: View {
    // Datos personales
    @State private var nombre = ""
    @State private var apellido = ""
    @State private var email = ""
    @State private var telefono = ""
    @State private var fechaNacimiento = Date()

    // Preferencias
    @State private var pais = "México"
    @State private var genero = "Prefiero no decir"
    @State private var nivelExperiencia = 1

    // Seguridad
    @State private var contrasena = ""
    @State private var confirmarContrasena = ""
    @State private var mostrarContrasena = false

    // Opciones
    @State private var recibirNoticias = true
    @State private var aceptaTerminos = false

    // Estado UI
    @State private var registroExitoso = false
    @State private var intentoEnviar = false

    let paises = ["México", "España", "Argentina",
                  "Colombia", "Chile", "Perú", "Otro"]
    let generos = ["Masculino", "Femenino",
                   "No binario", "Prefiero no decir"]

    // MARK: - Validaciones
    var nombreValido: Bool { nombre.count >= 2 }
    var emailValido: Bool {
        email.contains("@") && email.contains(".")
    }
    var contrasenaValida: Bool { contrasena.count >= 8 }
    var contrasenasCoinciden: Bool {
        contrasena == confirmarContrasena && !contrasena.isEmpty
    }
    var formularioValido: Bool {
        nombreValido && emailValido &&
        contrasenaValida && contrasenasCoinciden &&
        aceptaTerminos
    }

    var body: some View {
        NavigationStack {
            Form {
                // — Datos Personales —
                Section {
                    TextField("Nombre *", text: $nombre)
                    if intentoEnviar && !nombreValido {
                        ErrorCampo(mensaje: "Mínimo 2 caracteres")
                    }

                    TextField("Apellido", text: $apellido)

                    TextField("Email *", text: $email)
                        .keyboardType(.emailAddress)
                        .autocapitalization(.none)
                    if intentoEnviar && !emailValido {
                        ErrorCampo(mensaje: "Email inválido")
                    }

                    TextField("Teléfono", text: $telefono)
                        .keyboardType(.phonePad)

                    DatePicker("Fecha de nacimiento",
                               selection: $fechaNacimiento,
                               in: ...Date(),
                               displayedComponents: .date)
                } header: {
                    Label("Datos Personales", systemImage: "person")
                }

                // — Preferencias —
                Section {
                    Picker("País", selection: $pais) {
                        ForEach(paises, id: \.self) { Text($0) }
                    }

                    Picker("Género", selection: $genero) {
                        ForEach(generos, id: \.self) { Text($0) }
                    }

                    VStack(alignment: .leading, spacing: 6) {
                        Text("Nivel de experiencia: \(nivelTexto)")
                            .font(.subheadline)
                        Slider(value: Binding(
                            get: { Double(nivelExperiencia) },
                            set: { nivelExperiencia = Int($0) }
                        ), in: 1...5, step: 1)
                        .tint(.blue)
                        HStack {
                            Text("Principiante")
                            Spacer()
                            Text("Experto")
                        }
                        .font(.caption2)
                        .foregroundStyle(.secondary)
                    }
                } header: {
                    Label("Preferencias", systemImage: "slider.horizontal.3")
                }

                // — Seguridad —
                Section {
                    HStack {
                        Group {
                            if mostrarContrasena {
                                TextField("Contraseña *", text: $contrasena)
                            } else {
                                SecureField("Contraseña *", text: $contrasena)
                            }
                        }
                        Button {
                            mostrarContrasena.toggle()
                        } label: {
                            Image(systemName: mostrarContrasena
                                  ? "eye.slash" : "eye")
                                .foregroundStyle(.secondary)
                        }
                    }

                    if !contrasena.isEmpty {
                        BarraFortaleza(contrasena: contrasena)
                    }

                    if intentoEnviar && !contrasenaValida {
                        ErrorCampo(mensaje: "Mínimo 8 caracteres")
                    }

                    SecureField("Confirmar contraseña *",
                                text: $confirmarContrasena)

                    if intentoEnviar && !contrasenasCoinciden {
                        ErrorCampo(mensaje: "Las contraseñas no coinciden")
                    }
                } header: {
                    Label("Seguridad", systemImage: "lock")
                }

                // — Opciones —
                Section {
                    Toggle("Recibir novedades por email",
                           isOn: $recibirNoticias)

                    Toggle(isOn: $aceptaTerminos) {
                        Text("Acepto los ") +
                        Text("Términos y Condiciones")
                            .foregroundStyle(.blue)
                            .underline()
                    }
                } header: {
                    Label("Opciones", systemImage: "checkmark.circle")
                } footer: {
                    if intentoEnviar && !aceptaTerminos {
                        Text("⚠️ Debes aceptar los términos para continuar")
                            .foregroundStyle(.red)
                    }
                }

                // — Botón de Registro —
                Section {
                    Button {
                        intentoEnviar = true
                        if formularioValido {
                            registroExitoso = true
                        }
                    } label: {
                        HStack {
                            Spacer()
                            Label("Crear Cuenta",
                                  systemImage: "person.badge.plus")
                                .fontWeight(.semibold)
                            Spacer()
                        }
                    }
                    .tint(formularioValido ? .blue : .gray)
                }
            }
            .navigationTitle("Crear Cuenta")
            .alert("¡Registro Exitoso! 🎉",
                   isPresented: $registroExitoso) {
                Button("Continuar") { }
            } message: {
                Text("Bienvenido/a, \(nombre)!\nTu cuenta ha sido creada con éxito.")
            }
        }
    }

    var nivelTexto: String {
        switch nivelExperiencia {
        case 1: return "Principiante"
        case 2: return "Básico"
        case 3: return "Intermedio"
        case 4: return "Avanzado"
        default: return "Experto"
        }
    }
}

// MARK: - Componentes auxiliares
struct ErrorCampo: View {
    let mensaje: String
    var body: some View {
        Label(mensaje, systemImage: "exclamationmark.triangle.fill")
            .font(.caption)
            .foregroundStyle(.red)
    }
}

struct BarraFortaleza: View {
    let contrasena: String

    var fortaleza: (valor: Double, texto: String, color: Color) {
        let n = contrasena.count
        if n < 6  { return (0.25, "Débil 😟", .red) }
        if n < 10 { return (0.5,  "Regular 😐", .orange) }
        if n < 14 { return (0.75, "Fuerte 💪", .yellow) }
        return (1.0, "Muy fuerte 🔒", .green)
    }

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            ProgressView(value: fortaleza.valor)
                .tint(fortaleza.color)
            Text(fortaleza.texto)
                .font(.caption)
                .foregroundStyle(fortaleza.color)
        }
    }
}

#Preview {
    FormularioRegistroCompleto()
}
```

---

## ✅ Resumen

| Control | Uso |
|---|---|
| `TextField` | Texto de una línea |
| `SecureField` | Contraseñas |
| `TextEditor` | Texto multilínea |
| `Toggle` | Sí / No |
| `Slider` | Rango continuo |
| `Stepper` | Incremento/decremento |
| `Picker` | Selección de una lista |
| `DatePicker` | Fecha y hora |
| `Form` | Contenedor estilo configuración |
| `Section` | Agrupación dentro de Form |

---

⬅️ [Módulo 04 — @EnvironmentObject](../modulo-04-estado-y-datos/03-environmentobject.md) | ➡️ [02 — Alerts y Sheets](./02-alerts-sheets.md)

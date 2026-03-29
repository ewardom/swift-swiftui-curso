# 🏪 Publicar en la App Store

## 🎯 Lo que aprenderás

- Configurar App Icons y Assets
- Info.plist y permisos
- Probar en dispositivo real
- Crear un Archive para distribución
- TestFlight para pruebas beta
- Proceso de revisión de App Store
- Recursos para seguir aprendiendo

---

## 1. App Icons

Xcode 15+ genera los iconos automáticamente desde una sola imagen:

1. En tu proyecto, abre `Assets.xcassets`
2. Selecciona **AppIcon**
3. Arrastra una imagen de **1024×1024 px** en PNG
4. Xcode genera todos los tamaños automáticamente ✅

> 💡 **Tip:** Usa [appicon.co](https://appicon.co) para generar todos los tamaños si usas una versión anterior de Xcode.

### Requisitos del ícono:
- Formato: PNG sin transparencia
- Tamaño base: 1024×1024 px
- Sin esquinas redondeadas (Apple las agrega)
- Sin texto pequeño (no se verá en tamaños pequeños)

---

## 2. Assets y Colores Adaptativos

```swift
// En Assets.xcassets crea colores para dark/light mode:
// 1. Click en + → Color Set
// 2. Nómbralo (ej: "ColorPrimario")
// 3. Configura el color para Light y Dark

// Uso en SwiftUI:
Color("ColorPrimario")   // se adapta automáticamente

// Imágenes adaptativas (mismo proceso con Image Set)
Image("LogoApp")
    .resizable()
    .scaledToFit()
```

---

## 3. Info.plist — Permisos

Cuando tu app necesita acceso a recursos del sistema:

```
Cámara
→ NSCameraUsageDescription
→ "Necesitamos la cámara para tomar fotos de perfil."

Micrófono
→ NSMicrophoneUsageDescription
→ "Usamos el micrófono para grabar notas de voz."

Galería de fotos
→ NSPhotoLibraryUsageDescription
→ "Accedemos a tus fotos para elegir una imagen."

Ubicación
→ NSLocationWhenInUseUsageDescription
→ "Usamos tu ubicación para mostrarte contenido cercano."
```

En Xcode: **Project → Target → Info → Custom iOS Target Properties**

---

## 4. Probar en tu iPhone

Antes de publicar, prueba siempre en dispositivo real:

1. Conecta tu iPhone al Mac
2. En Xcode, selecciona tu iPhone en el selector de destino (arriba)
3. Presiona **⌘ + R** para ejecutar
4. La primera vez deberás ir a:
   **Ajustes → General → VPN y gestión del dispositivo → Confiar en developer**

### Checklist de pruebas en dispositivo:
```
✅ La app inicia correctamente
✅ Funciona en modo oscuro y claro
✅ Funciona en orientación vertical y horizontal
✅ Los textos no se cortan con Dynamic Type
✅ Sin crashes al navegar por todas las pantallas
✅ El rendimiento es fluido (sin lag)
✅ Los permisos se solicitan correctamente
```

---

## 5. Crear el Archive

El Archive es el paquete final que subirás a App Store Connect:

1. En Xcode, selecciona **"Any iOS Device"** como destino (no un simulador)
2. Ve a **Product → Archive**
3. Espera a que compile (puede tardar unos minutos)
4. Se abrirá el **Organizer** con tu Archive listo

---

## 6. Subir a App Store Connect

Desde el Organizer:

1. Click en **"Distribute App"**
2. Selecciona **"App Store Connect"**
3. Selecciona **"Upload"**
4. Activa:
   - ✅ Upload your app's symbols
   - ✅ Manage Version and Build Number
5. Click en **"Distribute"**
6. Espera la confirmación de Apple (~5-10 minutos)

---

## 7. TestFlight — Pruebas Beta

Antes de publicar, distribuye tu app a testers con TestFlight:

### En App Store Connect:
1. Ve a [appstoreconnect.apple.com](https://appstoreconnect.apple.com)
2. Selecciona tu app → **TestFlight**
3. Espera que Apple procese el build (~15-30 min)
4. Agrega testers internos (hasta 100, sin revisión)
5. O testers externos (hasta 10,000, con revisión de Apple)

### Tus testers instalan la app desde:
- La app **TestFlight** en su iPhone
- O un enlace de invitación que tú generas

---

## 8. Crear la Página de la App en App Store Connect

Antes de enviar a revisión, completa toda la información:

### Información requerida:
```
📱 Nombre de la app       — máximo 30 caracteres
📝 Subtítulo              — máximo 30 caracteres
📄 Descripción            — máximo 4000 caracteres
🔑 Palabras clave         — máximo 100 caracteres (separadas por coma)
🌐 URL de soporte         — requerida (puede ser un email o sitio web)
📸 Capturas de pantalla   — mínimo 1, máximo 10 por tamaño de pantalla
🎬 Preview de video       — opcional pero muy recomendado
🔞 Clasificación de edad  — se configura con un cuestionario
💰 Precio                 — gratis o de pago
```

### Tamaños de captura de pantalla requeridos:
| Dispositivo | Tamaño |
|---|---|
| iPhone 6.9" | 1320×2868 px |
| iPhone 6.7" | 1290×2796 px |
| iPad 13" | 2064×2752 px |

> 💡 **Tip:** Usa el **Simulador de Xcode** para tomar screenshots de alta calidad con **⌘ + S**.

---

## 9. Enviar a Revisión

1. En App Store Connect → tu app → **"Add for Review"**
2. Responde las preguntas de cumplimiento de exportación
3. Selecciona **"Manually release"** o **"Automatically release"**
4. Click en **"Submit to App Review"**

### Tiempo de revisión:
- **Primera vez:** 1-3 días hábiles
- **Actualizaciones:** 24-48 horas
- Puedes ver el estado en App Store Connect

### Motivos comunes de rechazo:
```
❌ Crashes o bugs obvios
❌ Descripción que no coincide con la funcionalidad
❌ Capturas de pantalla de simulador con borde de Mac
❌ Información de prueba (usuario/contraseña) no proporcionada
❌ Permisos solicitados sin razón clara
❌ Interfaz incompleta o de placeholder
```

---

## 10. ¡Tu App está en la App Store! 🎉

Una vez aprobada:
1. Recibirás un email de confirmación
2. Tu app estará disponible en la App Store en ~1 hora
3. Comparte el enlace desde App Store Connect → **"App Store"** → **"Share"**

### Para actualizar tu app:
1. Incrementa el número de versión en Xcode:
   **Project → Target → General → Version** (ej: 1.0 → 1.1)
2. Haz un nuevo Archive
3. Sube a App Store Connect
4. Completa las **"What's New"** (novedades de la versión)
5. Envía a revisión nuevamente

---

## 11. Recursos para Seguir Aprendiendo 📚

### Documentación oficial
- 🍎 [Apple Developer Documentation](https://developer.apple.com/documentation/swiftui)
- 🎓 [Apple SwiftUI Tutorials](https://developer.apple.com/tutorials/swiftui)
- 🎬 [WWDC Videos](https://developer.apple.com/videos/)

### Sitios y libros
- 🔥 [Hacking with Swift — Paul Hudson](https://www.hackingwithswift.com)
- 📱 [Swift by Sundell](https://www.swiftbysundell.com)
- 📺 [Sean Allen en YouTube](https://www.youtube.com/@seanallen)
- 📖 [100 Days of SwiftUI](https://www.hackingwithswift.com/100/swiftui)

### Comunidades
- 💬 [Swift Forums](https://forums.swift.org)
- 🐦 [Comunidad Swift en X/Twitter](https://twitter.com/search?q=%23swiftui)
- 💼 [iOS Developers en Reddit](https://www.reddit.com/r/iOSProgramming/)

---

## ✅ Checklist Final antes de Publicar

```
APP
✅ Funciona sin crashes en dispositivo real
✅ Funciona en modo oscuro y claro
✅ Todos los permisos tienen descripción clara
✅ El ícono está configurado (1024×1024)
✅ La versión está correcta (ej: 1.0.0)
✅ El Bundle ID es único (ej: com.tunombre.tuapp)

APP STORE CONNECT
✅ Nombre y subtítulo definidos
✅ Descripción completa y sin errores
✅ Palabras clave relevantes
✅ Capturas de pantalla reales y atractivas
✅ URL de soporte válida
✅ Clasificación de edad configurada
✅ Precio definido (gratis o de pago)

DISTRIBUCIÓN
✅ Archive creado desde "Any iOS Device"
✅ Build subido a App Store Connect
✅ Probado en TestFlight con al menos 1 tester
✅ Enviado a revisión
```

---

## 🎓 ¡Felicidades! Completaste el Curso

```
Módulo 00 ✅ — Preparación del entorno
Módulo 01 ✅ — Swift Básico
Módulo 02 ✅ — Swift Intermedio
Módulo 03 ✅ — SwiftUI Esencial
Módulo 04 ✅ — Estado y Datos
Módulo 05 ✅ — Interacción con el Usuario
Módulo 06 ✅ — Arquitectura MVVM
Módulo 07 ✅ — Temas Avanzados
Módulo 08 ✅ — Publicación en App Store
```

> 🚀 **Ahora tienes todo lo necesario para crear y publicar tu propia app en la App Store. El siguiente paso es solo uno: ¡construir algo!**

---

⬅️ [Módulo 07 — Persistencia con SwiftData](../modulo-07-avanzado/03-persistencia-datos.md)

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
- Sin esquinas redondeadas (Apple las agrega automáticamente)
- Sin texto pequeño (no se verá en tamaños pequeños)

---

## 2. Assets y Colores Adaptativos

```swift
// En Assets.xcassets crea colores para dark/light mode:
// 1. Click en + → Color Set
// 2. Nombra el color (ej: "ColorPrimario")
// 3. Configura el color para Light y Dark

// Uso en código:
Color("ColorPrimario")   // se adapta automáticamente

// Imágenes adaptativas (igual proceso con Image Set)
Image("LogoApp")
```

---

## 3. Info.plist — Permisos

Cuando tu app necesita acceder a recursos del sistema, debes declararlo:

```xml
<!-- Cámara -->
<key>NSCameraUsageDescription</key>
<string>Necesitamos acceso a la cámara para tomar fotos de perfil.</string>

<!-- Micrófono -->
<key>NSMicrophoneUsageDescription</key>
<string>Usamos el micrófono para grabar notas de voz.</string>

<!-- Galería de Fotos (lectura) -->
<key>NSPhotoLibraryUsageDescription</key>
<string>Accedemos a tus fotos para que puedas elegir una imagen.</string>

<!-- Ubicación cuando se usa -->
<key>NSLocationWhenInUseUsageDescription</key>
<string>Usamos tu ubicación para mostrarte contenido cercano.</string>

<!-- Notificaciones — se solicita en código -->
```

En Xcode moderno, edita el `Info.plist` desde:
**Project → Target → Info → Custom iOS Target Properties**

---

## 4. Probar en tu

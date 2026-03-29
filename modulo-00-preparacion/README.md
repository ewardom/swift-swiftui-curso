# 🛠️ Módulo 00 — Preparación del Entorno

## 🎯 Lo que aprenderás

- Instalar y configurar Xcode en tu Mac
- Configurar Swift Playgrounds en Mac y iPad
- Conectar tu iPhone para desarrollo
- Activar el modo desarrollador en tu iPhone
- Crear tu Apple Developer Account
- Escribir tu primer programa en Swift

---

## 1. 📥 Instalar Xcode

Xcode es el entorno de desarrollo oficial de Apple. Es **gratuito** y lo necesitas para crear apps.

### Pasos:
1. Abre **App Store** en tu Mac
2. Busca **"Xcode"**
3. Click en **"Obtener"** → **"Instalar"**
4. Espera la descarga (~15 GB, ten paciencia ☕)
5. Ábrelo una vez instalado y acepta los términos

> ⚠️ **Importante:** Xcode solo está disponible en Mac. Asegúrate de tener macOS Sequoia o posterior para usar las últimas funciones.

> 💡 **Tip:** Mientras se descarga Xcode, instala Swift Playgrounds en tu iPad para ir familiarizándote.

---

## 2. 🎮 Instalar Swift Playgrounds

Swift Playgrounds es una app interactiva ideal para aprender Swift de forma visual y divertida.

### En tu Mac:
1. Abre **App Store**
2. Busca **"Swift Playgrounds"**
3. Click en **"Obtener"** → **"Instalar"**

### En tu iPad:
1. Abre **App Store** en el iPad
2. Busca **"Swift Playgrounds"**
3. Instala la app (es gratuita)

> 💡 **Tip:** Swift Playgrounds en iPad es perfecta para estudiar en el sofá o mientras viajas. Úsala para los ejercicios de los módulos 01 y 02.

---

## 3. 📱 Conectar tu iPhone al Mac

Para probar tus apps en tu iPhone real, necesitas conectarlo a tu Mac.

### Pasos:
1. Conecta tu iPhone al Mac con cable USB o USB-C
2. En tu iPhone aparecerá un mensaje: **"¿Confiar en este computador?"** → Toca **"Confiar"**
3. Ingresa tu código de desbloqueo del iPhone
4. Abre **Xcode** → en la barra superior verás tu iPhone disponible como destino

### Conexión inalámbrica (después del primer setup):
1. En Xcode → **Window** → **Devices and Simulators**
2. Selecciona tu iPhone
3. Activa **"Connect via network"**
4. ¡Ya puedes probar apps sin cable! 📡

---

## 4. 🔧 Activar Modo Desarrollador en iPhone

Desde iOS 16, necesitas activar el Modo Desarrollador en tu iPhone.

### Pasos:
1. Ve a **Ajustes** en tu iPhone
2. Toca **Privacidad y seguridad**
3. Desplázate hasta abajo → **Modo desarrollador**
4. Activa el toggle → **Reiniciar**
5. Al reiniciar, confirma que quieres activarlo

> ⚠️ **Solo hazlo en tu iPhone personal de desarrollo**, no en dispositivos de producción.

---

## 5. 👤 Crear Apple Developer Account

Para instalar apps en tu iPhone necesitas una Apple ID (cuenta gratuita). Para publicar en la App Store necesitas el programa de pago ($99/año).

### Cuenta gratuita (para este curso):
1. Ve a [developer.apple.com](https://developer.apple.com)
2. Click en **"Account"**
3. Inicia sesión con tu **Apple ID**
4. Acepta los términos del Apple Developer Agreement
5. ¡Listo! Ya puedes instalar apps en tu propio iPhone

> 💡 **Tip:** Con la cuenta gratuita puedes instalar hasta 100 apps en tus dispositivos. Más que suficiente para aprender.

---

## 6. 🚀 Tu Primer Programa en Swift

¡Vamos a escribir tu primer código Swift! Abre **Swift Playgrounds** en tu Mac o iPad.

### Opción A — En Swift Playgrounds:
1. Abre Swift Playgrounds
2. Click en **"+"** → **"Blank"** (en blanco)
3. Borra el código que aparece
4. Escribe lo siguiente:

```swift
// 🎉 ¡Mi primer programa en Swift!

// Una variable para guardar un mensaje
var mensaje = "¡Hola, mundo!"

// Mostrar el mensaje en la consola
print(mensaje)

// Cambiar el mensaje
mensaje = "¡Estoy aprendiendo Swift!"
print(mensaje)

// Una constante (no puede cambiar)
let miNombre = "Tu nombre aquí"
print("Me llamo \(miNombre) y estoy aprendiendo a programar.")
```

5. Presiona el botón **▶️ Run** para ejecutar

### Opción B — En Xcode Playground:
1. Abre Xcode
2. **File** → **New** → **Playground**
3. Selecciona **"Blank"** → **Next**
4. Ponle nombre y guárdalo
5. Escribe el mismo código de arriba
6. Presiona **⌘ + ⇧ + Return** para ejecutar

### Resultado esperado:
```
¡Hola, mundo!
¡Estoy aprendiendo Swift!
Me llamo Tu nombre aquí y estoy aprendiendo a programar.
```

> 🎉 **¡Felicidades!** Acabas de escribir tu primer programa en Swift.

---

## ✅ Resumen

En este módulo configuraste todo tu entorno de desarrollo:

| ✅ | Tarea |
|---|---|
| ✅ | Xcode instalado en tu Mac |
| ✅ | Swift Playgrounds en Mac y/o iPad |
| ✅ | iPhone conectado y en modo desarrollador |
| ✅ | Apple Developer Account activa |
| ✅ | Primer programa Swift ejecutado |

---

## ➡️ Siguiente módulo

[🔤 Módulo 01 — Swift Básico: Variables y Constantes](../modulo-01-swift-basico/01-variables-y-constantes.md)

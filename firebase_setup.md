# 🔥 GUÍA DE CONEXIÓN FIREBASE + FIRESTORE
## Proyecto: FRESH TARTS

---

## PASO 1 — Crear proyecto en Firebase Console

1. Ve a [https://console.firebase.google.com](https://console.firebase.google.com)
2. Clic en **"Agregar proyecto"**
3. Nombre del proyecto: `fresh-tarts`
4. Desactiva Google Analytics (opcional para desarrollo)
5. Clic **"Crear proyecto"**

---

## PASO 2 — Activar Authentication

1. En la consola de Firebase → **Authentication** → **Comenzar**
2. Pestaña **"Sign-in method"**
3. Activa **"Correo electrónico/contraseña"** → Guardar
4. NO activas link sin contraseña (Email link)

---

## PASO 3 — Crear base de datos Firestore

1. En Firebase Console → **Firestore Database** → **Crear base de datos**
2. Selecciona: **"Comenzar en modo de prueba"** (para desarrollo)
   > ⚠️ Cambia a reglas de producción antes de lanzar
3. Región: `us-central1` (o la más cercana a tus usuarios)
4. Clic **"Listo"**

---

## PASO 4 — Registrar la app Android en Firebase

1. En la consola → ícono de Android (**⊕ Agregar aplicación**) 
2. Nombre del paquete Android: `com.freshtarts.app`
   > ⚠️ Este debe coincidir exactamente con el `applicationId` en `android/app/build.gradle`
3. Apodo de la app: `FRESH TARTS`
4. SHA-1: (opcional para auth básico, requerido para Google Sign-In)
5. Descarga el archivo **`google-services.json`**
6. Colócalo en: `android/app/google-services.json`

---

## PASO 5 — FlutterFire CLI (genera firebase_options.dart)

```bash
# Instala FlutterFire CLI globalmente
dart pub global activate flutterfire_cli

# Dentro de la carpeta del proyecto fresh_tarts:
cd fresh_tarts
flutterfire configure --project=fresh-tarts

# Selecciona: Android (y iOS si lo necesitas)
# Esto genera automáticamente: lib/firebase_options.dart
```

---

## PASO 6 — Estructura de Firestore (colecciones a crear)

Crea estas colecciones manualmente desde la consola de Firebase o con el seed_data:

```
📂 fresh-tarts (proyecto)
 └── 🗄️ Firestore Database
      ├── 📁 roles
      ├── 📁 usuarios
      ├── 📁 clientes
      ├── 📁 proveedores
      ├── 📁 categorias
      ├── 📁 unidades_medida
      ├── 📁 ingredientes
      ├── 📁 recetas
      │    └── 📁 {recetaId}/ingredientes (subcolección)
      ├── 📁 productos
      ├── 📁 inventario
      ├── 📁 pedidos
      │    └── 📁 {pedidoId}/items (subcolección)
      ├── 📁 ventas
      ├── 📁 reseñas
      └── 📁 carrito_items
```

---

## PASO 7 — Crear usuario administrador

### Opción A — Desde Firebase Console:
1. Authentication → Usuarios → **Agregar usuario**
2. Email: `admin@freshtarts.com`
3. Password: `Admin1234!` (cámbiala)
4. Copia el UID generado

### Opción B — Crear documento en Firestore:
En la consola de Firestore:
1. Colección: `roles` → Agregar documento:
```json
{
  "id": "admin",
  "nombre": "Administrador",
  "descripcion": "Rol con acceso total al sistema",
  "is_active": true,
  "created_at": "SERVER_TIMESTAMP",
  "updated_at": "SERVER_TIMESTAMP",
  "created_by": "system",
  "updated_by": "system"
}
```

2. Colección: `usuarios` → Agregar documento con ID = UID del admin:
```json
{
  "email": "admin@freshtarts.com",
  "nombre_completo": "Administrador FRESH TARTS",
  "telefono": "",
  "rol_id": "admin",
  "direccion": "",
  "avatar_url": "",
  "is_active": true,
  "fecha_ultimo_login": null,
  "created_at": "SERVER_TIMESTAMP",
  "updated_at": "SERVER_TIMESTAMP",
  "created_by": "system",
  "updated_by": "system"
}
```

---

## PASO 8 — Aplicar Firestore Security Rules

1. Firebase Console → Firestore → **Reglas**
2. Copia y pega el contenido del archivo `firestore.rules` del proyecto
3. Clic **"Publicar"**

---

## PASO 9 — Configurar android/app/build.gradle

```gradle
android {
    compileSdk 34

    defaultConfig {
        applicationId "com.freshtarts.app"
        minSdk 21
        targetSdk 34
        versionCode 1
        versionName "1.0.0"
        multiDexEnabled true
    }
    ...
}

dependencies {
    implementation platform('com.google.firebase:firebase-bom:33.0.0')
    implementation 'com.google.firebase:firebase-analytics'
}
```

---

## PASO 10 — Configurar android/build.gradle (raíz)

```gradle
buildscript {
    dependencies {
        classpath 'com.google.gms:google-services:4.4.2'
    }
}
```

En `android/app/build.gradle`, al final del archivo:
```gradle
apply plugin: 'com.google.gms.google-services'
```

---

## COMANDOS DE DESARROLLO

```bash
# Instalar dependencias
flutter pub get

# Ejecutar en modo debug (con dispositivo conectado en modo desarrollador)
flutter run

# Compilar APK debug (para instalar en Android modo desarrollador)
flutter build apk --debug

# Compilar APK release (para distribución, requiere keystore)
flutter build apk --release

# Limpiar build cache si hay errores
flutter clean && flutter pub get

# Ver dispositivos disponibles
flutter devices

# Logs en tiempo real
flutter logs
```

---

## INSTALAR APK EN MODO DESARROLLADOR

### En el teléfono Android:
1. **Ajustes** → **Acerca del teléfono** → Toca **"Número de compilación" 7 veces**
2. Regresa a **Ajustes** → **Opciones de desarrollador** → Activa **"Depuración USB"**
3. Conecta el teléfono por USB

### En la computadora:
```bash
# Verificar que el dispositivo es detectado
adb devices

# Instalar el APK directamente
adb install build/app/outputs/flutter-apk/app-debug.apk

# Si el APK ya fue copiado al teléfono, instalar desde ahí
adb install /ruta/al/app-debug.apk
```

### Alternativamente (sin cable):
1. Copia `build/app/outputs/flutter-apk/app-debug.apk` al teléfono
2. En el teléfono → Ajustes → **"Instalar aplicaciones desconocidas"** → Activa para el explorador de archivos
3. Abre el APK desde el explorador de archivos

---

## INICIALIZAR DATOS DE PRUEBA (SEED)

Después de configurar Firebase, ejecuta el seed desde la app:

```dart
// En main.dart (solo en modo debug):
if (kDebugMode) {
  await SeedData.seedFirestore();
}
```

O crea una pantalla de admin oculta para ejecutar el seed manualmente.

---

## VARIABLES DE ENTORNO (.env.example)

```env
# Copia este archivo como .env y reemplaza los valores
# NO subas .env a git (agrega al .gitignore)

FIREBASE_PROJECT_ID=fresh-tarts
FIREBASE_API_KEY=AIza...
FIREBASE_AUTH_DOMAIN=fresh-tarts.firebaseapp.com
FIREBASE_STORAGE_BUCKET=fresh-tarts.appspot.com
FIREBASE_MESSAGING_SENDER_ID=123456789
FIREBASE_APP_ID=1:123456789:android:abc123

ADMIN_EMAIL=admin@freshtarts.com
```

---

## SOLUCIÓN DE PROBLEMAS COMUNES

| Error | Solución |
|-------|----------|
| `google-services.json not found` | Verificar que el archivo esté en `android/app/` |
| `minSdkVersion too low` | Cambiar `minSdk` a **21** en `build.gradle` |
| `FirebaseApp not initialized` | Asegurarse de llamar `await Firebase.initializeApp()` antes de `runApp()` |
| `Permission denied` en Firestore | Revisar las Security Rules y que el usuario esté autenticado |
| `MissingPluginException` | Ejecutar `flutter clean && flutter pub get && flutter run` |
| `Gradle build failed` | Verificar conexión a internet (descarga dependencias) y versión de Java ≥ 17 |
| APK no instala en el teléfono | Activar "Fuentes desconocidas" o "Instalar apps desconocidas" en Ajustes |

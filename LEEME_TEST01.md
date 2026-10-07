# TEST01 — termux-app `v0.119.0-beta.3` + parche 01

## Qué es este repositorio
Es una copia **pristina** del código fuente oficial **termux-app `v0.119.0-beta.3`**
(tal cual el upstream), a la que se le aplica **un único cambio** durante el build,
mediante el parche `01-reactivar-am-socket.patch`:

> Descomentar la llamada `TermuxAmSocketServer.setupTermuxAmSocketServer(context)`
> en `app/src/main/java/com/termux/app/TermuxApplication.java`
> (en el upstream está dentro de un bloque `/* ... */`).

### Lo que NO se toca
- `applicationId` = **`com.termux`** (intacto).
- `versionName` = **`0.119.0-beta.3`**, `versionCode` = **1022** (intactos).
- Manifest, recursos, `termux-shared`, `terminal-emulator`, `terminal-view`, `gradle.properties`
  y el resto del código: **intactos**.
- El texto del parche: **intacto** (idéntico al aprobado).

## Archivos añadidos por TEST01
| Archivo | Para qué |
|---|---|
| `01-reactivar-am-socket.patch` | El parche 01. El CI lo aplica automáticamente. |
| `.github/workflows/build-test01.yml` | Workflow que compila el APK de **debug**. |
| `LEEME_TEST01.md` | Este documento. |
| `INSTRUCCIONES_GITHUB.md` | Paso a paso: subir a GitHub, ejecutar, descargar, verificar. |

> Los workflows **oficiales** del upstream (`debug_build.yml`, `run_tests.yml`, etc.)
> se conservan sin modificar. Sólo se disparan con las ramas `master` / `android-10`,
> PRs a `master`, o *releases*; si usas la rama **`main`** no se ejecutan solos.

## Requisitos de build (los mismos que fija el proyecto)
| Elemento | Valor | Origen |
|---|---|---|
| Runner | `ubuntu-latest` (**x86_64**) | workflow oficial |
| JDK | **11** (Temurin) | `jitpack.yml` → `openjdk11` |
| Gradle | **7.2** (`gradle-wrapper`) | `gradle/wrapper/gradle-wrapper.properties` |
| compileSdk | **30** | `gradle.properties` |
| targetSdk | 28 | `gradle.properties` |
| NdK (ndkVersion) | **22.1.7171670** | `gradle.properties` |
| build-tools | **30.0.3** | *default* de AGP 4.2.2 |
| Android Gradle Plugin | 4.2.2 | `build.gradle` (raíz) |

El workflow instala explícitamente: `platform-tools`, `platforms;android-30`,
`build-tools;30.0.3` y `ndk;22.1.7171670`.

## Reproducir el build en local (x86_64)
```bash
# 0) Requisitos: JDK 11, Android SDK con cmdline-tools, NDK 22.1.7171670
sdkmanager "platform-tools" "platforms;android-30" "build-tools;30.0.3" "ndk;22.1.7171670"

# 1) Aplicar el parche (sobre el código pristino)
git apply --verbose 01-reactivar-am-socket.patch

# 2) Compilar sólo lo necesario
export TERMUX_PACKAGE_VARIANT=apt-android-7   # (o apt-android-5)
./gradlew assembleDebug

# 3) Resultado
ls -l app/build/outputs/apk/debug/*.apk
sha256sum app/build/outputs/apk/debug/*.apk
```

## Salida esperada
Se generan 5 APKs de *debug* (split ABI + universal), **firmados con la test-key pública
del proyecto** (`app/testkey_untrusted.jks`), igual que los builds oficiales de GitHub:

```
termux-app_apt-android-7-debug_universal.apk
termux-app_apt-android-7-debug_arm64-v8a.apk
termux-app_apt-android-7-debug_armeabi-v7a.apk
termux-app_apt-android-7-debug_x86_64.apk
termux-app_apt-android-7-debug_x86.apk
```

> ⚠️ Nunca instales esta test-key en producción. Estos APKs son sólo para pruebas.

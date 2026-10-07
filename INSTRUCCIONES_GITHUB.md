# Instrucciones — Subir TEST01 a GitHub y ejecutar el build (manual)

Todo el proceso es **manual y sin credenciales compartidas**: tú creas el repo,
tú subes el código, tú lanzas el workflow. (No se usa ningún token ni se publica nada.)

---

## A) Qué archivos debes subir
Sube **todo el contenido** de la carpeta `TEST01/` tal cual. En concreto, además del
código fuente pristino de `v0.119.0-beta.3`, destacan estos archivos:

| Ruta | Qué es |
|---|---|
| `01-reactivar-am-socket.patch` | El parche 01 (el CI lo aplica solo). |
| `.github/workflows/build-test01.yml` | **El workflow que compila.** |
| `LEEME_TEST01.md` | Descripción del proyecto. |
| `INSTRUCCIONES_GITHUB.md` | Este documento. |
| *(resto de la carpeta)* | Código fuente oficial pristino (app/, termux-shared/, gradle/, etc.). |

> No subas carpetas `build/` ni `.gradle/` (no existen en esta entrega y están en `.gitignore`).

---

## B) Cómo crear el repositorio en GitHub
1. Entra a <https://github.com/new>.
2. **Repository name**: p. ej. `termux-test01`.
3. **Visibility**: lo que prefieras (Private o Public). Los *artifacts* requieren estar logueado.
4. **IMPORTANTE**: **NO** marques “Add a README / .gitignore / license”
   (el repo debe quedar **vacío**).
5. Pulsa **Create repository**. Copia la URL que aparece, p. ej.
   `https://github.com/TU_USUARIO/termux-test01.git`.

---

## C) Cómo subir el código
Elige **una** de las dos formas.

### C.1 — Línea de comandos (recomendado)
```bash
cd TEST01
git init -b main
git add -A
git commit -m "TEST01: termux-app v0.119.0-beta.3 + patch 01"
git remote add origin https://github.com/TU_USUARIO/termux-test01.git
git push -u origin main
```
> La rama se llama **`main`** a propósito: así los workflows oficiales del upstream
> (que escuchan en `master`) **no** se disparan solos.

### C.2 — Interfaz web (arrastrar y soltar)
1. En el repo vacío, pulsa **“uploading an existing file”**.
2. Arrastra **todo el contenido** de la carpeta `TEST01/` (incluida la carpeta
   `.github`, que es oculta → actívala en el explorador, o súbela aparte).
3. Mensaje de commit → **Commit changes**.

> Si usas la web y te falta `.github/workflows/build-test01.yml`, créalo con
> **Add file → Create new file** y pega el contenido; la ruta exacta es
> `.github/workflows/build-test01.yml`.

---

## D) Cómo ejecutar GitHub Actions (manualmente)
1. En el repo, pestaña **Actions**.
2. Si es la primera vez: pulsa **“I understand my workflows, go ahead and enable them”**.
3. En la barra lateral elige **“Build TEST01 (termux-app v0.119.0-beta.3 + patch 01)”**.
4. Botón **Run workflow ▾** → elige la rama **`main`** →
   variante **`apt-android-7`** (Android 7+;
   usa `apt-android-5` sólo si quieres builds para Android 5/6) → **Run workflow**.
5. La ejecución dura unos **5–15 min**. El paso *“1) Aplicar el parche…”* deja en el
   log la prueba de que el cambio se aplicó.

---

## E) Dónde aparece el APK
Al terminar la ejecución (✅ verde):
1. Entra al run (clic sobre él).
2. Baja hasta la sección **Artifacts** (abajo del todo).
3. Verás **`test01-termux-app-debug-apks`**: dentro están los 5 APKs
   (`*_universal.apk` + 4 por ABI), `output-metadata.json` y `sha256sums.txt`.
   > Los *artifacts* sólo son descargables si estás **logueado** en GitHub.

Los SHA-256 también se muestran en la página del run, en la sección **Summary**.

---

## F) Cómo descargarlo
En **Artifacts → `test01-termux-app-debug-apks`** pulsa el icono de descarga.
Se baja un `.zip` con los APKs. Descomprímelo en tu PC/dispositivo.

---

## G) Cómo verificar el SHA-256
El `.zip` de artifacts ya trae `sha256sums.txt`. Para comprobar integridad:

**Linux / macOS**
```bash
cd <carpeta_descomprimida>
sha256sum -c sha256sums.txt          # Linux
shasum -a 256 -c sha256sums.txt      # macOS
```

**Windows (PowerShell)**
```powershell
Get-FileHash .\termux-app_apt-android-7-debug_universal.apk -Algorithm SHA256
# Compara el hash con el que aparece en sha256sums.txt / en el Summary del run.
```

Si el hash local coincide con el del workflow → el APK está íntegro y es el generado
por esta ejecución.

---

## Notas de seguridad
- Estos APKs van firmados con la **test-key pública** del proyecto
  (`app/testkey_untrusted.jks`): sirven **sólo para pruebas**. No los uses en producción.
- No se instala nada en ningún dispositivo: el workflow únicamente **genera APKs**.
- No compartas tokens ni credenciales; este flujo no los necesita.

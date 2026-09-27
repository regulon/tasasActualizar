# CalculoTasasApp — empaquetado a APK

Esta carpeta contiene tu `main.py` (app Kivy que muestra tu Streamlit en un WebView
de Android) más lo necesario para compilarla como APK.

## Opción A — Compilar en la nube con GitHub Actions (recomendado, no requiere instalar nada)

1. Creá un repositorio nuevo en GitHub (puede ser privado).
2. Subí **todo el contenido de esta carpeta** (incluida la carpeta oculta `.github/`)
   a la raíz del repo.
3. Andá a la pestaña **Actions** de tu repo en GitHub. Si no arrancó sola, corré el
   workflow manualmente: Actions → "Build APK" → "Run workflow".
4. Esperá a que termine (la primera vez tarda bastante, ~20-30 min, porque descarga
   el SDK/NDK de Android). Los builds siguientes son más rápidos por el cache.
5. Cuando termine, entrá al run finalizado y bajá el archivo desde la sección
   **Artifacts** (se llama "package"). Ahí adentro está tu `.apk`.

## Opción B — Compilar localmente (Linux o WSL en Windows)

Buildozer **no funciona en Windows nativo**, necesitás Linux, WSL2, o una VM.

```bash
sudo apt update
sudo apt install -y python3-pip build-essential git python3-dev \
    ffmpeg libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-ttf-dev \
    libportmidi-dev libswscale-dev libavformat-dev libavcodec-dev zlib1g-dev \
    openjdk-17-jdk unzip

pip3 install --user buildozer cython

cd webapp_project
buildozer android debug
```

El `.apk` queda en `webapp_project/bin/`. La primera compilación baja el SDK/NDK de
Android (varios GB) así que puede tardar bastante.

## Notas sobre buildozer.spec

- `requirements`: incluí `pyjnius` (obligatorio para el `autoclass` que usa tu
  código para acceder al WebView nativo) y `openpyxl` (estaba importado en tu
  `main.py`, aunque no se usa en la lógica actual — si no lo necesitás, podés
  sacarlo para que compile más rápido).
- `android.permissions = INTERNET`: imprescindible, sin esto el WebView no va a
  poder cargar tu URL de Streamlit.
- Si más adelante agregás un ícono o splash screen, descomentá y completá las
  líneas `icon.filename` / `presplash.filename` en el `.spec`.
- Para generar un APK firmado para Play Store en vez de uno de debug, el comando
  cambia a `buildozer android release` y requiere generar un keystore aparte.

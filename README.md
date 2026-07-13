# ZEROED — Rutina Aim Lab

App de racha diaria para tu rutina de Aim Lab (Multishot Ultimate ×10, Gridshot Speed ×3), lista para instalar en el móvil como si fuera una app nativa.

## Qué hay en esta carpeta

```
aimlab-app/
├── index.html          → la app entera (HTML/CSS/JS, sin dependencias de build)
├── manifest.json        → metadatos para que el móvil la reconozca como app instalable
├── sw.js                 → service worker (cache offline)
└── icons/
    ├── icon-192.png
    ├── icon-512.png
    ├── icon-512-maskable.png
    └── favicon.png
```

Guarda los datos con `localStorage`, así que cada persona que la instale tiene su propia racha en su propio móvil — no hace falta servidor ni base de datos.

## 1. Probarla en local

No necesitas Node ni nada instalado, solo un servidor estático (los navegadores bloquean los service workers si abres el archivo directamente con `file://`):

```bash
cd aimlab-app
python3 -m http.server 8080
```

Abre `http://localhost:8080` en el móvil (misma red WiFi que el ordenador, usando la IP local en vez de `localhost`) o en el propio ordenador.

## 2. Publicarla en internet (gratis)

Para que tus amigos la instalen necesitas una URL real con HTTPS. Opciones gratuitas, sin backend:

- **GitHub Pages**: sube la carpeta a un repo de GitHub → Settings → Pages → Deploy from branch. Te da una URL tipo `https://tuusuario.github.io/aimlab-app/`.
- **Netlify** o **Vercel**: arrastra la carpeta a [app.netlify.com/drop](https://app.netlify.com/drop) y listo, sin cuenta siquiera para la versión drop.
- **Cloudflare Pages**: similar, conectando el repo o subiendo directamente.

Cualquiera de las tres te sirve. HTTPS es obligatorio para que el "Instalar app" funcione en Android/iOS.

## 3. Instalarla como app (sin APK)

Una vez publicada, cualquiera que abra el link puede:

- **Android (Chrome)**: menú ⋮ → "Instalar app" o "Añadir a pantalla de inicio".
- **iPhone (Safari)**: botón compartir → "Añadir a pantalla de inicio".

Se abre a pantalla completa, con icono propio, y funciona offline. Esto ya cubre el 90% de "es como una app" sin pasar por ningún store.

## 4. Si de verdad quieres un `.apk` instalable

Esto convierte la PWA en un APK real que se puede instalar directamente en Android (fuera de Google Play) o subir a la Play Store:

**Opción fácil — PWABuilder (sin tocar código):**
1. Publica la app (paso 2).
2. Ve a [pwabuilder.com](https://www.pwabuilder.com), pega tu URL.
3. Te analiza el manifest y el service worker automáticamente.
4. Pestaña "Android" → genera el paquete APK/AAB firmado, listo para descargar.

**Opción manual — Bubblewrap (línea de comandos, más control):**
```bash
npm i -g @bubblewrap/cli
bubblewrap init --manifest=https://tu-url.com/manifest.json
bubblewrap build
```
Esto usa Android Studio/Gradle por debajo y te deja un `.apk` firmado en la carpeta del proyecto.

Ambas opciones generan lo que se llama una **TWA (Trusted Web Activity)**: un APK real que abre tu PWA a pantalla completa sin barra de navegador, indistinguible de una app nativa para el usuario.

## 5. Notificaciones push reales (OneSignal) — YA IMPLEMENTADO, falta tu configuración

El código ya está metido en `index.html` y `sw.js`. Solo te quedan 3 pasos:

1. **Crea cuenta gratis** en [onesignal.com](https://onesignal.com) → "New App/Website" → tipo **Web Push** → dale la URL donde vayas a publicar la app (paso 2 más arriba, necesitas la URL primero).
2. Copia el **OneSignal App ID** que te dan y pégalo en `index.html`, sustituyendo esta línea:
   ```js
   const ONESIGNAL_APP_ID = 'ONESIGNAL_APP_ID_AQUI';
   ```
   por tu ID real, entre comillas.
3. En el dashboard de OneSignal, ve a **Messages → Journeys** (ya no se llama "Automated Messages", eso lo quitaron para apps nuevas y planes gratis) y crea un Journey con un disparador **diario recurrente** a la hora que quieras — el plan gratis te deja 1 Journey, que es justo lo que necesitas para esto.

Con eso, el botón de la campanita 🔔 en la app pedirá permiso de notificaciones al usuario, y OneSignal mandará el aviso todos los días a esa hora — funciona aunque el móvil lleve horas sin abrir la app, porque el aviso lo dispara el servidor de OneSignal, no el navegador.

**Nota:** cada amigo que instale la app tiene que darle al 🔔 y aceptar el permiso desde su propio móvil — no puedes activarlo tú por ellos.


## Personalizar

- Cambiar los ejercicios de la rutina: edita el array `DEFAULT_ROUTINE` en `index.html` (también se pueden añadir/quitar desde la propia app).
- Cambiar colores/tema: variables CSS al principio de `index.html` (`:root { --phosphor: ... }`).
- Cambiar el icono: sustituye los PNG en `icons/` manteniendo los mismos nombres y tamaños.

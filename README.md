# INERGO — Te toca.

Single Page Web App (HTML + Tailwind CDN + JavaScript vanilla), instalable como **app (PWA)** tanto en móvil como en escritorio. Sin backend, sin build, sin dependencias que instalar. Todo el estado se guarda en `localStorage` del navegador.

**Flujo:** grabar pantalla → abrir INERGO → elegir categoría → PLAY → animación de selección → aparece el reto → TE TOCA → EMPEZAR → temporizador → LO HE HECHO.

No existe ningún botón para rechazar, cambiar o saltar un reto una vez revelado. Esa es la regla central de la app.

---

## Estructura del proyecto

```
inergo/
├── inergo.html              ← toda la app (HTML, CSS y JS en un único archivo)
├── manifest.json            ← metadatos de la PWA (nombre, iconos, colores)
├── sw.js                    ← service worker (permite instalarla y abrirla sin conexión)
├── vercel.json              ← hace que la URL raíz cargue inergo.html automáticamente
├── icon-192.png
├── icon-512.png
├── icon-maskable-512.png    ← icono con margen de seguridad para Android
└── apple-touch-icon.png     ← icono para "Añadir a pantalla de inicio" en iOS
```

Todos los archivos deben subirse juntos, en la misma carpeta — `manifest.json` y `sw.js` referencian los iconos y `inergo.html` con rutas relativas.

> **Si vienes de una versión anterior del proyecto:** el archivo principal se llamaba `index.html` y ahora se llama `inergo.html`. Borra el `index.html` viejo de tu repositorio de GitHub para que no queden los dos sueltos a la vez, y sube `vercel.json` (es nuevo).

## Ejecutar en local

No requiere servidor ni instalación para probar la app en el navegador:

1. Descarga toda la carpeta `inergo` (no solo `inergo.html`).
2. Haz doble clic sobre `inergo.html` para abrirlo, o arrástralo a una pestaña.
3. Listo — funciona igual en desktop y en móvil.

**Para instalarla como app** (icono, pantalla completa, funciona sin conexión) hace falta servirla por `http://` o `https://` — abrir el archivo directamente con `file://` no activa el service worker. Sírvela así:

```bash
cd inergo
python3 -m http.server 8000
# abre http://localhost:8000/inergo.html en el navegador
# o, desde el móvil en la misma red: http://TU_IP_LOCAL:8000/inergo.html
```

## Instalar como app

### En iPhone / iPad (Safari)
1. Abre la URL de la app en Safari.
2. Toca el icono de **Compartir** (el cuadrado con la flecha hacia arriba).
3. Baja hasta **"Añadir a pantalla de inicio"** y confirma.
4. Aparecerá un icono de INERGO en tu pantalla de inicio; al abrirlo, se abre a pantalla completa, sin la barra de Safari.

### En Android (Chrome)
1. Abre la URL de la app en Chrome.
2. Toca el menú (los tres puntos, arriba a la derecha).
3. Selecciona **"Instalar app"** o **"Añadir a pantalla de inicio"**.
4. Se instala como una app más, con su propio icono y su propia ventana.

### En ordenador (Chrome, Edge)
1. Abre la URL de la app.
2. En la barra de direcciones, a la derecha, aparecerá un icono de instalación (una pantalla con una flecha, o "+").
3. Pulsa **Instalar**. Se abre como una ventana independiente, sin las pestañas ni la barra del navegador.

> La app también funciona perfectamente como página web normal, sin instalarla — instalarla solo añade el icono, la pantalla completa y el acceso sin conexión.

---

## Desplegar en Vercel

1. Crea una cuenta gratuita en [vercel.com](https://vercel.com) (puedes registrarte con GitHub, GitLab o email).
2. En el dashboard, pulsa **Add New → Project**.
3. Si tu código está en GitHub: importa el repositorio que contiene la carpeta `inergo`. Si no usas Git, pulsa la pestaña que permite **subir una carpeta directamente** (Vercel CLI, ver abajo, es la vía más rápida sin Git).
4. **Configurar el proyecto:** al ser HTML estático sin build, deja el *Framework Preset* en **Other**, el *Build Command* vacío y el *Output Directory* como `.` (raíz).
5. Pulsa **Deploy**.
6. En unos segundos obtendrás una URL del tipo `https://tu-proyecto.vercel.app`. Gracias al archivo `vercel.json`, esa URL raíz carga automáticamente `inergo.html` sin que tengas que escribir nada más al final. Al ser `https://`, el service worker funciona automáticamente y la app ya se puede instalar.
7. **Dominio propio (opcional):** en el proyecto, ve a **Settings → Domains**, añade tu dominio y sigue las instrucciones para apuntar los registros DNS (CNAME o A) que te indique Vercel.

### Alternativa rápida sin usar la web (Vercel CLI)

```bash
npm install -g vercel
cd inergo
vercel
# sigue las preguntas en pantalla (login, nombre de proyecto, etc.)
vercel --prod   # para publicar la versión definitiva
```

---

## Desplegar en Netlify

1. Crea una cuenta gratuita en [netlify.com](https://netlify.com).
2. En el dashboard, pulsa **Add new site → Deploy manually** (o "Import from Git" si tu código está en GitHub).
3. **Deploy manual:** arrastra la carpeta `inergo` completa (con todos sus archivos, no solo `inergo.html`) al recuadro de subida.
4. Netlify sube y publica el proyecto automáticamente — no hay build que configurar porque es HTML estático.
5. En unos segundos obtendrás una URL del tipo `https://nombre-aleatorio.netlify.app`, ya en `https://` y lista para instalarse.
6. **Importante en Netlify:** el archivo `vercel.json` no le sirve a Netlify (es específico de Vercel). Si despliegas aquí, la URL raíz no cargará `inergo.html` sola — tendrás que compartir la URL completa `https://tu-sitio.netlify.app/inergo.html`, o renombrar `inergo.html` a `index.html` solo para este despliegue.
7. **Dominio propio (opcional):** en el sitio, ve a **Site configuration → Domain management → Add a domain**, añade tu dominio y sigue las instrucciones DNS que te indique Netlify.

### Alternativa con Netlify CLI

```bash
npm install -g netlify-cli
cd inergo
netlify deploy          # deploy de prueba
netlify deploy --prod   # deploy definitivo
```

---

## Notas técnicas

- **Sin backend ni base de datos.** Todo el progreso (XP, racha, historial, retos personalizados, retos pendientes) vive en `localStorage`, por lo que es local a cada navegador/dispositivo — instalar la app en el móvil y en el ordenador crea dos progresos independientes.
- **PWA (Progressive Web App):** `manifest.json` define nombre, iconos y colores; `sw.js` es el service worker que cachea la app para que abra incluso sin conexión una vez visitada por primera vez. Solo funciona instalada/servida por `https://` (o `localhost`); no funciona abriendo `inergo.html` directamente con doble clic.
- **Tailwind vía CDN** — no requiere `npm install` ni proceso de build.
- **Web Audio API** para los sonidos (click, ticks, revelación, completar) — no hay archivos de audio externos.
- Borrar los datos del navegador (o usar modo incógnito, o desinstalar la app) reinicia el progreso.
- Los retos de la app están diseñados para generar exposición e incomodidad social, nunca situaciones peligrosas, ilegales o que invadan la privacidad de terceros.


# Venta de Garaje Institucional · Alliance Française

Aplicación web de una sola página (SPA) para gestionar la venta de mobiliario.
Los visitantes pueden reservar artículos; el administrador puede marcarlos como
**Vendido** o devolverlos a **Disponible**. Todo se sincroniza en tiempo real
mediante **Firebase Realtime Database**.

- Frontend: HTML5 + Tailwind CSS (vía CDN) + Vanilla JS.
- Backend / persistencia: Firebase Realtime Database.
- Sin build, sin instalación. Es un único archivo: `index.html`.

---

## 1) Crear el proyecto en Firebase (gratis)

1. Entra a <https://console.firebase.google.com/> con tu cuenta de Google.
2. Pulsa **Agregar proyecto** → escribe un nombre (ej. `venta-alianza`) →
   *Continuar* → puedes **desactivar Google Analytics** (no es necesario) →
   *Crear proyecto*.
3. En el menú lateral entra a **Build → Realtime Database** → *Crear base de
   datos* → elige la región más cercana → empieza en **modo de prueba**
   (luego pegaremos reglas más estrictas).
4. En el icono ⚙️ (arriba a la izquierda) entra a **Configuración del
   proyecto → Tus apps**. Pulsa el icono **`</>`** para registrar una app web:
   - Apodo: `Venta Alianza` → *Registrar app*.
   - Copia el objeto `firebaseConfig` que aparece.

## 2) Pegar las credenciales en el código

Abre `index.html` y busca el bloque marcado:

```js
const firebaseConfig = {
  apiKey: "TU_API_KEY",
  authDomain: "TU_PROYECTO.firebaseapp.com",
  databaseURL: "https://TU_PROYECTO-default-rtdb.firebaseio.com",
  ...
};
```

Reemplaza **cada valor** con el de tu proyecto.

> 🔎 **IMPORTANTE:** Asegúrate de incluir `databaseURL`. Si no aparece en la
> configuración que te dio Firebase, cópialo de la pestaña **Realtime
> Database** (algo como `https://venta-alianza-default-rtdb.firebaseio.com`).

## 3) Cambiar la contraseña de administrador

En el mismo `index.html`, modifica:

```js
const ADMIN_PASSWORD = "alliance2026";
```

⚠️ Esta contraseña vive en el código fuente del navegador y NO es secreta —
sólo evita el acceso casual. Para una seguridad real, activa **Firebase
Authentication** y protege con reglas (ver paso 5).

## 4) Reglas de seguridad recomendadas

En **Realtime Database → Reglas**, pega el contenido de `firebase-rules.json`
y publica. Estas reglas permiten:

- Lectura pública del catálogo.
- Escritura pública (necesaria para que cualquier visitante pueda reservar y
  para que el "administrador" del cliente pueda marcar vendido).
- Validan el formato (estado válido, precios numéricos, etc.).

### Opción más segura (recomendada para producción)

Si quieres que **sólo el administrador** pueda marcar como vendido o devolver
a disponible:

1. Activa **Authentication → Email/contraseña** en Firebase y crea un usuario
   admin.
2. Cambia las reglas para limitar la escritura de `status: sold` y la limpieza
   de `reservation` a `auth.uid` específico.
3. Sustituye el login estático del HTML por `signInWithEmailAndPassword`.

(El template actual prioriza la simplicidad: una sola página, sin login real.)

## 5) Publicar gratis

### Opción A — Vercel (más rápido, recomendado)

1. Crea una cuenta en <https://vercel.com> con tu cuenta de GitHub.
2. Pulsa **Add New → Project** → importa el repositorio
   `migueltillero-ship-it/Ventadegarajealianza`.
3. En "Framework Preset" elige **Other**. No hace falta `build command`.
   El directorio raíz contiene el `index.html`.
4. Deploy. Vercel te dará un enlace tipo
   `https://ventadegarajealianza.vercel.app`.

### Opción B — GitHub Pages

1. En GitHub, entra a **Settings → Pages** del repositorio.
2. En "Source" elige la rama `main` (o la que tenga el `index.html`) y la
   carpeta `/ (root)` → *Save*.
3. En 1–2 minutos tendrás una URL tipo
   `https://migueltillero-ship-it.github.io/Ventadegarajealianza/`.

### Opción C — Netlify

1. En <https://app.netlify.com>, arrastra la carpeta del proyecto al área de
   *deploys*. Listo.

> Después de publicar, **vuelve a Firebase → Authentication → Settings →
> Authorized domains** y añade el dominio de Vercel/Pages/Netlify para que la
> base de datos acepte conexiones desde ahí. *(Realtime Database lo permite por
> defecto, pero conviene tenerlo si en el futuro activas Auth.)*

## 6) Uso

- **Catálogo público:** abre la URL pública. La galería muestra en verde,
  amarillo o rojo el estado de cada artículo. Cada cambio se ve **en tiempo
  real** en todas las pantallas abiertas.
- **Reservar:** clic en *Reservar* → captura tu nombre y teléfono/correo →
  confirmar.
- **Modo administrador:** pulsa el botón *Admin* arriba a la derecha **o**
  añade `?admin=true` al final de la URL. Escribe la contraseña configurada.
  Verás los datos de contacto de quien reservó y los botones para *Marcar
  como vendido* y *Volver a disponible*.

## 7) Inventario inicial

Se siembra automáticamente la primera vez que la app se conecta y la base de
datos está vacía. Después puedes editar artículos directamente en
**Realtime Database → Datos** dentro de la consola de Firebase (cambiar
fotos, precios, agregar nuevos artículos con la misma estructura).

Estructura de un artículo:

```json
"escritorio": {
  "title": "Escritorio",
  "price": 3000,
  "description": "Texto opcional",
  "image": "https://url-de-la-imagen.jpg",
  "status": "available"
}
```

## 8) Soporte

Cualquier cambio (nuevas imágenes, precios, textos) se hace editando
`index.html` y haciendo *commit*; Vercel/Pages republicarán automáticamente.

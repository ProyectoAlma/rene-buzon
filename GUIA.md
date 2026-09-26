# Buzón de archivos · René Boiero — Guía de puesta en marcha

Esta app deja que cualquier persona del equipo **arrastre sus archivos** y caigan
directo en una **Unidad compartida** de Google Drive de la empresa. Como quien
sube es una *cuenta de servicio*, **los archivos son propiedad de la empresa**
automáticamente (no de quien los sube). Sin la danza de compartir, sin el límite
de 6 minutos del script.

> ⏱️ Tiempo total de configuración: ~20 minutos, una sola vez.

---

## Paso 1 — Crear la Unidad compartida (2 min)

1. Entrá a Google Drive con la cuenta de **Workspace de René Boiero**.
2. Barra izquierda → **Unidades compartidas** → **Nueva** → nombrala, ej.
   `Buzón - Material del equipo`.
3. Abrila y mirá la URL:
   `https://drive.google.com/drive/folders/0AXXXXXXXXXXXXXXXX`
   Ese `0A...` es el **ID de la Unidad compartida**. Anotalo (va en los secrets).

*(Opcional: dentro creá una subcarpeta base y usá su ID en `base_folder_id`.
Si lo dejás vacío, todo cae en la raíz de la unidad.)*

---

## Paso 2 — Crear la cuenta de servicio (10 min)

1. Andá a **console.cloud.google.com** con tu cuenta de René Boiero.
2. Arriba, **Crear proyecto** (ej. `buzon-rene`). Seleccionalo.
3. Buscador → **"Google Drive API"** → **Habilitar**.
4. Menú → **API y servicios → Credenciales** → **Crear credenciales →
   Cuenta de servicio**. Nombre: `buzon-rene`. Crear y continuar → Listo.
5. Entrá a esa cuenta de servicio → pestaña **Claves** → **Agregar clave →
   Crear clave nueva → JSON**. Se descarga un archivo `.json`. 🔒 **Esa es la
   clave secreta: no la subas a ningún lado público.**
6. Copiá el **email** de la cuenta de servicio (algo como
   `buzon-rene@buzon-rene.iam.gserviceaccount.com`).

## Paso 3 — Darle acceso a la Unidad compartida (1 min)

1. Volvé a la **Unidad compartida** del Paso 1 → **Administrar miembros**.
2. Pegá el **email de la cuenta de servicio** y dale rol
   **Administrador de contenido** (Content manager). Guardar.

> Con esto la app puede escribir en esa unidad — y **solo en esa unidad**.

---

## Paso 4 — Publicar la app en Streamlit Community Cloud (gratis, 5 min)

1. Subí esta carpeta a un repo de GitHub (yo te lo dejo listo si querés).
   **Nunca subas la clave `.json` ni `secrets.toml`** (el `.gitignore` ya los ignora).
2. Entrá a **share.streamlit.io** → *New app* → elegí el repo → archivo `app.py`.
3. **Advanced settings → Secrets** → pegá esto (reemplazando por lo tuyo):

   ```toml
   [gcp_service_account]
   # 👉 pegá acá TODO el contenido del .json, campo por campo.
   #    (type, project_id, private_key_id, private_key, client_email, ...)
   type = "service_account"
   project_id = "buzon-rene"
   private_key_id = "..."
   private_key = "-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n"
   client_email = "buzon-rene@buzon-rene.iam.gserviceaccount.com"
   client_id = "..."
   auth_uri = "https://accounts.google.com/o/oauth2/auth"
   token_uri = "https://oauth2.googleapis.com/token"
   auth_provider_x509_cert_url = "https://www.googleapis.com/oauth2/v1/certs"
   client_x509_cert_url = "..."

   [drive]
   shared_drive_id = "0AXXXXXXXXXXXXXXXX"
   base_folder_id = ""
   access_code = "rene2026"
   ```

   > Truco para `private_key`: en el `.json` la clave viene con `\n`. Pegala tal
   > cual, entre comillas, en una sola línea. Streamlit la interpreta bien.

4. **Deploy**. En 1–2 minutos tenés una URL tipo `https://rene-buzon.streamlit.app`.

---

## Paso 5 — Listo: compartí con el equipo

Mandales la **URL** + el **código de acceso** (`access_code`). La app tiene **dos
formas de cargar** (dos pestañas):

**📁 Desde mi computadora** — la persona pone su nombre, elige el tipo de archivo,
arrastra y sube. Ideal para grabaciones/fotos que tiene en la compu o el celu.

**☁️ Desde mi Google Drive** — la persona pega el **link** de una carpeta o archivo
que ya tiene en su Drive y se **copia enterita** a la Unidad compartida (útil para
migrar material que ya vive en Drive). Para que la app pueda leer ese link, quien
sube tiene que **compartir** primero esa carpeta:
- **Compartir → agregar el email de la app** (`...@...iam.gserviceaccount.com`,
  la app lo muestra en pantalla) como **Lector**, o
- ponerla en **“Cualquiera con el enlace”**.

En los dos casos, todo aparece ordenado en la Unidad compartida así:

```
Unidad compartida/
└── Nombre de la persona/
    └── Podcast / Audio, Video, Fotos, Documentos… (o el nombre de la carpeta copiada)/
        └── (sus archivos)
```

---

## Notas y límites

- **Tamaño**: configurado a 2 GB por archivo (`.streamlit/config.toml`).
  Streamlit Community Cloud tiene ~1 GB de RAM, así que **videos muy pesados
  (varios GB)** pueden fallar ahí. Para ese caso: subir el video directo a la
  Unidad compartida, o publicar la app en **Google Cloud Run** (más músculo).
- **Seguridad**: la cuenta de servicio solo ve la unidad que le compartiste.
  El `access_code` evita que suba cualquiera con el link. Si más adelante querés,
  se puede cambiar por *"Iniciar sesión con Google"* limitado al dominio.
- **Costo**: $0. Drive de la empresa + Streamlit Community Cloud, ambos gratis.

¿Dudas en algún paso? Te lo hago junto/a por pantalla.

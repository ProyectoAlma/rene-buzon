# Buzón de archivos · René Boiero

App en Streamlit para que el equipo cargue archivos —desde la computadora o
desde su Google Drive— directo a una **Unidad compartida** de Google Drive de la
empresa (los archivos quedan como propiedad de la empresa).

- `app.py` — la aplicación (dos modos: subir desde la compu / copiar desde Drive).
- `GUIA.md` — puesta en marcha paso a paso (Unidad compartida + cuenta de servicio + deploy).
- `.streamlit/secrets.toml.example` — plantilla de secrets (la clave real NO se sube).

## Correr local
```
pip install -r requirements.txt
streamlit run app.py
```
Sin secrets arranca en modo vista previa. Ver **GUIA.md** para conectarlo.

# Proyecto: Web Page con Chatbot Integrado

Este proyecto contiene los dos entregables solicitados y los archivos para ejecutarlos:

1. `index.html`: landing page lista para publicar en GitHub Pages.
2. `n8n-chatbot-flow.json`: flujo de n8n para importar y activar el chatbot con Gemini.
3. `docker-compose.yml`: configuracion para levantar n8n local con Docker.
4. `.env.example`: plantilla para guardar la API key de Gemini sin ponerla en la pagina web.

## 0. Levantar n8n local con Docker

1. Instala Docker Desktop.
2. Copia `.env.example` como `.env` si todavia no existe.
3. En `.env`, guarda tu API key de Gemini. Ese archivo esta excluido por `.gitignore` y nunca debe subirse a GitHub:

```text
GEMINI_API_KEY=tu_api_key_real
```

4. Desde esta carpeta, ejecuta:

```powershell
docker compose up -d
```

Si PowerShell no encuentra `docker`, usa la ruta de la instalacion por usuario:

```powershell
& "$env:LOCALAPPDATA\Programs\DockerDesktop\resources\bin\docker.exe" compose up -d
```

5. Abre n8n en:

```text
http://localhost:5678
```

## 1. Importar el flujo en n8n

1. Entra a n8n.
2. Crea un workflow nuevo.
3. Usa la opcion **Import from file**.
4. Selecciona `n8n-chatbot-flow.json`.
5. Activa el workflow.
6. Copia la URL de produccion del Webhook. Debe terminar en:

```text
/webhook/chatbot-curcuma-amador
```

## 2. Exponer n8n con un tunel publico

Si n8n corre localmente, GitHub Pages no puede llamarlo usando `localhost`.
Para que el profesor pueda probar el chatbot, manten Docker Desktop y n8n encendidos y usa un tunel publico.
Un dominio de desarrollo asignado por ngrok permanece estable entre reinicios del tunel; requiere una cuenta gratuita y un authtoken local. Cloudflare Quick Tunnel no requiere cuenta, pero su URL cambia cada vez que se inicia.

Con Docker, una opcion rapida es ejecutar:

```powershell
docker run --rm -it cloudflare/cloudflared:latest tunnel --url http://host.docker.internal:5678
```

Copia la URL publica que entrega Cloudflare. Sera parecida a:

```text
https://algo-random.trycloudflare.com
```

## 3. Conectar la landing page con n8n

Abre `index.html` y busca esta linea:

```js
const N8N_WEBHOOK_URL = "https://TU-TUNEL-PUBLICO/webhook/chatbot-curcuma-amador";
```

Reemplaza esa URL por la URL publica del tunel que apunte a tu n8n local.

## 4. Publicar en GitHub Pages

1. Crea un repositorio nuevo en GitHub.
2. Sube el archivo `index.html`.
3. Ve a **Settings > Pages**.
4. En **Branch**, selecciona `main`.
5. Guarda los cambios.
6. Espera unos segundos y copia el enlace publico generado por GitHub Pages.

## 5. Entrega formal

Entrega:

- Enlace de la pagina publicada en GitHub Pages.
- Archivo exportado `.json` del workflow de n8n.

## Nota

El chatbot solo responde cuando el workflow de n8n esta activo y la URL publica del webhook esta configurada en `index.html`. Si el tunel se detiene, la pagina permanece publicada pero el asistente deja de responder.

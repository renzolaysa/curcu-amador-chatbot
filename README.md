# Proyecto: Web Page con Chatbot Integrado

Este proyecto contiene los dos entregables solicitados y los archivos para ejecutarlos:

1. `index.html`: landing page publicada en https://renzolaysa.github.io/curcu-amador-chatbot/.
2. `n8n-chatbot-flow.json`: exportacion real del flujo publicado en n8n.
3. `docker-compose.yml` y `nginx.conf`: n8n local, proxy limitado al webhook y tunel publico.
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
5. Publica el workflow.
6. Copia la URL de produccion del Webhook. Debe terminar en:

```text
/webhook/chatbot-curcuma-amador
```

## 2. Exponer n8n con un tunel publico

Si n8n corre localmente, GitHub Pages no puede llamarlo usando `localhost`.
El servicio `tunnel` en `docker-compose.yml` crea una URL publica temporal y apunta al proxy `webhook-proxy`. Este proxy solo admite POST al webhook del chatbot y no expone el panel de n8n.
Desde la carpeta del proyecto, ejecuta:

```powershell
docker compose up -d
docker compose logs --tail=30 tunnel
```

Copia la URL publica que entrega Cloudflare. La URL actual es:

```text
https://rome-systematic-rides-gore.trycloudflare.com
```

La URL de Quick Tunnel cambia cuando el tunel se reinicia. Para una entrega que deba funcionar despues de reiniciar Docker, hay que sustituirla por un tunel con dominio estable y actualizar `index.html`. Mantener Docker y la PC encendidos es necesario en ambos casos.

## 3. Conectar la landing page con n8n

Abre `index.html` y busca esta linea:

```js
const N8N_WEBHOOK_URL = "https://rome-systematic-rides-gore.trycloudflare.com/webhook/chatbot-curcuma-amador";
```

Reemplaza esa URL si el tunel se reinicia y cambia de direccion. Vuelve a publicar el archivo en GitHub Pages despues del cambio.

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

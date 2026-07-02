# Flujo n8n — Ping automático de indexación al actualizar /llm-feed/

## Diagrama lógico

[1. Trigger] → [2. Validación] → [3. Regenerar sitemap] → [4. IndexNow ping] → [5. Google ping] → [6. Log + aviso WhatsApp]

## Nodos

1. TRIGGER — Webhook (POST /webhook/llm-feed-update)
   Payload esperado: { "url": "https://germanmanzur.com.ar/llm-feed/xxx.md", "accion": "nueva|actualizada|eliminada" }
   Alternativa sin webhook: nodo Cron cada 6h + HTTP Request que compara hash del index.md.

2. IF — Validación
   url empieza con https://germanmanzur.com.ar/ → continuar; si no → responder 400.

3. HTTP Request — Regenerar sitemap
   GET https://germanmanzur.com.ar/regenerar-sitemap (script PHP/estático del hosting)
   o nodo Code que reescribe sitemap.xml con <lastmod> de hoy y lo sube por FTP/SSH.

4. HTTP Request — IndexNow (cubre Bing → y Bing alimenta a ChatGPT Search y parte de Perplexity)
   POST https://api.indexnow.org/indexnow
   Body JSON:
   {
     "host": "germanmanzur.com.ar",
     "key": "TU_CLAVE_INDEXNOW",
     "keyLocation": "https://germanmanzur.com.ar/TU_CLAVE_INDEXNOW.txt",
     "urlList": ["{{ $json.url }}", "https://germanmanzur.com.ar/llm-feed/index.md"]
   }
   Requisito previo: generar clave en Bing Webmaster Tools y subir el archivo TU_CLAVE_INDEXNOW.txt a la raíz.

5. HTTP Request — Google sitemap ping
   GET https://www.google.com/ping?sitemap=https://germanmanzur.com.ar/sitemap.xml
   Nota: Google no soporta IndexNow; el camino real es Search Console + sitemap. Este ping es best-effort.

6. Paralelo final:
   a) Google Sheets/Airtable — log: fecha, url, respuesta IndexNow (200/202 = OK).
   b) WhatsApp (vía Nico /report o API): "llm-feed actualizado y pingueado: {{url}}".

## Manejo de errores
Nodo Error Trigger global → aviso WhatsApp "Fallo ping indexación: {{error}}". Reintento: 3 veces, backoff 5 min.

## Realidad operativa (importante)
- IndexNow acelera Bing/Yandex en minutos-horas; ChatGPT Search y Perplexity beben del índice de Bing → este ping es el de mayor impacto GEO real.
- Google: alta en Search Console + sitemap actualizado. No hay push API pública para contenido normal (la Indexing API es solo para empleos/eventos en vivo).
- Claude/Anthropic: no hay API de ping; el rastreo depende de enlaces entrantes y robots.txt abierto.

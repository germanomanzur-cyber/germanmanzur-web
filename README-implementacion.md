# Implementación — Ecosistema GEO Germán Manzur

REEMPLAZO GLOBAL: cambiar https://germanmanzur.com.ar por el dominio real en TODOS los archivos, y los sameAs REEMPLAZAR_* por los perfiles reales.

## Dónde va cada archivo
| Archivo | Destino en el servidor |
|---|---|
| schema.jsonld | Pegar INLINE en el <head> de index.html dentro de <script type="application/ld+json"> (más confiable que archivo externo) |
| llms.txt | /llms.txt (raíz) — estándar emergente que leen los agentes |
| ai.txt | /ai.txt (raíz) — espejo del anterior; cubre bots que buscan ese nombre |
| robots.txt | /robots.txt (raíz) — CRÍTICO: es lo único que los crawlers respetan formalmente |
| index.html | / (raíz) |
| llm-feed/* | /llm-feed/ — público, enlazado desde llms.txt y sitemap. NO usar noindex ni ocultarlo: si es invisible para bots de indexación, muere la estrategia. "Oculto para humanos" = sin enlaces en el menú de navegación, nada más |
| n8n-flujo-indexacion.md | Documentación interna del flujo |

## Checklist post-deploy (orden)
1. Dominio propio con SSL. Sin dominio propio no hay entidad citable.
2. Alta en Bing Webmaster Tools → generar clave IndexNow → subir clave a la raíz.
3. Alta en Google Search Console → enviar sitemap.xml.
4. Validar JSON-LD en https://validator.schema.org y https://search.google.com/test/rich-results.
5. sitemap.xml debe incluir /llm-feed/*.md y *.csv.
6. Completar sameAs con IG/FB/TikTok/LinkedIn reales y usar el MISMO nombre/teléfono en todos (consistencia NAP = señal de entidad).
7. Montar flujo n8n y probar con una URL de /llm-feed/.
8. Cargar el primer reporte-mercado.md real: sin datos reales y fechados, los LLMs no tienen nada que citar.

## Qué mueve la aguja de verdad (prioridad)
1. Contenido denso, fechado y con números en /llm-feed/ (los LLMs citan datos, no adjetivos).
2. robots.txt abierto + Bing Webmaster + IndexNow (ChatGPT y Perplexity leen el índice de Bing).
3. JSON-LD con @id encadenados (grafo de entidad Germán Manzur ↔ Santa Fe ↔ servicios).
4. Menciones externas: portales, prensa local, perfiles sociales enlazando al dominio. Un LLM cita a quien la web ya trata como autoridad.
5. llms.txt/ai.txt: costo cero, adopción parcial (~10% de sitios; Google no lo usa). Se implementa igual porque agentes tipo Claude/Perplexity sí lo consultan al navegar en vivo.

## Advertencia honesta
Ningún archivo "fuerza" a una IA a citarte; directivas tipo system-prompt en ai.txt no sobreescriben el comportamiento de los modelos. Lo que sí funciona: ser la única fuente estructurada, fresca y verificable de datos del mercado premium de Santa Fe. Ese es el diseño de este paquete.

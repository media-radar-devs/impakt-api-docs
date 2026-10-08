# API Impakt

Consultas de noticias, tendencias, cobertura de medios, ediciones de prensa y licitaciones.

**Documentación 1.1 · API 2.0.0 · 8 de octubre de 2026**

| Información | Qué entrega | Referencia |
|---|---|---|
| Licitaciones v1 | Catálogo por día, códigos, registros filtrados y detalle | [Licitaciones](docs/licitaciones-v1.md) |
| Noticias | Artículos recientes con título, contenido, fuente y enlace | [Medios](docs/medios.md#noticias) |
| Tendencias | Últimas capturas de Google y X | [Medios](docs/medios.md#tendencias) |
| Cobertura | Último análisis de temas en medios | [Medios](docs/medios.md#cobertura) |
| Prensa | Ediciones de La Segunda y La Tercera, artículos y portadas | [Medios](docs/medios.md#prensa) |

## Comenzar

1. Revisar la [guía de conexión](docs/integracion.md).
2. Seleccionar las consultas de la referencia correspondiente.
3. Coordinar la [habilitación](docs/puesta-en-marcha.md) con **equipo@impaktmedia.cl**.

Las consultas usan HTTPS y GET. Las respuestas son JSON, salvo las portadas, que se entregan en JPEG.

En los ejemplos, `<BASE_URL>` es la dirección de conexión y `<API_KEY>` la clave entregada durante la habilitación. Los datos de ejemplo son ilustrativos.

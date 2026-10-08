# Información de medios

## Consultas disponibles

| GET | Parámetros | Qué entrega |
|---|---|---|
| `/api/noticias` | `hours`: 1–72, predeterminado 14; `limit`: 1–500, predeterminado 200 | Noticias recientes |
| `/api/tendencias` | Ninguno | Últimas capturas de Google y X |
| `/api/cobertura` | Ninguno | Último análisis disponible |
| `/api/lasegunda` | `date=YYYY-MM-DD`, opcional | Edición de La Segunda y artículos |
| `/api/latercera` | `date=YYYY-MM-DD`, opcional | Edición de La Tercera y artículos |
| `/api/portadas/lasegunda` | `date=YYYY-MM-DD`, opcional | Portada JPEG |
| `/api/portadas/latercera` | `date=YYYY-MM-DD`, opcional | Portada JPEG |

## Noticias

```http
GET <BASE_URL>/api/noticias?hours=24&limit=100
Accept: application/json
```

```json
[
  {
    "id": "00000000-0000-4000-8000-000000000001",
    "url_hash": "hash-ilustrativo",
    "title": "Título de noticia de ejemplo",
    "content": "Contenido ilustrativo",
    "url": "https://example.com/noticia",
    "source": "Medio de ejemplo",
    "image_url": null,
    "published_at": "2026-10-08T09:00:00-03:00",
    "scraped_at": "2026-10-08T09:05:00-03:00",
    "raw_source_name": "Medio de ejemplo"
  }
]
```

`hours` define la ventana y `limit` el máximo de elementos. La consulta no tiene offset. Para seguimiento continuo, almacene los registros y deduplique por `id`. Sin coincidencias, devuelve `[]`.

## Tendencias

```http
GET <BASE_URL>/api/tendencias
```

```json
{
  "google_trends": {
    "id": "00000000-0000-4000-8000-000000000002",
    "terms": ["Tema ilustrativo"],
    "raw_terms": ["Tema ilustrativo"],
    "captured_at": "2026-10-08T08:00:00-03:00"
  },
  "x_trends": {
    "id": "00000000-0000-4000-8000-000000000003",
    "data": {"topics": [{"rank": "1", "topic": "Tema ilustrativo"}]},
    "captured_at": "2026-10-08T08:00:00-03:00"
  }
}
```

Cada componente puede ser `null`. `captured_at` indica la fecha de captura de los datos entregados.

## Cobertura

`GET /api/cobertura` devuelve `id`, `topics`, `raw_response`, `headline_count` y `created_at`. Los campos `topics` y `raw_response` contienen el análisis; `created_at` indica cuándo se generó.

Si no hay análisis, devuelve `200` con `{"message":"No analysis available"}`.

## Prensa

```http
GET <BASE_URL>/api/lasegunda?date=2026-10-08
```

| Campos | Contenido |
|---|---|
| `id`, `source`, `edition_date` | Identificador, fuente y día de edición |
| `article_count`, `newspaper_articles` | Cantidad de artículos registrada y artículos disponibles |
| `summary_data` | Resumen |
| `cover_image_path`, `pdf_path` | Referencias de recursos |
| `created_at` | Fecha y hora del registro |

Valide `date` como `YYYY-MM-DD` antes de consultar. Si se omite, se utiliza el día del servidor. Sin edición disponible, devuelve `200` con `{"message":"No edition found"}`.

Para obtener una portada, utilice la ruta de imagen:

```http
GET <BASE_URL>/api/portadas/lasegunda?date=2026-10-08
Accept: image/jpeg
```

Devuelve JPEG con `200` y caché de una hora. Una portada no disponible devuelve `404`; una fuente diferente de `lasegunda` o `latercera` devuelve `422`. Las referencias `cover_image_path` y `pdf_path` no garantizan descarga pública.

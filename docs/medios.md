# Referencia · Información de medios

Estas rutas de lectura responden actualmente sin credencial a nivel HTTP. Coordine el alcance y las condiciones de consumo con Impakt.

## Endpoints y parámetros

| GET | Parámetros | Respuesta |
|---|---|---|
| `/api/noticias` | `hours`: 1–72, predeterminado 14; `limit`: 1–500, predeterminado 200 | Arreglo de noticias recientes |
| `/api/tendencias` | Ninguno | Objeto con `google_trends` y `x_trends` |
| `/api/cobertura` | Ninguno | Último análisis disponible |
| `/api/lasegunda` | `date=YYYY-MM-DD`, opcional | Edición y artículos |
| `/api/latercera` | `date=YYYY-MM-DD`, opcional | Edición y artículos |
| `/api/portadas/lasegunda` | `date=YYYY-MM-DD`, opcional | Imagen JPEG |
| `/api/portadas/latercera` | `date=YYYY-MM-DD`, opcional | Imagen JPEG |

En las consultas de prensa, omitir `date` selecciona el día actual del servidor. Envíe una fecha explícita y valídela antes de la solicitud; las rutas de edición no ofrecen una validación uniforme de fechas inválidas.

## Noticias

```http
GET <BASE_URL>/api/noticias?hours=24&limit=100
Accept: application/json
```

Respuesta ilustrativa con los campos principales:

```json
[
  {
    "id": "00000000-0000-4000-8000-000000000001",
    "url_hash": "hash-ilustrativo",
    "title": "Título de noticia de ejemplo",
    "content": "Contenido ilustrativo de la noticia",
    "url": "https://example.com/noticia",
    "source": "Medio de ejemplo",
    "image_url": null,
    "published_at": "2026-10-08T09:00:00-03:00",
    "scraped_at": "2026-10-08T09:05:00-03:00",
    "raw_source_name": "Medio de ejemplo"
  }
]
```

`hours` define la ventana reciente y `limit` el máximo de elementos devueltos. Esta ruta no dispone de paginación por offset: no constituye una exportación histórica completa. Mantenga una copia propia y deduplique por `id` si necesita seguimiento continuo, con la frecuencia acordada.

El contrato técnico también expone el parámetro opcional `user_id`, que cambia la consulta a coincidencias pendientes de correo de un usuario. Para este recorrido de integración general, omítalo; cualquier uso personalizado requiere definir el alcance con Impakt.

## Tendencias

Devuelve los últimos registros disponibles:

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
    "data": {
      "topics": [
        {"rank": "1", "topic": "Tema ilustrativo"}
      ]
    },
    "captured_at": "2026-10-08T08:00:00-03:00"
  }
}
```

Cada componente puede ser `null` si no hay un registro disponible. Use `captured_at` para mostrar cuándo se obtuvo la información; no suponga que cada consulta genera una nueva captura.

## Cobertura

El análisis incluye `id`, `topics`, `raw_response`, `headline_count` y `created_at`. `topics` y `raw_response` contienen el resultado del análisis y deben tratarse de forma tolerante a su contenido.

Si no hay análisis disponible:

```json
{"message": "No analysis available"}
```

La respuesta describe el último análisis almacenado, que puede corresponder a un día anterior al de la consulta.

## Ediciones de prensa

La respuesta incluye:

| Campo | Contenido |
|---|---|
| `id`, `source`, `edition_date` | Identificador, fuente y fecha de edición |
| `article_count` | Cantidad de artículos registrada para la edición |
| `newspaper_articles` | Artículos disponibles |
| `summary_data` | Información de resumen |
| `cover_image_path`, `pdf_path` | Referencias a recursos almacenados |
| `created_at` | Fecha y hora de creación del registro |

Las referencias de almacenamiento no garantizan descarga pública ni otorgan acceso a infraestructura. Para la portada, utilice el endpoint de imagen documentado.

Cuando no hay edición para la fecha solicitada, devuelve `200`:

```json
{"message": "No edition found"}
```

El consumidor debe distinguir ese objeto de una edición con artículos.

## Portadas

```http
GET <BASE_URL>/api/portadas/lasegunda?date=2026-10-08
Accept: image/jpeg
```

Una portada disponible devuelve `200`, `Content-Type: image/jpeg` y `Cache-Control: public, max-age=3600`. Si no está disponible, devuelve `404`. Una fuente distinta de `lasegunda` o `latercera` devuelve `422`.

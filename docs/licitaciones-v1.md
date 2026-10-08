# Licitaciones v1

Todas las consultas v1 requieren el header `x-api-key: <API_KEY>`.

## Consultas disponibles

| GET | Qué entrega |
|---|---|
| `/api/v1/licitaciones/catalog` | Días disponibles, cantidad de detalles y estado |
| `/api/v1/licitaciones/index?dia=YYYY-MM-DD` | Códigos de un día |
| `/api/v1/licitaciones` | Registros de un día, con filtros y paginación |
| `/api/v1/licitaciones/{external_id}` | Detalle completo de un código |

`dia` es el día del listado de Mercado Público. En `index` es obligatorio. Codifique `external_id` como segmento de URL para solicitar un detalle.

## Catálogo

```http
GET <BASE_URL>/api/v1/licitaciones/catalog
x-api-key: <API_KEY>
```

```json
{
  "dias": [
    {
      "fecha": "2026-10-08",
      "count": 2,
      "checksum": "a1b2c3d4e5",
      "last_modified": "2026-10-08T12:00:00-03:00",
      "status": "complete"
    }
  ]
}
```

Los días aparecen del más reciente al más antiguo. `count` indica detalles completados. `status` puede ser `pending`, `complete`, `expired` o `failed`. Un día pendiente puede incorporar más registros; revíselo en consultas posteriores. `checksum` y `last_modified` ayudan a identificar cambios del día.

## Listado: parámetros

| Parámetro | Predeterminado | Uso |
|---|---|---|
| `dia` | Día del servidor | `YYYY-MM-DD`; se recomienda enviarlo |
| `modo` | `completo` | `completo` o `resumen`; `light` y `ligero` son alias de resumen |
| `limit` | Todos los coincidentes | De 1 a 5000 |
| `offset` | `0` | Entero mayor o igual a 0 |
| `region` | Sin filtro | Nombre de región, sin distinguir mayúsculas |
| `tipo` | Sin filtro | Tipo, por ejemplo `LP`, sin distinguir mayúsculas |
| `fecha_inicio_desde` / `fecha_inicio_hasta` | Sin filtro | Límites inclusivos sobre `started_at` |
| `fecha_fin_desde` / `fecha_fin_hasta` | Sin filtro | Límites inclusivos sobre `finalized_at` |

Los filtros se combinan con AND. Las fechas de inicio y fin aceptan fecha o fecha/hora ISO 8601. Envíe región y tipo sin comodines. Los resultados se ordenan por `published_at` descendente.

```http
GET <BASE_URL>/api/v1/licitaciones?dia=2026-10-08&modo=resumen&tipo=LP&limit=100&offset=0
x-api-key: <API_KEY>
```

```json
{
  "dia": "2026-10-08",
  "modo": "resumen",
  "count": 1,
  "licitaciones": [
    {
      "CodigoExterno": "EJEMPLO-01-LP26",
      "Nombre": "Adquisición de insumos",
      "Descripcion": "Descripción ilustrativa",
      "CodigoEstado": "5",
      "Tipo": "LP",
      "Moneda": "CLP",
      "MontoEstimado": 15000000,
      "RegionUnidad": "Región Metropolitana de Santiago",
      "Comprador": {
        "NombreOrganismo": "Organismo de ejemplo",
        "RegionUnidad": "Región Metropolitana de Santiago"
      },
      "DiasCierreLicitacion": 8,
      "VisibilidadMonto": "1",
      "Categorias": ["43211503"],
      "Fechas": {
        "FechaPublicacion": "2026-10-08T09:00:00-03:00",
        "FechaInicio": "2026-10-08T09:00:00-03:00",
        "FechaFinal": "2026-10-16T15:00:00-03:00",
        "FechaCierre": "2026-10-16T15:00:00-03:00"
      }
    }
  ]
}
```

## Completo y detalle

`modo=completo` mantiene la envoltura del listado y entrega registros con los siguientes campos. La consulta por código devuelve el objeto directamente.

| Campos | Contenido |
|---|---|
| `id`, `external_id` | Identificadores del registro y de la fuente |
| `name`, `description`, `organization`, `url` | Nombre, descripción, organismo y enlace |
| `amount`, `currency`, `amount_visibility` | Monto, moneda y visibilidad |
| `status`, `tender_type`, `region`, `categories` | Estado y clasificación |
| `publish_date`, `close_date` | Fechas de publicación y cierre |
| `published_at`, `started_at`, `finalized_at`, `closes_at` | Fechas y horas |
| `days_to_close` | Días al cierre registrados por la fuente |
| `raw_data` | Detalle de origen, incluidos comprador e ítems disponibles |
| `content_hash`, `created_at`, `updated_at` | Metadatos del registro |

Los códigos de estado, tipo y categorías provienen de Mercado Público. Los campos opcionales pueden ser `null`; `categories` puede estar vacío. `raw_data` puede incluir campos adicionales.

## Índice y paginación

El índice devuelve `{"dia":"2026-10-08","count":1,"codigos":["EJEMPLO-01-LP26"]}`.

En el listado, `count` es la cantidad de la página, después de filtros. Para recorrer el día, use `limit=100`, comience en `offset=0` e incremente el offset en la cantidad recibida. Termine al recibir menos de 100 registros.

Guarde por código y vuelva a consultar los días pendientes: sus resultados pueden cambiar entre páginas. Un día sin coincidencias devuelve `200` con `count: 0` y un arreglo vacío.

## Errores específicos

Modo o filtro de fecha/hora inválido: `400`. Clave ausente o incorrecta: `403`. Código inexistente: `404`. Día, límite u offset inválidos: `422`. Consulta no habilitada: `503`.

## Licitaciones abiertas

`GET /api/licitaciones` devuelve un arreglo de registros completos cuya fecha de cierre es igual o posterior al día del servidor. La selección es por fecha de cierre. No requiere clave ni acepta filtros o paginación. Para consultas históricas o por lotes, use v1 con límites explícitos.

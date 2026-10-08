# Referencia · Licitaciones v1

Todos los endpoints v1 requieren `x-api-key: <API_KEY>`. Las solicitudes y respuestas de ejemplo son ficticias.

## Flujo de consulta

1. Consultar el catálogo para descubrir los días disponibles y su estado.
2. Consultar el índice si se necesitan solo códigos o comparar registros locales.
3. Obtener el listado del día en resumen o completo, utilizando paginación.
4. Consultar un detalle cuando se necesite actualizar un código concreto.
5. Volver a consultar los días pendientes y actualizar la copia local por código.

`dia` corresponde al día del listado de Mercado Público; no debe interpretarse como un filtro por la fecha de inicio o cierre de una licitación.

## Endpoints

| Método y ruta | Propósito | Respuesta |
|---|---|---|
| GET `/api/v1/licitaciones/catalog` | Descubrir días disponibles | `{"dias":[...]}` |
| GET `/api/v1/licitaciones/index?dia=YYYY-MM-DD` | Obtener códigos de un día | `{"dia":"...","count":2,"codigos":[...]}` |
| GET `/api/v1/licitaciones` | Consultar registros de un día | `{"dia":"...","modo":"...","count":2,"licitaciones":[...]}` |
| GET `/api/v1/licitaciones/{external_id}` | Obtener un registro completo | Objeto normalizado, sin envoltura |

`catalog` no requiere parámetros. En `index`, `dia` es obligatorio. Codifique el identificador como segmento de URL al solicitar un detalle.

## Catálogo y estado de los días

Cada elemento de `dias` incluye:

| Campo | Significado |
|---|---|
| `fecha` | Día del listado |
| `count` | Cantidad de detalles completados |
| `checksum` | Identificador corto del checksum del día, de hasta 10 caracteres |
| `last_modified` | Fecha y hora de actualización del registro del día |
| `status` | `pending`, `complete`, `expired` o `failed` |

Los días se devuelven del más reciente al más antiguo. `count` indica los detalles completados, no necesariamente todos los anunciados originalmente por la fuente. Un día `pending` puede incorporar más detalles en consultas posteriores. `complete` describe el estado de procesamiento del listado; una licitación puede cambiar posteriormente en su fuente.

Use `checksum` y `last_modified` como señales para revisar el día; no como una garantía de que el dato externo nunca volverá a cambiar. Coordine con Impakt la política de actualización que requiera su caso de uso.

## Parámetros del listado diario

| Parámetro | Predeterminado | Regla |
|---|---|---|
| `dia` | Día actual del servidor | Fecha `YYYY-MM-DD`; se recomienda enviarla explícitamente |
| `modo` | `completo` | `completo` o `resumen`; `light` y `ligero` son alias de resumen |
| `limit` | Todos los coincidentes | De 1 a 5000; usar un valor explícito |
| `offset` | `0` | Entero mayor o igual a 0 |
| `region` | Sin filtro | Coincidencia sin distinguir mayúsculas |
| `tipo` | Sin filtro | Tipo de licitación, por ejemplo `LP` |
| `fecha_inicio_desde` | Sin filtro | `started_at >=` fecha/hora |
| `fecha_inicio_hasta` | Sin filtro | `started_at <=` fecha/hora |
| `fecha_fin_desde` | Sin filtro | `finalized_at >=` fecha/hora |
| `fecha_fin_hasta` | Sin filtro | `finalized_at <=` fecha/hora |

Los filtros se combinan con AND. Las fechas aceptan fecha o fecha y hora ISO 8601. Envíe nombres de región y tipo completos, sin comodines `*` o `%`. Los resultados se ordenan por `published_at` descendente.

### Solicitud con filtros

```http
GET <BASE_URL>/api/v1/licitaciones?dia=2026-10-08&modo=resumen&tipo=LP&limit=100&offset=0
Accept: application/json
x-api-key: <API_KEY>
```

### Respuesta en resumen

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

Los códigos de estado, tipo, categorías y visibilidad provienen de la fuente. Confirme su interpretación para el uso de negocio; no deduzca el significado de un código a partir de un solo ejemplo.

### Respuesta completa y detalle

`modo=completo` devuelve objetos normalizados dentro de `licitaciones`. El endpoint de detalle devuelve uno de esos objetos directamente.

| Campos | Contenido |
|---|---|
| `id`, `external_id` | Identificadores interno y de la fuente |
| `name`, `description`, `organization`, `url` | Descripción, organismo y enlace |
| `amount`, `currency`, `amount_visibility` | Monto, moneda y visibilidad |
| `status`, `tender_type`, `region`, `categories` | Clasificación y ubicación |
| `publish_date`, `close_date` | Fechas de publicación y cierre |
| `published_at`, `started_at`, `finalized_at`, `closes_at` | Fechas y horas normalizadas |
| `days_to_close` | Valor de días al cierre registrado por la fuente; puede variar o faltar |
| `raw_data` | Detalle de origen, incluidos comprador e ítems cuando están disponibles |
| `content_hash`, `created_at`, `updated_at` | Metadatos del registro |

Los campos opcionales pueden ser `null` y `categories` puede estar vacío. `raw_data` conserva la estructura de la fuente y puede contener campos adicionales; no debe suponerse una forma idéntica para todas las licitaciones.

## Paginación

`count` es la cantidad recibida en esa respuesta, después de filtros y paginación; no es el total de coincidencias del día.

Con `limit=100`:

1. Solicitar `offset=0`.
2. Procesar y guardar los resultados por identificador.
3. Incrementar `offset` en la cantidad recibida.
4. Terminar cuando la cantidad recibida sea menor que 100.

Una consulta sin coincidencias devuelve `200`, `count: 0` y `licitaciones: []`. El índice de un día sin registros devuelve `codigos: []`.

La paginación no entrega un token de instantánea. En días en actualización puede cambiar el contenido entre páginas: use operaciones idempotentes y vuelva a revisar los días pendientes. Para lotes históricos, comience por días completos.

## Errores

| HTTP | Caso |
|---|---|
| `400` | Modo desconocido o filtro de fecha/hora inválido |
| `403` | Clave ausente o incorrecta |
| `404` | Código inexistente |
| `422` | Día inválido, límite fuera de rango u offset negativo |
| `503` | Credencial de consumo no configurada en el servicio |

Ejemplo:

```json
{"detail": "Invalid API key"}
```

## Consulta pública de abiertas

`GET /api/licitaciones` devuelve un arreglo completo de registros con fecha de cierre igual o posterior al día actual del servidor. No acepta filtros ni paginación y actualmente no requiere clave. La selección se realiza por fecha de cierre; no equivale a filtrar por un código de estado específico.

La respuesta puede ser considerablemente mayor que una página de v1. Para una integración por lotes o histórica, use v1 con límites explícitos.

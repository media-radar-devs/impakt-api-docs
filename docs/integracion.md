# Conexión y uso

Realice las consultas desde el servidor de su aplicación. Guarde las claves en configuración segura y evite exponerlas en el navegador o en registros de ejecución. Para consumo directo desde un navegador, coordine los dominios permitidos con Impakt.

## Autenticación

| Consultas | Autenticación HTTP |
|---|---|
| `/api/v1/licitaciones` y sus rutas de catálogo, índice y detalle | Header `x-api-key` |
| Medios y `/api/licitaciones` | Sin clave |

## Primera consulta

```http
GET <BASE_URL>/api/v1/licitaciones/catalog
Accept: application/json
x-api-key: <API_KEY>
```

El catálogo devuelve los días disponibles. Use una fecha del catálogo para consultar el [listado de licitaciones](licitaciones-v1.md).

## Reglas de consumo

- Envíe días como `YYYY-MM-DD` y filtros de fecha/hora como ISO 8601, con zona horaria cuando incluya horas.
- Envíe una fecha explícita para procesos diarios; los valores predeterminados utilizan la fecha del servidor.
- En licitaciones, use `limit` y `offset` y actualice registros por `external_id` o `CodigoExterno`.
- En noticias, deduplique por `id`.
- Admita campos opcionales con `null` y arreglos vacíos.
- Para errores de red o `5xx`, aplique reintentos limitados con espera creciente. Ante `4xx`, revise la solicitud antes de repetirla.

## Respuestas y errores

| HTTP | Interpretación |
|---|---|
| `200` | Consulta correcta; puede devolver resultados vacíos |
| `400` | Modo o filtro inválido |
| `403` | Clave de licitaciones ausente o incorrecta |
| `404` | Detalle o portada no disponible |
| `422` | Parámetro inválido o fuera de rango |
| `503` | Consulta temporalmente no habilitada |
| Otros `5xx` | Error del servicio |

Los errores pueden incluir `detail` como texto o arreglo. Admita también respuestas de error en texto. Para soporte, conserve la ruta, el código HTTP y la hora de la consulta.

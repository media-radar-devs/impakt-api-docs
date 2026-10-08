# Guía de integración

## Arquitectura recomendada

```text
API de Impakt → servidor de su empresa → almacenamiento / panel / procesos internos
```

Realice las consultas desde su backend. Mantenga las credenciales en un gestor de secretos o en la configuración segura del servidor. Evite incluirlas en código fuente, repositorios, registros de ejecución o aplicaciones de navegador.

La configuración actual de CORS permite los dominios de Impakt. Una integración directa desde el navegador de su empresa debe coordinarse antes de implementarse; las consultas desde su backend no dependen de CORS.

## Convenciones

| Elemento | Convención |
|---|---|
| Transporte | HTTPS |
| Método de las consultas de esta guía | GET |
| Respuestas estructuradas | JSON |
| Portadas | JPEG |
| Fechas de consulta | `YYYY-MM-DD` |
| Fechas y horas de filtros de licitaciones | ISO 8601; incluir zona horaria al enviar horas |
| Credencial de licitaciones v1 | Header `x-api-key` |
| Campos opcionales | Pueden ser `null`; algunos arreglos pueden estar vacíos |

Envíe fechas explícitas cuando su proceso dependa de un día concreto. No suponga que el día predeterminado del servidor coincide siempre con el día local de su empresa.

## Primera solicitud, después de la habilitación

```http
GET <BASE_URL>/api/v1/licitaciones/catalog
Accept: application/json
x-api-key: <API_KEY>
```

Respuesta ilustrativa:

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

Los endpoints de medios y `/api/licitaciones` responden actualmente sin credencial a nivel HTTP. Licitaciones v1 requiere clave. La selección de productos y las condiciones de uso se coordinan con Impakt; la ausencia de autenticación en una ruta no sustituye ese acuerdo.

## Diseño del consumidor

- Comience con páginas pequeñas, por ejemplo `limit=100`, para licitaciones v1.
- Guarde licitaciones mediante actualización o inserción por `external_id`; en resumen el mismo identificador se llama `CodigoExterno`.
- Guarde noticias por `id` y tolere registros ya recibidos en consultas sucesivas.
- Distinga resultados vacíos de errores HTTP.
- Configure tiempos de espera según el tamaño de respuesta y las mediciones de su integración.
- Ante errores transitorios de red o `5xx`, use reintentos limitados con espera creciente.
- Corrija los parámetros o credenciales antes de repetir solicitudes con `4xx`.
- Conserve código HTTP, ruta y hora para soporte; oculte claves y datos sensibles en los registros.

## Errores y resultados vacíos

| Resultado | Acción |
|---|---|
| `200` con datos | Procesar y almacenar |
| `200` con arreglo vacío o `count: 0` | Registrar que la consulta no encontró coincidencias |
| `200` con `{"message":"No edition found"}` | La edición solicitada no está disponible |
| `400` | Revisar modo o filtros |
| `401` | Una ruta de usuario requiere autenticación; esas rutas quedan fuera de esta guía |
| `403` en licitaciones v1 | Revisar la clave con el responsable de habilitación |
| `404` | El detalle o la portada solicitados no están disponibles |
| `422` | Corregir el parámetro indicado en `detail` |
| `5xx` | Registrar el incidente, aplicar reintentos limitados y escalar si persiste |

Los errores de validación pueden devolver `detail` como arreglo; otros errores lo devuelven como texto. No todos los errores del servidor son JSON: el consumidor debe admitir respuestas de texto.

## Desarrollo antes de recibir acceso

Use las respuestas ilustrativas de esta documentación como datos simulados. Puede preparar el cliente HTTP, validaciones, persistencia, deduplicación y pantallas. Reserve la prueba de conectividad y validación con datos reales para la habilitación.

La frecuencia de consulta, los volúmenes y los compromisos de servicio se confirman para cada integración. Esta guía no establece un SLA ni cuotas contractuales.

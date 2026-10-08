# Preparación y puesta en marcha

## Información que su empresa debe presentar

Puede enviar este formulario completo a **equipo@impaktmedia.cl**:

| Dato | Respuesta de su empresa |
|---|---|
| Empresa y responsable de negocio | |
| Responsable técnico | |
| Objetivo de la integración | |
| Productos seleccionados | |
| Sistema de destino | |
| Fuentes, regiones, tipos y fechas que necesita | |
| Frecuencia y volumen previstos | |
| Uso interno o distribución a terceros | |
| Almacenamiento y retención previstos | |
| Requisitos operativos o de disponibilidad | |
| Fecha objetivo de puesta en marcha | |

## Preparación del equipo técnico

Antes de recibir credenciales, el equipo puede:

- Diseñar el consumo desde su backend.
- Crear modelos de almacenamiento con identificadores para deduplicación.
- Implementar el cliente HTTP y probarlo con respuestas simuladas.
- Preparar paginación, manejo de resultados vacíos y errores.
- Configurar un mecanismo seguro para incorporar las credenciales.
- Preparar registros operativos que oculten información sensible.

## Habilitación coordinada

Impakt y su empresa confirman:

1. Productos y usos habilitados.
2. Dirección de conexión y requisitos de autenticación.
3. Volumen, frecuencia y condiciones operativas.
4. Canal seguro de entrega de credenciales cuando corresponda.
5. Responsables y canal de soporte.
6. Condiciones de uso, retención y redistribución de datos.

Este repositorio no contiene credenciales. Su entrega al cliente se realiza una vez definido el alcance y los destinatarios autorizados.

## Validación antes de operar

| Comprobación | Resultado esperado |
|---|---|
| Conectividad | Se reciben respuestas HTTP y el formato esperado |
| Credencial de licitaciones v1 | El catálogo responde con acceso habilitado |
| Parámetros | Filtros y fechas corresponden al caso de uso |
| Paginación | Se recorren y guardan registros sin duplicar |
| Resultados vacíos | Se distinguen de errores y de registros disponibles |
| Actualización | Se revisan los estados y fechas de captura pertinentes |
| Errores | El consumidor procesa códigos HTTP y registra incidentes |
| Seguridad | Las credenciales permanecen en el servidor y no aparecen en logs |

## Versiones de esta documentación

Esta edición documenta el comportamiento de lectura revisado en producción el **8 de octubre de 2026**, sobre Varys **2.0.0**. El segmento `v1` identifica el contrato de licitaciones; no es la versión global del backend.

Se actualizará la documentación al concretar el alcance del cliente y ante cambios relevantes del contrato. La validación de inicio de sesión, operaciones personales, tareas administrativas y procesamiento interno queda fuera de esta entrega.

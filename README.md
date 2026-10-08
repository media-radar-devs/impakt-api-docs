# Impakt · Documentación de integración de API

Impakt permite incorporar noticias, tendencias, análisis de cobertura, ediciones de prensa y licitaciones en herramientas de trabajo de su empresa. Esta documentación acompaña la evaluación y preparación de una integración.

**Edición:** 1.0 · **Referencia técnica:** API Varys 2.0.0 · **Actualización:** 8 de octubre de 2026.

## Dos recorridos para avanzar

| Destinatario | Documento | Resultado |
|---|---|---|
| Negocio, gerencia y responsables del proyecto | [Presentación para su empresa](docs/presentacion-empresa.md) | Entender el servicio y presentar una propuesta interna |
| Tecnología e integraciones | [Guía de integración](docs/integracion.md) | Preparar la arquitectura y las solicitudes |
| Equipo técnico de licitaciones | [Referencia de licitaciones v1](docs/licitaciones-v1.md) | Definir consultas, sincronización y almacenamiento |
| Equipo técnico de información de medios | [Referencia de medios](docs/medios.md) | Integrar noticias, tendencias, cobertura y prensa |
| Responsable de habilitación | [Preparación y puesta en marcha](docs/puesta-en-marcha.md) | Coordinar alcance, accesos y validación |

## Alcance de esta entrega

Se documentan consultas de lectura comprobadas en producción. Los ejemplos contienen datos ficticios y marcadores de credenciales; permiten diseñar la integración sin entregar accesos.

La habilitación del cliente se coordina con Impakt una vez acordados los productos, el volumen de consulta y los responsables. Tener esta documentación no constituye la asignación de una clave ni autoriza a distribuir credenciales o datos.

**Contacto:** equipo@impaktmedia.cl.

## Cómo utilizar los ejemplos

En las solicitudes, `<BASE_URL>` representa la dirección confirmada durante la habilitación y `<API_KEY>` la credencial que Impakt entregará por un canal seguro. Son marcadores y deben reemplazarse antes de ejecutar una solicitud real.

Para pruebas de desarrollo previas a la habilitación se pueden usar los JSON de esta guía como respuestas simuladas. Las fechas, códigos, cantidades y organizaciones mostrados son ilustrativos.

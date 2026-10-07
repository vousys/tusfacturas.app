<p align="center">
  <a href="https://www.tusfacturas.app/">
    <img src="https://cdn.tusfacturas.app/web/images/ig/queteresuelvelaapiarcaafip-tusfacturasapp.webp" alt="TusFacturasAPP - API y SDK de Factura Electrónica AFIP/ARCA para Argentina" width="640">
  </a>
</p>

<p align="center">
  <a href="https://php.net/"><img src="https://img.shields.io/badge/php-%3E%3D%207.2-8892BF.svg" alt="PHP >= 7.2"></a>
  <a href="https://github.com/vousys/tusfacturas/releases/"><img src="https://img.shields.io/badge/version-1.0-brightgreen" alt="Release"></a>
  <a href="https://github.com/vousys/tusfacturas/graphs/commit-activity"><img src="https://img.shields.io/badge/Maintained%3F-yes-green.svg" alt="Mantenido"></a>
  <a href="https://developers.tusfacturas.app/"><img src="https://img.shields.io/badge/docs-developers.tusfacturas.app-blue" alt="Documentación"></a>
  <a href="https://developers.tusfacturas.app/changelog"><img src="https://img.shields.io/badge/API-v2-orange" alt="API v2"></a>
</p>

# API y SDK de Factura Electrónica AFIP/ARCA para Argentina

**SDK y API REST para emitir facturas electrónicas válidas ante AFIP/ARCA desde cualquier lenguaje de programación.** Integrá la facturación electrónica de Argentina en tu ecommerce, ERP, CRM o SaaS en minutos, sin pelearte con los webservices SOAP de AFIP, los certificados ni el WSFEv1.

Desarrollado por [TusFacturasAPP](https://www.tusfacturas.app/) — en producción desde 2015, con el respaldo de un estudio impositivo que mantiene la plataforma al día con la normativa argentina.

📚 **Documentación completa:** [developers.tusfacturas.app](https://developers.tusfacturas.app/)
🧪 **Probar gratis:** [Crear cuenta de prueba](https://www.tusfacturas.app/probar-gratis-api-factura-electronica-tusfacturasapp)

---

## ¿Por qué usar esta API en lugar de integrar con ARCA directo?

Integrar los webservices de AFIP/ARCA por tu cuenta implica manejar certificados X.509, tickets de acceso WSAA, SOAP, homologación, numeración de comprobantes, reintentos ante caídas del organismo y cambios normativos constantes.

Esta API Rest para ARCA te resuelve todo eso detrás de un único `POST` con JSON:

| Sin API | Con TusFacturasAPP |
| --- | --- |
| SOAP + WSAA + certificados | JSON sobre HTTPS |
| Gestión manual de CAE y numeración | CAE y numeración automáticos |
| Generás el PDF vos | PDF A4 y ticket 80mm generados y enviados por mail |
| Seguís la normativa a mano | Actualizaciones normativas incluidas |
| Reintentos artesanales ante caídas de AFIP | Cola de procesamiento asincrónica con webhooks |

---

## Integración

No necesitás SDK. La API es REST pura con entrada y salida en JSON, así que funciona con `requests`, `axios`, `HttpClient`, `RestSharp` o lo que uses. Ya está en producción con PHP, Python, Node.js, Ruby, .NET y hasta Visual Basic 6.0, sobre AWS, Google Cloud, servidores dedicados y hostings compartidos.

> **¿Usás PHP?** Hay un SDK listo para copiar a tu proyecto en [vousys/tusfacturas](https://github.com/vousys/tusfacturas) — sin dependencias externas, dos archivos.

**Especificaciones técnicas**

| | |
| --- | --- |
| Versión | API v2 |
| Tipo | REST |
| Método | `POST` |
| Formato | JSON |
| Charset | UTF-8 |
| Seguridad | TLS 1.2+ |

---

## Quickstart: emitir una factura en ARCA en 1 request

Endpoint de [facturación instantánea e individual](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/api-factura-electronica-afip-facturacion-nuevo-comprobante):

```bash
curl -X POST https://www.tusfacturas.app/app/api/v2/facturacion/nuevo \
  -H "Content-Type: application/json" \
  -d '{
    "usertoken": "TU_USERTOKEN",
    "apikey":    "TU_APIKEY",
    "apitoken":  "TU_APITOKEN",
    "cliente":     { "...": "estructura del bloque cliente" },
    "comprobante": { "...": "estructura del bloque comprobante" }
  }'
```

> La estructura completa de los bloques `cliente` y `comprobante` está detallada en la [Referencia API AFIP/ARCA](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/referencia-api-afip-arca), con ejemplos listos para copiar por cada tipo de comprobante.

**Respuesta exitosa**

```json
{
  "error": "N",
  "errores": [""],
  "rta": "El comprobante FACTURA B 0002-00000006 se ha guardado correctamente",
  "cae": "65301278726386",
  "vencimiento_cae": "07/08/2026",
  "comprobante_nro": "0000123",
  "comprobante_tipo": "FACTURA B",
  "comprobante_pdf_url": "https://...",
  "comprobante_ticket_url": "https://...",
  "afip_qr": "https://www.afip.gob.ar/fe/qr/?p=...",
  "afip_codigo_barras": "12121212121006000300000000000000201811052",
  "envio_x_mail": "S"
}
```

**Respuesta con error**

Cuando algo falla, `error` viene en `"S"` y `errores` trae el detalle legible, más `error_details` con códigos para que ramifiques por caso:

```json
{
  "error": "S",
  "errores": ["Para la condicion de IVA seleccionada no se permite realizar comprobantes de tipo B."],
  "error_details": [
    { "code": "TFC-8004", "text": "Para la condicion de IVA seleccionada no se permite realizar comprobantes de tipo B." }
  ]
}
```

⚠️ **Regla de reintento:** si un comprobante devuelve error por una intermitencia de ARCA, reprocesalo **en el mismo orden cronológico** en que lo emitiste originalmente. La numeración de AFIP es secuencial y no perdona saltos.

📖 [Guía completa: ¿Cómo empiezo?](https://developers.tusfacturas.app/como-empiezo) · [Autenticación](https://developers.tusfacturas.app/como-empiezo/api-factura-electronica-afip-autenticacion) · [Cómo paso a producción](https://developers.tusfacturas.app/como-paso-a-produccion)

---

## Modalidades de emisión

| Modalidad | Cuándo usarla | Documentación |
| --- | --- | --- |
| **Instantánea individual** | Punto de venta, ecommerce, cualquier flujo que necesite el CAE en el momento | [Ver docs](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/api-factura-electronica-afip-facturacion-nuevo-comprobante) |
| **Asincrónica individual** ⭐ | Recomendada. Encola el comprobante y te notifica por webhook. Inmune a caídas de AFIP | [Ver docs](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/api-factura-electronica-afip-facturacion-nuevo-comprobante-1) |
| **Instantánea por lotes** | Facturación masiva con respuesta inmediata | [Ver docs](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/api-factura-electronica-afip-api-facturacion-por-lotes) |
| **Asincrónica por lotes** | Cierres masivos, suscripciones, facturación recurrente | [Ver docs](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/facturacion-asincronica-por-lotes-encolada) |

¿Ya integraste el modo instantáneo? Mirá la [guía de migración a facturación asincrónica](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/guia-de-migracion-a-facturacion-asincronico-encolada) para ganar estabilidad sin reescribir todo.

---

## Tipos de comprobante soportados

Ejemplos de request en JSON, listos para copiar, por cada tipo de comprobante AFIP/ARCA:

| Comprobante | Para quién | Ejemplo |
| --- | --- | --- |
| Factura A | Responsable Inscripto → RI / Monotributo | [JSON](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-factura-a) |
| Factura B | Responsable Inscripto → CF / EXENTO | [JSON](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-factura-b) |
| Factura C | Monotributistas y exentos | [JSON](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-factura-c) |
| Factura E | Exportación de bienes y servicios | [JSON](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-factura-e) |
| Factura M | Contribuyentes con régimen M | [JSON](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-factura-m) |
| Factura MiPyme A / B (FCE) | Factura de Crédito Electrónica | [A](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-factura-mypyme-a) · [B](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-factura-mypyme-b) |
| Notas de crédito A / B / C / E | Devoluciones y anulaciones | [A](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-nota-de-credito-a) · [B](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-nota-de-credito-b) · [C](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-nota-de-credito-c) · [E](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-nota-de-credito-e) |
| Notas de débito A / B / C / E | Ajustes e intereses | [A](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-nota-de-debito-a) · [B](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-nota-de-debito-b) · [C](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-nota-de-debito-c) · [E](https://developers.tusfacturas.app/web-services-afip-api-arca/nota-de-debito-e) |
| Recibo C (cod. 11) | Monotributistas | [JSON](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-recibo-c) |
| Liquidaciones A / B | Régimen de liquidación | [A](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-arca-liquidaciones-a-63) · [B](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-arca-liquidaciones-b-64) |
| Remito con CAI | Papel pre-impreso | [JSON](https://developers.tusfacturas.app/web-services-afip-api-arca/remitos-cai-noelectronicos) |

**Casos especiales:** [Factura A con RG5329](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-factura-a-rg5329) · [Factura A en dólares](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-arca-factura-a-dolares) · [Factura con bonificaciones](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-arca-factura-a-con-bonificacion) · [Factura B sin datos del comprador](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-factura-b-sin-especificar-comprador) · [Factura B a cliente del exterior](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-factura-b-cliente-exterior)

¿No sabés qué comprobante te corresponde? [Tabla según condición de IVA](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/que-tipos-de-comprobante-debo-puedo-emitir) · [¿MiPyme o factura común?](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/api-factura-electronica-afip-or-como-se-si-emitir-una-factura-mipyme-o-una-comun)

---

## Más allá de facturar

La API no es solo emisión de comprobantes. Estos endpoints suelen ahorrar integraciones enteras:

### Consultas a servicios oficiales de AFIP/ARCA

| Endpoint | Qué resuelve |
| --- | --- |
| [Consultar CUIT](https://developers.tusfacturas.app/consultas-varias-a-servicios-afip-arca/api-factura-electronica-afip-clientes-consultar-cuit-en-constancia-de-inscripcion) | Datos completos de la constancia de inscripción, en JSON. Autocompletá el alta de clientes |
| [Cotización de monedas](https://developers.tusfacturas.app/consultas-varias-a-servicios-afip-arca/cotizacion-monedas-afip) | Cotización oficial del dólar y otras monedas según ARCA |
| [Base APOC](https://developers.tusfacturas.app/consultas-varias-a-servicios-afip-arca/api-factura-apocrifa-base-apoc) | Verificá si un proveedor está en la base de facturas apócrifas |
| [Padrón ARBA](https://developers.tusfacturas.app/consultas-a-padrones-oficiales/api-factura-electronica-afip-consulta-de-alicuotas-en-padron-arba-sujetos-recaudacion) | Alícuotas de percepción/retención de Ingresos Brutos PBA |
| [Padrón AGIP](https://developers.tusfacturas.app/consultas-a-padrones-oficiales/api-factura-electronica-afip-consulta-de-alicuotas-en-padron-agip.) | Alícuotas de Regímenes Generales CABA |
| [Estado de servicios AFIP](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-estado-de-los-servicios-afip) | Chequeá si AFIP está caído antes de emitir |
| [Tope para consumidor final](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-or-consulta-de-tope-para-ventas-a-consumidor-final) | El monto vigente sin pedir datos del comprador |

### Gestión y administración

- **[Consultas de ventas](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/consulta-avanzada)** — por [external reference](https://developers.tusfacturas.app/web-services-afip-api-arca/consulta-avanzada-por-external-reference), [tipo y número](https://developers.tusfacturas.app/web-services-afip-api-arca/consulta-simple-tipo-numero), [fecha](https://developers.tusfacturas.app/web-services-afip-api-arca/consulta-avanzada-por-fecha) o [rango numérico](https://developers.tusfacturas.app/web-services-afip-api-arca/consulta-avanzada-por-numero)
- **[API de Compras](https://developers.tusfacturas.app/api-compras)** — cargá y gestioná comprobantes de proveedores
- **[Recibos de cobro y órdenes de pago](https://developers.tusfacturas.app/recibos-de-cobro-y-ordenes-de-pago)** — [ingresar pagos](https://developers.tusfacturas.app/recibos-de-cobro-y-ordenes-de-pago/api-factura-electronica-afip-or-ingresar-pago), [generar recibos](https://developers.tusfacturas.app/recibos-de-cobro-y-ordenes-de-pago/api-factura-electronica-afip-or-ingresar-pago-1), [órdenes de pago](https://developers.tusfacturas.app/recibos-de-cobro-y-ordenes-de-pago/api-factura-electronica-afip-or-ingresar-pago-2)
- **[Cuentas corrientes](https://developers.tusfacturas.app/cuentas-corrientes-de-clientes/api-factura-electronica-afip-clientes-cuenta-corriente)** — saldos de clientes en tiempo real
- **[Productos y stock](https://developers.tusfacturas.app/productos)** — [administrar](https://developers.tusfacturas.app/productos/administrar-productos), [consultar](https://developers.tusfacturas.app/productos/consultar-productos), [listar](https://developers.tusfacturas.app/productos/listar-productos), [gestión de stock](https://developers.tusfacturas.app/productos/gestion-de-stock)
- **[Reportes](https://developers.tusfacturas.app/reportes)** — [IVA compras-ventas](https://developers.tusfacturas.app/reportes/solicitar-reporte-iva-compras-ventas), [¿quién me debe? saldos](https://developers.tusfacturas.app/reportes/reporte-quien-me-debe-saldos) y [detalle](https://developers.tusfacturas.app/reportes/reporte-quien-me-debe-detalle)
- **[Mi cuenta](https://developers.tusfacturas.app/mi-cuenta)** — [puntos de venta](https://developers.tusfacturas.app/mi-cuenta/agregar-o-modificar-puntos-de-venta), [certificados AFIP](https://developers.tusfacturas.app/mi-cuenta/solicitar-certificado-de-enlace-con-afip), [consumo de requests](https://developers.tusfacturas.app/mi-cuenta/mi-cuenta)

### PDFs y envío al cliente

La API genera el PDF en A4 (con varios estilos para respetar tu marca) o en formato [ticket para impresora térmica de 80mm](https://www.tusfacturas.app/caracteristicas-de-tus-facturas-electronica-impresion-tickets.html), lo envía por mail a tu cliente con un solo request y te permite [regenerarlo](https://developers.tusfacturas.app/web-services-afip-api-arca/regenerar-el-archivo-pdf) o [reenviarlo](https://developers.tusfacturas.app/web-services-afip-api-arca/api-factura-electronica-afip-or-reenviar-comprobante) las veces que necesites.

> 💡 Las URLs de descarga del PDF son temporales. Guardá los archivos de tu lado.

---

## Webhooks

Recibí notificaciones instantáneas de cada evento de facturación en lugar de hacer polling:

📖 [Webhooks (notificaciones)](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/webhooks-notificaciones) · [Configuración del webhook](https://developers.tusfacturas.app/web-services-afip-api-arca/webhook)

---

## Integraciones no-code

¿No querés escribir código? Conectá tu ecommerce, CRM o planilla con [Zapier](https://developers.tusfacturas.app/integraciones-factura-afip-arca-no-code/zapier-factura-electronica-afip-arca-no-code) y automatizá la emisión de comprobantes sin programar.

📖 [Todas las integraciones no-code](https://developers.tusfacturas.app/integraciones-factura-afip-arca-no-code)

---

## Debugging y errores frecuentes

- 🐞 [Cómo debuguear tu integración](https://developers.tusfacturas.app/como-puedo-debugear) — logs detallados de cada request y hook enviado
- 🧯 [Errores comunes de ARCA](https://developers.tusfacturas.app/api-factura-electronica-afip-facturacion-ventas/errores-comunes-de-arca-en-tusfacturasapp) — causas y soluciones rápidas de los códigos que más aparecen
- 🗑️ [Eliminar comprobantes encolados](https://developers.tusfacturas.app/web-services-afip-api-arca/eliminar-comprobantes-encolados) · [Cambiar fecha de encolados](https://developers.tusfacturas.app/web-services-afip-api-arca/cambiar-fecha-a-comprobante-encolado) · [Reenviar encolados con error](https://developers.tusfacturas.app/web-services-afip-api-arca/reenvio-de-comprobantes-encolados-con-error)

**FAQs:** [Generales](https://developers.tusfacturas.app/faqs-or-preguntas-frecuentes) · [Ventas asincrónicas](https://developers.tusfacturas.app/faqs-or-ventas-asincronicas) · [RG5329](https://developers.tusfacturas.app/faqs-or-rg5329)

---

## Tablas de referencia

Países, unidades de medida, CUIT país, Incoterms y demás tablas que AFIP define y necesitás para armar tus requests:

📖 [Parámetros y tablas de referencia](https://developers.tusfacturas.app/parametros/tablas-de-referencia)

---

## Empezar

1. [Creá tu cuenta de prueba gratis](https://www.tusfacturas.app/probar-gratis-api-factura-electronica-tusfacturasapp) (1 mes sin cargo)
2. Configurá tu CUIT y punto de venta siguiendo [¿Cómo empiezo?](https://developers.tusfacturas.app/como-empiezo)
3. Obtené tus credenciales en [Autenticación](https://developers.tusfacturas.app/como-empiezo/api-factura-electronica-afip-autenticacion)
4. Probá en homologación y [pasá a producción](https://developers.tusfacturas.app/como-paso-a-produccion)

---

## Changelog

Los cambios de la API se publican en el [changelog oficial](https://developers.tusfacturas.app/changelog). Las novedades del SDK PHP están en [Releases](https://github.com/vousys/tusfacturas/releases).

---

## Contribuir

Los aportes son bienvenidos. Si encontrás un bug en el SDK, querés portarlo a otro lenguaje o sumar un ejemplo de integración:

1. Abrí un [issue](https://github.com/vousys/tusfacturas/issues) describiendo el caso
2. Forkeá el repo y creá una rama (`git checkout -b feature/mi-mejora`)
3. Mandá un Pull Request

Si integraste la API en un lenguaje o framework que todavía no está acá (Python, Node.js, Laravel, Django, .NET, Go, Ruby on Rails), abrí un issue y lo enlazamos desde este README.

---

## Soporte

- 🌐 [Contacto y chat en vivo](https://www.tusfacturas.app/contacto.html)
- 💬 [Centro de ayuda](https://ayuda.tusfacturas.app) — guías paso a paso y troubleshooting

---

## Sobre TusFacturasAPP

[TusFacturasAPP](https://www.tusfacturas.app/) es un proveedor [SaaS de facturación B2B en Argentina](https://www.tusfacturas.app/saas-facturacion-b2b-argentina.html) que permite a empresas de todos los tamaños emitir comprobantes fiscales válidos ante AFIP/ARCA de forma rápida y segura. Además de la API, incluye [plataforma web de facturación](https://www.tusfacturas.app/caracteristicas-de-tus-facturas-electronica-facturacion.html), bot de WhatsApp, [recordatorios automáticos de pago](https://www.tusfacturas.app/caracteristicas-de-tus-facturas-electronica-recordatorios.html) y gestión de compras, stock y cuentas corrientes.

📌 ¿Querés entender el marco legal? [Qué es la factura electrónica AFIP/ARCA](https://www.tusfacturas.app/factura-electronica-afip.html) · [Normativa: RG 4290](https://www.tusfacturas.app/normativa-afip-factura-electronica.html)

---

<sub>**Keywords:** API AFIP · API ARCA · factura electrónica Argentina · SDK AFIP PHP · webservice AFIP · WSFEv1 · WSFEX · CAE · facturación electrónica AFIP · API factura electrónica · monotributo · factura MiPyme · FCE · RG5329 · software de facturación Argentina</sub>

<sub>Mantenido por [@vousys](https://www.vousys.com) · [www.tusfacturas.app](https://www.tusfacturas.app)</sub>

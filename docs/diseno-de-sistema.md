# Diseño de sistema — landing pública de Malüwe

**Estado:** vigente para la landing; migración a Cloudflare Pages pendiente de configurar.  
**Última actualización:** 2026-08-13

## Propósito y alcance

Esta aplicación presenta Malüwe Beauty mientras se desarrolla la tienda. Es un sitio estático de marca: no procesa pagos, pedidos, clientes, inventario ni credenciales. La futura tienda será un sistema separado y no debe reutilizar este repositorio como núcleo de negocio.

### Historia de usuario

Como visitante, quiero conocer Malüwe y saber que sus productos estarán disponibles próximamente, para reconocer la marca y volver cuando la tienda esté lista.

### Criterios de aceptación permanentes

- La página funciona sin backend ni secretos.
- No se muestran precios, stock, carrito ni promesas de compra si no hay un canal real configurado.
- Las imágenes y textos se sirven desde este repositorio y funcionan en móvil y escritorio.
- Cada modificación queda versionada en GitHub y se puede revertir con un commit.
- La publicación usa HTTPS y el dominio principal será `www.maluwe.com.ar`.

## Arquitectura objetivo

```text
Editora/o
    │ modifica y revisa
    ▼
GitHub: LeoCelador/maluwe-landing
    │ push a main
    ▼
Cloudflare Pages
    │ HTTPS, CDN y despliegue automático
    ▼
www.maluwe.com.ar
    │ redirección canónica
    └── maluwe.com.ar
```

### Componentes y responsabilidades

| Componente | Responsabilidad | No debe hacer |
| --- | --- | --- |
| Repositorio GitHub | Fuente de verdad del código, textos e imágenes de la landing. | Guardar secretos o datos de clientes. |
| Cloudflare Pages | Construir/publicar el sitio estático, HTTPS y CDN. | Acceder al panel, la API o la base operativa. |
| Cloudflare DNS | Resolver el dominio y redirigir el dominio raíz a `www`. | Duplicar contenido en dos dominios. |
| Landing | Presentar marca y dirigir al canal oficial cuando exista. | Implementar catálogo, checkout o administración. |
| Futura tienda | Catálogo, carrito, checkout y sus integraciones. | Compartir archivos internos o acceso directo a este hosting. |

## Estado de publicación

| Entorno | URL | Estado |
| --- | --- | --- |
| Producción temporal | `https://leocelador.github.io/maluwe-landing/` | Activo hasta migrar a Cloudflare Pages. |
| Producción objetivo | `https://www.maluwe.com.ar` | Pendiente de conectar Cloudflare Pages al repositorio. |
| Dominio raíz | `https://maluwe.com.ar` | Debe redirigir a `https://www.maluwe.com.ar`. |

## Estructura del proyecto

```text
maluwe-landing/
├── assets/             # imágenes optimizadas usadas por la landing
├── docs/
│   └── diseno-de-sistema.md
├── index.html          # estructura y contenido de la página
├── styles.css          # tokens visuales y estilos responsivos
└── README.md           # acceso rápido a publicación y documentación
```

Los tokens visuales se mantienen al inicio de `styles.css`. Antes de incorporar una imagen, optimizarla y guardarla con un nombre descriptivo en `assets/`; no depender de rutas externas ni de carpetas del panel operativo.

## Despliegue en Cloudflare Pages

1. En Cloudflare, conectar GitHub y seleccionar `LeoCelador/maluwe-landing`.
2. Crear un proyecto de **Pages** sin comando de build; el directorio de salida es `/`.
3. Usar `main` como rama de producción.
4. En **Custom domains**, asignar `www.maluwe.com.ar` como dominio principal y `maluwe.com.ar` como redirección.
5. Mantener la zona DNS de `maluwe.com.ar` en Cloudflare y confirmar que el certificado HTTPS esté activo.
6. Recién cuando el dominio responda correctamente, desactivar GitHub Pages para evitar dos orígenes de publicación.

No se necesita ningún token, variable de entorno ni secreto para esta landing. Las autorizaciones de GitHub y Cloudflare deben configurarse en sus respectivas interfaces, nunca dentro del repositorio.

## Flujo para cambios futuros

```text
Pedido de cambio → editar HTML/CSS/assets → revisar localmente → commit
→ push a rama → preview de Cloudflare Pages → aprobar → merge a main → producción
```

- Cambios pequeños de texto o estilo: realizar en una rama y revisar el preview.
- Nuevo CTA: definir antes destino, texto y responsable del canal; no publicar enlaces simulados.
- Formulario, analítica o píxeles: documentar propósito, datos recolectados, consentimiento y proveedor antes de incorporarlos.
- Catálogo o compra: se implementan en la futura tienda, no como scripts aislados en esta landing.

## Controles de calidad previos a publicar

- Probar la portada y el CTA en pantalla chica y grande.
- Confirmar que cada imagen cargue y tenga `alt` descriptivo.
- Verificar que no haya precios, información de contacto no aprobada ni enlaces rotos.
- Comprobar que el dominio use HTTPS y que `maluwe.com.ar` redirija a `www`.
- Confirmar que no se subieron archivos `.env`, claves, tokens ni datos personales.

## Decisiones abiertas

1. Definir el canal oficial del CTA: WhatsApp, Instagram o formulario.
2. Sustituir el wordmark tipográfico por el logo final en SVG o PNG transparente, cuando esté disponible.
3. Confirmar si se utilizará analítica y bajo qué política de privacidad.
4. Migrar la publicación de GitHub Pages a Cloudflare Pages al completar la conexión de la cuenta.

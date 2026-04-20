# RUBIK SOTA — Metaverse Compilation Engine

> **Un único archivo HTML de ~150KB que convierte cualquier imagen o vídeo en un catálogo inmersivo interactivo exportable, sin instalación, sin backend, sin límites.**

---

## ¿Qué es?

RubikSOTA es un **editor de metaversos comerciales** ejecutable directamente en el navegador. El editor y el viewer exportado conviven en un único archivo HTML autocontenido. El usuario configura salas, hotspots, marca, pagos y experiencia visual — y exporta un HTML listo para subir a cualquier servidor o CDN.

**No requiere:** Node.js, npm, backend, base de datos, ni cuenta en ningún servicio.  
**Requiere:** Un navegador moderno y un servidor HTTP para compartir el export.

---

## Stack Tecnológico

| Capa | Tecnología |
|---|---|
| Renderizado 2.5D | PIXI.js 7.3.2 |
| Animaciones | GSAP 3.12.2 |
| Lógica | Vanilla JS ES5 (IIFE, sin bundler) |
| Persistencia | localStorage con fallback en memoria |
| Pagos | Stripe Payment Links / mailto |
| Compartir | Web Share API + clipboard |
| Distribución | Single-file HTML (~150KB sin media) |

---

## Arquitectura

```
RubikSOTA.html
├── #editor-css          → Estilos del panel editor (NO se exportan)
├── #viewer-css          → Estilos del viewer (SE exportan íntegros)
├── Panel Editor (HTML)  → UI de configuración lado izquierdo
├── Preview (HTML)       → Canvas PIXI + UI del viewer en tiempo real
└── <script> IIFE
    ├── Estado global S  → Salas, hotspots, marca, tema, pagos
    ├── Fases A–I        → Lógica de todas las funcionalidades
    └── buildViewerScript() → Genera el JS del viewer como string
```

El export serializa `S` como JSON e inyecta el viewer script generado por `buildViewerScript()`. El HTML exportado es completamente autónomo.

---

## Fases Implementadas

| Fase | Funcionalidad | Descripción |
|---|---|---|
| A | Diferenciación visual hotspots | 3 tipos visuales: producto (dorado), portal (azul+aro), info (dashed) |
| B | Mini-mapa de navegación | Canvas 120×120 con nodos, aristas y click para teleport |
| C | Tour automático | Recorrido guiado por todas las salas con modales automáticos |
| D | Giroscopio | Parallax reactivo al movimiento en móvil (iOS + Android) |
| E | Hotspots condicionales | Desbloqueo por visitas, compras o tiempo en sala |
| F | Info Card | Tarjeta editorial flotante on-click con gradiente dinámico |
| G | Gradient Cards permanentes | Elementos visuales fijos posicionables con X/Y porcentuales |
| H | Social Viral | Compartir en 6 redes + deeplinks `?scene=` y `?product=` |
| I | Pagos duales | Stripe Payment Link + Reserva por email (mailto) |

---

## Uso Rápido

1. Abrir `RubikSOTA_Final_v7.html` en un navegador moderno
2. Subir una imagen o vídeo en la sección **Media de la Sala**
3. Hacer **doble clic** en el canvas para anclar hotspots
4. Configurar marca, tema y método de pago
5. Pulsar **⬇ EXPORTAR METAVERSO HTML**
6. Subir el HTML exportado a cualquier servidor (GitHub Pages, Netlify, etc.)

---

## Estructura de Datos

```javascript
S = {
  sceneId: 'scene_0',
  scenes: {
    [id]: {
      id, name, type,          // 'image' | 'video'
      img, vid, depth,         // Base64 o null
      depthInt,                // 0–1, intensidad efecto 3D
      hs: [ hotspot ],         // Array de hotspots
      cards: [ gradientCard ]  // Array de gradient cards
    }
  },
  brand:   { logo, title, sub, footer },
  theme:   { gold, surface, productColor, portalColor },
  snd:     { bg, hov, clk, suc, tel },
  bgAudio: boolean,
  payment: { method, paymentLink, sellerEmail, emailSubject }
}
```

---

## Hotspot Schema

```javascript
// Producto
{ id, type:'product', x, y, title, price, desc, gallery[], related[], condition }

// Portal
{ id, type:'portal', x, y, title, targetId, condition }

// Info Card
{ id, type:'info', x, y, cardTitle, cardBody, cardColor, condition }

// Gradient Card (en sc.cards, no en sc.hs)
{ id, x, y, title, body, bgColor, fgColor, width }
```

---

## Sistema de Condiciones

```javascript
condition: {
  type: 'visit_count' | 'item_purchased' | 'time_spent',
  sceneId: string,   // para visit_count y time_spent
  value: number      // N visitas | nombre producto | segundos
}
```

El progreso se persiste en `localStorage` con clave `rubik_sota_progress`.

---

## Export Flow

```
buildViewerScript(stateJson)
  → L[] de strings JS
  → L.join('\n')
  → inyectado en <script> del HTML exportado
```

El viewer exportado incluye: PIXI+GSAP desde CDN, el CSS del viewer, el JSON del estado, y todo el JS generado. Sin dependencias locales.

---

## Compatibilidad

| Browser | Soporte |
|---|---|
| Chrome 90+ | ✅ Completo |
| Firefox 88+ | ✅ Completo |
| Safari 14+ | ✅ Completo (giroscopio requiere permiso iOS) |
| Edge 90+ | ✅ Completo |
| IE11 | ❌ No soportado |

---

## Limitaciones Conocidas

- El export abre desde `file://` pero las funciones de compartir requieren HTTPS
- Stripe Payment Links no confirman el pago (sin backend)
- `mailto:` para reservas depende del cliente de correo del usuario
- `genDepth()` procesa imágenes hasta 1024px en el hilo principal

---

## Autor

**RUBIK SOTA** — [629554870](tel:629554870)  
*Idea, arquitectura y dirección: Rubik SOTA*

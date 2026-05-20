# Memoria de Sesión — Travel App Iguazú 2026

## Contexto

**Fecha:** 19 de Mayo 2026
**Usuario:** Nicolás (viaja con Melannie)
**Destino:** Cataratas del Iguazú, Argentina
**Fechas de viaje:** 8 – 12 Junio 2026 (4 noches, 5 días)
**Origen:** Santiago de Chile (SCL) → Puerto Iguazú (IGR)

## Necesidad

App/web app para organizar pendientes del viaje y centralizar toda la información en un solo lugar. Ya tienen la planificación avanzada, pero necesitan:

1. Seguimiento de pendientes (checklists)
2. Información centralizada (vuelos, hotel, seguro, póliza, links)
3. Solución simple técnicamente (MVP)
4. Alta tolerancia a fallos
5. Uso máximo de recursos locales
6. Funcionamiento en celular

## Decisiones Arquitectónicas

### Opción elegida: PWA + Fallback Local

Se evaluaron 3 opciones:

| Opción | Descripción | Decisión |
|--------|-------------|----------|
| A | Single HTML file (no PWA) | Descartada — iOS complicado para abrir archivos locales |
| B | PWA real (solo hosting) | Descartada — sin fallback local |
| C | PWA + fallback local | **Elegida** — app instalable + archivo local de respaldo |

### Razones de la decisión

- **PWA** permite instalación en home screen de ambos celulares
- **Funciona 100% offline** gracias al service worker
- **Archivo local** como respaldo si no hay internet al llegar
- **Deploy gratuito** en Netlify o GitHub Pages (2 minutos)
- **Cero dependencias** — no requiere backend, base de datos, ni build

### Tecnologías

- **HTML + CSS + JS vanilla** — todo en un solo archivo `index.html`
- **localStorage** — persistencia de estado de checkboxes
- **Service Worker** — caché offline de todos los assets
- **Web App Manifest** — configuración PWA (nombre, íconos, colores)
- **Sin frameworks** — máxima simplicidad y tolerancia a fallos

## Estructura de Archivos

```
travelApp/
├── index.html       ← App completa (HTML + CSS + JS inline)
├── manifest.json    ← Configuración PWA
├── sw.js            ← Service Worker para caché offline
└── icons/
    ├── icon.svg     ← Ícono vectorial original
    ├── icon-192.png ← Ícono 192x192 para PWA
    └── icon-512.png ← Ícono 512x512 para PWA
```

## Funcionalidades Implementadas

### 1. Dashboard
- Tabla de estado general (8 ítems: vuelos, hotel, entradas, seguro, eSIM, Global66, asientos, transfer)
- Barra de progreso general calculada desde checkboxes completados
- Resumen del viaje (fechas, duración, ruta, tipo, equipaje)
- Banner de instalación PWA (aparece cuando el navegador lo soporta)

### 2. Pendientes (9 categorías con checklists)
| Categoría | Items | Estado |
|-----------|-------|--------|
| eSIM | Comprar, verificar compatibilidad, activar | Pendiente |
| Asientos JetSMART | Revisar precio, decidir, seleccionar | Pendiente |
| Transfer hotel | Contactar, confirmar vuelo, confirmar horario | Pendiente |
| Gran Aventura | Revisar presupuesto, decidir, reservar | Opcional |
| Efectivo USD | Comprar, preparar emergencia, guardar separado | Pendiente |
| Global66 | Agregar USD, recibir tarjeta, activar, probar | Pendiente |
| Check-in vuelos | Sky Airline, JetSMART, descargar boarding passes | Pendiente |
| Bolsa impermeable | Comprar, probar cierre, llevar al parque | Pendiente |
| Repelente | Comprar en Chile, guardar en ziplock, llevar diario | Pendiente |

Cada checklist incluye recomendaciones y notas contextuales.

### 3. Info del Viaje (secciones colapsables)
- **Vuelos:** Ida (Sky Airline) y vuelta (JetSMART con espera nocturna en AEP)
- **Hotel:** La Aldea De La Selva Lodge (datos completos)
- **Parque Nacional:** Entradas compradas, estrategia de 2 días
- **Seguro:** IATI Mochilero (coberturas detalladas)
- **Estrategia financiera:** Cómo pagar en Argentina
- **Presupuesto:** Desglose completo con totales
- **Clima en Junio:** Ventajas y consideraciones
- **Enchufes y Electricidad:** 220V 50Hz, compatible con Chile

### 4. Itinerario Día a Día
- Día 1 (Lunes 8): Viaje y llegada
- Día 2 (Martes 9): Circuitos + Gran Aventura
- Día 3 (Miércoles 10): Garganta del Diablo
- Día 4 (Jueves 11): Día flexible (lado brasileño recomendado)
- Día 5 (Viernes 12): Regreso

### 5. Packing List
12 items esenciales con checkboxes (zapatillas, ropa, abrigo, poncho, powerbank, etc.)

### 6. Links Útiles
7 links directos a webs relevantes (parque, Gran Aventura, aerolíneas, seguro, eSIM, Global66)

### 7. Modo Emergencia
Acceso rápido a info crítica desde botón 🚨:
- Datos de vuelos (ida y vuelta)
- Hotel (check-in/out, habitación)
- Seguro (cobertura principal)
- Datos clave (aeropuerto, moneda, electricidad)

### 8. Tema Claro/Oscuro
Toggle manual con persistencia en localStorage

### 9. Navegación
- Tabs superiores con scroll horizontal
- Bottom navigation bar (6 secciones)
- Cards colapsables tipo acordeón

## Persistencia de Datos

- **localStorage key:** `iguazu2026_checks` — estado de todos los checkboxes
- **localStorage key:** `iguazu2026_theme` — preferencia de tema
- **Datos del viaje:** Hardcodeados en el HTML (no cambian)
- **Reset:** Botón para resetear todos los pendientes con confirmación

## Service Worker

Estrategia de caché: **Cache First, fallback a Network, fallback a Offline Page**

- Cachea todos los assets en la instalación
- Limpia caches viejos en la activación
- Intercepta fetch requests y sirve desde cache si está disponible
- Si no hay cache ni network, sirve `index.html` como fallback

## PWA Manifest

- **name:** Iguazú 2026 · Nicolás & Melannie
- **short_name:** Iguazú 2026
- **display:** standalone (sin barra de navegador al abrir desde home screen)
- **theme_color:** #0f766e (verde azulado)
- **background_color:** #f8fafc
- **orientation:** portrait
- **Íconos:** 192x192 y 512x512 con propósito "any maskable"

## Diseño

- **Mobile-first** — optimizado para pantallas de celular
- **Max-width:** 600px centrado para legibilidad en desktop
- **Colores:** Teal (#0f766e) como color primario
- **Tipografía:** System fonts (-apple-system, BlinkMacSystemFont, Roboto)
- **Touch-friendly:** Botones grandes, sin hover states dependientes
- **Sin scroll infinito:** Navegación por secciones

## Lo que NO incluye (decisiones explícitas)

- No hay backend ni base de datos
- No hay sync entre dispositivos (cada uno instala la suya)
- No hay login ni autenticación
- No hay notificaciones push
- No hay edición de datos del viaje (hardcodeados)
- No hay formularios ni inputs de usuario (solo checkboxes)

## Deploy

### Opción 1: Netlify Drop
1. Ir a https://app.netlify.com/drop
2. Arrastrar la carpeta `travelApp` completa
3. Obtener link HTTPS automático

### Opción 2: GitHub Pages
1. Crear repo público en GitHub
2. Push de todos los archivos
3. Settings → Pages → Source: main branch
4. Obtener link HTTPS automático

### Instalación en celular
- **Android:** Abrir link → menú ⋮ → "Instalar app"
- **iPhone:** Abrir en Safari → compartir → "Agregar a pantalla de inicio"

### Fallback local
El archivo `index.html` funciona abriéndolo directamente desde el gestor de archivos del celular (sin servidor). Los checkboxes funcionan con localStorage incluso en modo local.

## Datos del Viaje (Hardcodeados)

### Vuelos
- **Ida:** Sky Airline SCL → IGR, 08:00 → 15:30 (escala en AEP)
- **Vuelta:** JetSMART IGR → AEP 21:36 → 23:36, AEP → SCL 05:45 → 07:11

### Hotel
- **Nombre:** La Aldea De La Selva Lodge
- **Check-in:** Lunes 8 Junio 2026
- **Check-out:** Viernes 12 Junio 2026
- **Habitación:** Doble estándar
- **Incluye:** Desayuno + transfer aeropuerto
- **Condición:** No reembolsable

### Parque Nacional
- **Día 1 (Mar 9):** Entrada completa — Circuito Superior + Inferior + Gran Aventura
- **Día 2 (Mié 10):** 50% descuento — Garganta del Diablo + Senderos opcionales

### Seguro
- **Proveedor:** IATI Mochilero
- **Cobertura:** 500.000 € asistencia médica, repatriación, equipaje, rescate

### Presupuesto
- **Sin Gran Aventura:** USD 1.650–1.850
- **Con Gran Aventura:** USD 1.850–2.150

## Notas Técnicas

- Los íconos PNG fueron generados con Python (Pillow) a partir de un SVG base
- El service worker usa versión de cache `iguazu-2026-v1` (incrementar al hacer cambios)
- El estado de checkboxes se guarda como objeto JSON plano en localStorage
- La barra de progreso se calcula dinámicamente: `checked / total * 100`
- El tema se persiste y restaura al cargar la página

## Próximos Pasos Posibles

- Agregar campo para número de póliza del seguro
- Agregar teléfonos de emergencia de IATI
- Agregar confirmación del transfer del hotel cuando se confirme
- Actualizar estado de pendientes a "completado" visualmente en el dashboard
- Agregar countdown al viaje
- Agregar sección de notas libres

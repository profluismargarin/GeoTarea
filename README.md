# GeoTarea

Aplicación móvil multiplataforma para que un técnico, encuestador o estudiante en campo revise qué debe hacer en el día, ubique el sitio físico de cada tarea en un mapa, y registre la evidencia de su trabajo — todo desde el teléfono, incluso sin conexión permanente a internet.

Construida con **Ionic 8 + Angular 20 + Capacitor 8**.

## Módulos

| # | Módulo | Descripción |
|---|--------|-------------|
| 1 | **Avisos** | Feed de avisos de la empresa (spinner de carga, manejo de error, pull-to-refresh). |
| 2 | **Tareas** | CRUD completo sobre SQLite real, organizado en tres columnas tipo tablero ágil: Diagnóstico, Plan de Acción y Evaluación. Ver / editar / eliminar con botones directos, y mover de columna deslizando el ítem. |
| 3 | **Mapa** | Mapa interactivo con Leaflet + OpenStreetMap. Muestra todas las tareas geolocalizadas y centra la vista en la ubicación actual del usuario. Cada tarea tiene además un mini-mapa en su detalle para fijar su ubicación. |
| 4 | **Evidencia** | Foto de evidencia por tarea con la cámara del dispositivo. Reproductor de audio HTML5 con pistas para concentrarse mientras se trabaja, totalmente editable (crear, editar, eliminar, subir pista propia). |
| 5 | **Perfil** | Perfil editable (nombre, rol, correo, foto). Confirmación de asistencia en el lugar acercando una tarjeta NFC (modo solo lectura). |

## Stack Tecnológico

- **Ionic** ^8.0.0 — componentes de UI móvil
- **Angular** 20.3.25 — framework SPA (NgModules, `standalone: false`)
- **TypeScript** ~5.9.0
- **Capacitor** 8.4.1 — puente a APIs nativas (Android / iOS)
- **RxJS** ~7.8.0 — `Observable` y `BehaviorSubject`
- **@capacitor-community/sqlite** ^8.1.0 + **jeep-sqlite** ^2.8.0 + **sql.js** 1.11.0 — SQLite real en nativo, respaldado por WebAssembly/IndexedDB en navegador web
- **@capacitor/geolocation** ^8.2.0 — GPS
- **@capacitor/camera** ^8.2.1 — foto de evidencia y foto de perfil
- **@capgo/capacitor-nfc** ^8.1.7 — lectura nativa de tarjetas NFC
- **Leaflet** ^1.9.4 + **@types/leaflet** — mapa con tiles de OpenStreetMap

## Estructura del proyecto

```
src/app/
├── app.module.ts / app-routing.module.ts / app.component.ts
├── models/            # Tarea, Noticia, PerfilUsuario, Pista
├── services/          # Sqlite, Tareas, Noticias, Ubicacion, Nfc, Perfil, Musica
├── shared/            # leaflet-icon.ts
├── tabs/               # Barra de navegación inferior (ion-tabs)
└── pages/
    ├── noticias/        # Módulo 1
    ├── tareas/          # Módulo 2 (+ tarea-form/ modal)
    ├── tarea-detalle/   # Mini-mapa + cámara de evidencia
    ├── mapa/            # Módulo 3
    ├── evidencia/       # Módulo 4 (+ musica-form/ modal)
    └── perfil/          # Módulo 5 (+ perfil-form/ modal)
```

## Requisitos previos

- Node.js 20+ y npm
- [Ionic CLI](https://ionicframework.com/docs/cli): `npm install -g @ionic/cli`

## Instalación

```bash
npm install
```

## Ejecutar en el navegador (desarrollo)

```bash
ionic serve
```

La app abre automáticamente en `http://localhost:8100`.

> En el navegador de escritorio, la cámara usa el selector de archivo/webcam, la geolocalización pide permiso al propio navegador, y NFC no está disponible (se muestra un aviso). Para probar cámara, GPS y NFC reales, compila la app en un dispositivo Android.

## Compilar para Android

```bash
ionic build
npx cap add android      # solo la primera vez
npx cap sync android
npx cap open android
```

Luego, en Android Studio: **Run → Run 'app'**.

## Compilar para iOS (solo en Mac)

```bash
ionic build
npx cap add ios           # solo la primera vez
npx cap sync ios
npx cap open ios
```

Luego, en Xcode: **Product → Run** (⌘+R).

## Datos de ejemplo

Al iniciar por primera vez, la base de datos SQLite se siembra automáticamente con 7 tareas de ejemplo geolocalizadas en República Dominicana (Santo Domingo, Santiago, Punta Cana, La Romana, San Cristóbal), repartidas en las tres columnas del tablero. Este contenido vive en `src/app/services/sqlite.ts` (`sembrarDatosDeEjemplo`) y se controla con la constante `SEED_VERSION`: si cambias los datos de ejemplo, súbele el número de versión para que se reemplacen automáticamente en el próximo arranque, sin necesidad de borrar el almacenamiento del navegador a mano.

## Problemas conocidos y solución (troubleshooting)

| Problema | Causa | Solución |
|---|---|---|
| 404 al cargar `sql-wasm.wasm` en el navegador | El `.wasm` de `sql.js` no se copia al build de Angular por defecto | Ya resuelto: glob de assets en `angular.json` |
| `LinkError` al instanciar el WebAssembly de SQLite | Versión de `sql.js` incompatible con el glue code de `jeep-sqlite` | Ya resuelto: `sql.js` fijado en `1.11.0` exacto |
| `Map container not found` en el detalle de tarea | Leaflet se inicializa antes de que Angular renderice el `*ngIf` | Ya resuelto: `ChangeDetectorRef.detectChanges()` antes de `L.map()` |
| `Not implemented on web` al pedir ubicación | `checkPermissions()`/`requestPermissions()` no existen en la implementación web de `@capacitor/geolocation` | Ya resuelto: se omiten en plataforma web |

Si ves datos viejos o desactualizados en el navegador tras un cambio, prueba en una ventana de incógnito o borra el almacenamiento del sitio (`localStorage` + IndexedDB) desde las DevTools.

## Documentación técnica

Ver `Geotarea DocumenatciónTécnica.docx` (o la versión adaptada `GeoTarea - Documentación Técnica.docx` en el Escritorio) para la documentación académica completa: arquitectura, modelo de negocio, diseño de interfaces, desarrollo por módulos y pruebas.

## Equipo

- Luis Margarín, M.A. — 100076383
- Luis Fernando Ortiz Rosario — 100025201
- Olirys Suárez Cruz — 100056027
- José E. Santana — 100054414

Proyecto final de la asignatura Programación de Dispositivos Móviles — Universidad Abierta Para Adultos (UAPA).

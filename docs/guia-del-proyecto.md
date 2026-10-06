# Guía del proyecto

Esta guía sirve para orientarte en el código mientras lo estudias o lo explicas. La aplicación es una demo de una sola página: cambia entre el área pública y administración sin recargar.

## Recorrido de una reserva

1. El huésped indica entrada, salida y número de personas en `#search-form`.
2. `validateSearch()` comprueba que haya fechas, que la salida sea posterior y que la estancia no supere 30 noches.
3. `renderApartments()` descarta alojamientos despublicados, comprueba capacidad y llama a `isAvailable()` para detectar solapes.
4. `showDetail()` presenta la información propia del alojamiento: entorno, servicios, normas y horarios.
5. `showBooking()` calcula noches, alojamiento, limpieza y total. Ese resumen siempre acompaña al formulario.
6. `submitBooking()` valida los datos y vuelve a llamar a `isAvailable()`. Esta segunda comprobación representa el caso en el que otra persona reserva mientras el formulario estaba abierto.
7. Si las fechas siguen libres, se guarda una copia del total en la reserva, se persiste en `localStorage` y se actualizan el buscador, el calendario y las tablas.

El criterio de solape está en `overlaps()`: una reserva `[entrada, salida)` ocupa la entrada y las noches intermedias, pero no el día de salida. Por eso una salida y una nueva entrada pueden coincidir.

## Archivos principales

### `dist/index.html`

Contiene la estructura estable: cabecera, buscador, zonas donde JavaScript dibuja contenido, panel lateral de administración, diálogo de cancelación y pie. Si necesitas añadir una sección completamente nueva, empieza aquí.

### `dist/styles.css`

Define la identidad compartida. Las variables de `:root` controlan colores, bordes y radios. La parte pública usa composiciones amplias y fotografía; las clases que empiezan por `admin-` concentran el diseño más denso del panel. Los dos bloques `@media` adaptan la interfaz a tableta y móvil.

### `dist/app.js`

Está dividido de forma sencilla:

- `apartments` y `seedBookings`: datos ficticios iniciales.
- Funciones pequeñas de fecha, moneda, escape y disponibilidad.
- Funciones `render…`: actualizan una zona concreta de la página.
- `showDetail`, `showBooking` y `submitBooking`: recorrido público.
- `renderAdmin`, `renderCalendar`, `renderReservations` y `renderProperties`: gestión.
- Escuchadores al final: conectan formularios y botones estáticos.

No hay una capa de servicios porque no hay servidor. Si el proyecto creciera, el primer paso razonable sería mover disponibilidad, creación y cancelación a una API, no crear abstracciones genéricas dentro de esta demo.

## Cómo modificar una funcionalidad

### Cambiar precios o limpieza

Edita `price` o `cleaning` en el alojamiento correspondiente de `apartments`. El resumen se recalcula solo. Las reservas ya confirmadas conservan su `total` guardado.

### Añadir un alojamiento

Copia un objeto de `apartments`, asigna un `id` único y completa todos sus campos. Añade la foto en `dist/assets` y usa su ruta relativa en `image`. Evita reutilizar descripciones: capacidad, distribución, normas y entorno deben corresponder a esa vivienda.

### Cambiar la regla de disponibilidad

Revisa juntas `overlaps()` e `isAvailable()`. Después prueba al menos estos casos:

- Entrada el mismo día en que sale otra reserva: permitido.
- Entrada durante una estancia existente: rechazado.
- Estancia que contiene por completo otra reserva: rechazada.
- Reserva cancelada en las mismas fechas: permitida.

### Añadir un nuevo estado de error

Escribe el mensaje junto a la acción que puede fallar y explica cómo continuar. Usa `role="alert"` si requiere atención inmediata o `role="status"` si es una actualización no urgente. No reutilices un mensaje genérico si conoces la causa.

## Pruebas manuales útiles

- Busca del 8 al 11 de octubre de 2026: Casa Álamos debe aparecer como no disponible.
- Intenta reservar sin nombre o con un correo incorrecto: el formulario explica qué corregir.
- Abre administración, cancela una reserva y vuelve al calendario: las noches quedan libres.
- Despublica un alojamiento: desaparece del área pública y mantiene su historial.
- Intenta subir un archivo que no sea JPG, PNG o WebP, o uno mayor de 3 MB: aparece el motivo concreto del rechazo.
- Pulsa “Simular sesión caducada”: las acciones se bloquean hasta volver a entrar.

## Límites que no debes ocultar

Esta versión no puede impedir reservas simultáneas entre dos navegadores porque no comparte una base de datos. Tampoco autentica al propietario ni al huésped, cobra pagos o envía mensajes. Son límites deliberados de una demo frontal; una versión real necesita servidor, base de datos transaccional, autenticación y gestión segura de archivos.

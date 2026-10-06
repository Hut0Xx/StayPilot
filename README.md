# StayPilot

Demo navegable para gestionar un pequeño conjunto de apartamentos turísticos ficticios en Málaga. Incluye una zona pública de búsqueda y reserva, y un panel de administración para consultar agenda, disponibilidad, reservas y publicación de alojamientos.

## Ejecutar en local

No necesita instalar dependencias ni compilar.

```powershell
python -m http.server 4173 --directory dist
```

Después abre `http://localhost:4173`.

## Qué está implementado

- Búsqueda por entrada, salida y número de huéspedes.
- Comprobación de disponibilidad contra reservas confirmadas.
- Fichas diferentes para tres alojamientos ficticios.
- Detalle con servicios, normas y horarios.
- Reserva con validación y desglose invariable de noches, limpieza y total.
- Segunda comprobación de disponibilidad justo antes de confirmar.
- Persistencia de reservas y cancelaciones en `localStorage`.
- Agenda, calendario de 14 días, filtro de reservas y cancelación.
- Publicar y despublicar alojamientos durante la sesión.
- Validación de formato y tamaño al cambiar una fotografía.
- Estados específicos para falta de resultados, formulario incorrecto, fechas ocupadas, sesión caducada, alojamiento despublicado, reserva cancelada y foto rechazada.

Todo el contenido se identifica como demostración. Los nombres, correos, teléfonos, reservas y ubicaciones aproximadas son ficticios.

## Decisiones técnicas

### Arquitectura

La demo usa HTML, CSS y JavaScript sin framework. El alcance es pequeño y no hay un servidor real: esta elección deja a la vista las reglas de negocio y evita dependencias que no aportan valor al ejercicio. `dist/index.html` contiene la estructura, `dist/styles.css` el sistema visual y `dist/app.js` los datos, reglas y renderizado.

### Cómo se evita una doble reserva

La función `isAvailable` considera ocupada una noche cuando dos intervalos se solapan. La salida no bloquea esa noche, de modo que puede coincidir con una nueva entrada. La comprobación se ejecuta al buscar y otra vez al confirmar, que es cuando podría haberse producido un cambio.

En producción esta regla debe ejecutarse además en una transacción de base de datos con una restricción o bloqueo. `localStorage` no coordina navegadores distintos y, por tanto, no puede garantizar por sí solo la ausencia de dobles reservas.

### Cómo se calculan y conservan los precios

Cada alojamiento tiene tarifa nocturna y limpieza. El total mostrado es `número de noches × tarifa + limpieza`. Al confirmar se guarda el total calculado dentro de la reserva; así una futura modificación de la tarifa no cambia el importe histórico de esa reserva.

### Protección de las reservas de cada cliente

La interfaz escapa los nombres antes de insertarlos en HTML y no muestra datos de contacto en el área pública. En esta demo no existe autenticación real ni aislamiento por cuenta. Una versión de producción debe asociar cada reserva al usuario autenticado y comprobar la autorización en el servidor en cada lectura y escritura.

### Simplificaciones conscientes

- No hay pagos, envío de correo, facturas ni integración con canales externos.
- La sesión caducada es un estado demostrable, no una autenticación real.
- Las fotos elegidas desde administración duran solo hasta recargar la página.
- Publicar o despublicar también dura solo durante la sesión.
- El calendario presenta 14 días fijos para mantener la demo directa.
- Los datos se guardan únicamente en el navegador actual.

## Estructura

```text
dist/
  index.html          Estructura de las vistas
  styles.css          Identidad visual y diseño adaptable
  app.js              Datos, disponibilidad, reservas y panel
  assets/             Fotografías de demostración generadas para el proyecto
docs/
  guia-del-proyecto.md
.openai/
  hosting.json        Configuración de publicación privada
```

Consulta [docs/guia-del-proyecto.md](docs/guia-del-proyecto.md) para seguir el recorrido de una reserva y localizar dónde modificar cada parte.

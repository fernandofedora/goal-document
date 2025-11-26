# Flujo de desarrollo

- Convenciones de ramas: `feature/`, `fix/`, `docs/`.
- PRs con revisión obligatoria.
- Lint y formato consistente.
- Commits claros y atómicos.

## Flujos del sistema

### Arranque de cuenta
- Registrar usuario y hacer login.
- Configurar categorías y tarjetas.
- Definir presupuesto mensual.
- Ver: `Usuarios → Guía de inicio` (`user/guia-inicio.md`).

### Registro de gasto
- Crear transacción tipo “Gasto” con descripción, monto, fecha y categoría.
- Elegir método de pago (efectivo/tarjeta) y tarjeta si aplica.
- Ver: `Usuarios → Transacciones` (`user/transacciones.md`).
- API: `POST /api/transactions` en `Backend/API` (`dev/backend.md`).

### Cierre mensual
- Consultar resumen del mes seleccionado.
- Exportar Excel para análisis.
- Ajustar presupuesto del siguiente mes.
- Ver: `Usuarios → Reportes` (`user/reportes.md`) y `Usuarios → Presupuestos` (`user/presupuestos.md`).
- API: `GET /api/stats/summary?period=YYYY-MM` y `GET /api/stats/export?period=YYYY-MM`.

### Mantenimiento de categorías y tarjetas
- Editar/crear categorías y tarjetas según tus necesidades.
- Ver: `Usuarios → Tarjetas` (`user/tarjetas.md`).
- API: `POST/PUT/DELETE /api/categories`, `POST/PUT/DELETE /api/cards`.

## Enlaces útiles
- Backend/API: `dev/backend.md`
- Base de datos: `dev/base-datos.md`

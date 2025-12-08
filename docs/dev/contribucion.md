# Guía de Contribución

Esta página explica cómo puedes iniciar a colaborar en el proyecto, desde proponer ideas o reportar bugs hasta el flujo para integrar cambios en QA y producción.

## Acceso al tablero del proyecto
- Solicita acceso al tablero en: `https://trello.com/b/xCcgPrw6/goals-project`.
- Una vez aceptado, podrás crear y mover tarjetas según el flujo descrito.

## Crear una tarjeta (feature o bug)
- Feature (nueva funcionalidad):
  - Título claro y conciso.
  - Descripción detallada con objetivo, alcance y dependencias.
  - Si es posible, incluye un demo (enlace, capturas) y criterios de aceptación.
- Bug (error):
  - Título claro.
  - Descripción del problema.
  - Pasos para reproducir.
  - Resultado esperado.
  - Demo o evidencia si es posible (capturas, video, logs).

## Flujo de columnas en Trello
- Ideas: todas las tarjetas nuevas entran aquí por defecto.
- To‑Do: tarjetas aprobadas por el responsable del proyecto pasan a esta columna.
- In‑Progress: cuando un desarrollador toma una tarjeta, la mueve acá y empieza a trabajar.
- Review: cuando termina la implementación, la tarjeta se mueve aquí para revisión (code review).
- QA: tras la aprobación del PR, los cambios pasan a la rama de QA para pruebas funcionales.
- Production: cuando QA valida, los cambios se despliegan a producción.

## Buenas prácticas para nombres de ramas
- Formato recomendado: `<tipo>/<scope>-<descripcion-corta>`.
- Usa minúsculas, guiones, sin espacios ni acentos; evita nombres largos.
- Incluye un identificador del ticket si aplica (p. ej. `feat/ui-123-...`).
- Tipos comunes:
  - `feat/` para nuevas funcionalidades.
  - `bugfix/` o `fix/` para correcciones de errores.
  - `hotfix/` para correcciones urgentes en producción.
  - `chore/` para tareas de mantenimiento (build, dependencias).
  - `docs/` para documentación.
- Ejemplos:
  - `feat/dashboard-year-filter`
  - `fix/export-month-zero-padding`
  - `hotfix/auth-token-refresh`
  - `chore/update-deps-2025-12`
  - `docs/contribucion-tutorial`

## Pull Request (PR)
- Crea un PR desde tu rama hacia la rama objetivo (por ejemplo, `qa` o `main` según el flujo).
- Enlaza el PR dentro de la tarjeta Trello que estás trabajando.
- Descripción del PR:
  - Resumen del cambio.
  - Referencia a la tarjeta (enlace).
  - Alcance, impacto y riesgos.
  - Pasos de prueba sugeridos.
- Cuando el PR esté listo, mueve la tarjeta a **Review**.

## Revisión y QA
- Code review: otro desarrollador revisa el PR (estilo, seguridad y funcionalidad).
- Aprobado el PR, se envía a la rama de **QA**.
- QA realiza pruebas funcionales siguiendo los criterios de aceptación y pasos de prueba.
- Si QA pasa, se procede a **Producción**.

## Producción
- Tras la validación en QA, los cambios se despliegan a producción.
- Si se detectan incidencias post‑deploy, se crean tarjetas de `hotfix` siguiendo el mismo flujo.

## Notas adicionales
- Mantén commits claros y atómicos; usa mensajes descriptivos.
- Adjunta evidencias (capturas, GIFs, enlaces a ambientes de staging) cuando sea útil.
- Coordina dependencias con el equipo (por ejemplo, cambios en API que afectan a la UI).

## Ejemplos y plantillas de tarjetas

### Plantilla para Feature
```
Título: [Feature] Filtro por año en Dashboard

Descripción:
- Objetivo: permitir seleccionar un año y mostrar datos de enero–diciembre.
- Alcance: UI (selector de año), lógica de carga mensual, exportación.
- Dependencias: endpoint /stats/summary existente.

Criterios de aceptación:
- El gráfico muestra 12 meses del año seleccionado.
- Cambiar el año actualiza el gráfico y exportación.
- La UI no se rompe si falta data mensual.

Demo / Evidencia:
- Captura de pantalla del selector y gráfico
- Enlace a staging si disponible

Checklist:
- [ ] UI implementada
- [ ] Lógica de datos
- [ ] Exportación
- [ ] Pruebas manuales básicas
```

### Plantilla para Bug
```
Título: [Bug] Exportación falla sin cero en mes

Descripción:
- Al exportar 2025-3, el backend espera 2025-03.
- El cliente envía sin cero y retorna error 400.

Pasos para reproducir:
1. Ir a Dashboard
2. Seleccionar marzo 2025
3. Click en "Export XLSX"

Resultado esperado:
- Exportación exitosa para 2025-03

Demo / Evidencia:
- Captura de error en consola
- Respuesta del backend (payload)

Checklist:
- [ ] Reproducido
- [ ] Fix aplicado (zero-padding)
- [ ] Verificado en UI y backend
```

### Buenas prácticas para adjuntar evidencia
- Incluye al menos una captura del problema o de la funcionalidad terminada.
- Si hay entorno de staging, añade el enlace y credenciales temporales si aplica.
- Para bugs, agrega logs o mensajes de error (texto) que ayuden al diagnóstico.

## Estados de PR
- Draft: trabajo en progreso; aún no listo para revisión.
- Ready for Review: listo para revisión técnica; incluye descripción y enlace a la tarjeta.
- Changes Requested: se requieren ajustes; el autor actualiza el PR y responde comentarios.
- Approved: aprobado por revisores; listo para integrarse.
- Merge a QA: se integra en la rama de QA para pruebas funcionales.
- Release a Producción: tras validar en QA, se despliega a producción.

## Checklist de Code Review
- Lint y formato: sin errores de lint; estilo consistente.
- Funcionalidad: cumple criterios de aceptación; casos borde probados.
- Seguridad: manejo correcto de tokens; sin secretos en código; validación de entrada; evitar inyección.
- Contrato de API: rutas, métodos, parámetros y estados de respuesta coherentes; documentación actualizada.
- Rendimiento: sin consultas/llamadas redundantes; evita N+1; procesamiento razonable.
- Accesibilidad/UI: textos alternativos, roles apropiados, estados visibles, mensajes de error claros.
- Documentación: README o notas actualizadas si el cambio impacta instalación/uso.
- Commits y rama: mensajes claros y atómicos; rama con buen nombre (feat/fix/hotfix/chore/docs).
- Entorno: variables (`.env`, `VITE_API_URL`, `SECRET_KEY`, DB_*) correctas y seguras; sin hardcode.

## Ejemplos de mensajes de commit
- Estilo recomendado: Conventional Commits.
- Formato: `tipo(scope): resumen corto` y, opcionalmente, cuerpo con contexto.
- Referencia a tarjeta: agrega el enlace a Trello o ID relacionado.

Ejemplos:

```
feat(dashboard): agregar filtro de año en resumen

- Permite seleccionar año y ver meses enero–diciembre.
Refs: Trello https://trello.com/c/XYZ123
```

```
fix(export): aplicar zero-padding al mes para /stats/export

- Corrige error 400 cuando el mes es <10.
Refs: Trello https://trello.com/c/ABC789
```

```
docs(contribucion): añadir plantillas de tarjetas y buenas prácticas
```

```
chore(deps): actualizar dependencias y ajustar scripts
```

```
refactor(auth): simplificar middleware de validación de token
```

```
perf(stats): cachear consultas de resumen mensual
```

```
test(transactions): cubrir casos de creación y borrado
```

Ejemplo con cambio rompedor:

```
feat(api): unificar endpoints de /income y /expense en /transactions

BREAKING CHANGE: se eliminan /income y /expense; usar /transactions.
Refs: Trello https://trello.com/c/DEF456
```

## Definition of Done (DoD)
- Criterios de aceptación de la tarjeta cumplidos y casos borde contemplados.
- Lint y build pasan sin errores (`goals-client`: `npm run build`; `goals-server`: iniciar sin fallos).
- Documentación actualizada (README, docs o notas relevantes).
- Variables de entorno revisadas y seguras (`VITE_API_URL`, `SECRET_KEY`, `POSTGRES_*`).
- PR enlazado a la tarjeta Trello, revisado y aprobado; estado actualizado en Trello.
- Validado en rama `qa` por QA sin regresiones funcionales.
- Manejo de errores y logs adecuado; sin secretos en código ni consola.
- Evidencia adjunta en la tarjeta (capturas, demo, pasos de prueba).
- Checklist de code review satisfecho.

# Backend/API (goals-server)

## Estructura
- Express + Sequelize.
- Rutas: `/api/auth`, `/api/categories`, `/api/cards`, `/api/transactions`, `/api/budgets`, `/api/stats`.
- Middleware: autenticación (`authMiddleware`), CORS, JSON parser.

## Ejecución local
- Clonar: `git clone https://github.com/fernandofedora/goals-server`
- Instalar: `npm install`
- Configurar `.env` (DB, JWT_SECRET)
- Dev: `npm run dev` (por defecto puerto `4000`)

## Buenas prácticas
- No loggear secretos.
- Validar entradas y manejar errores.

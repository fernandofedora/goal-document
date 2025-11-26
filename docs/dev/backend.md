# Backend/API (goals-server)

## Visión general
- Framework: Express con Sequelize.
- Rutas principales: `/api/auth`, `/api/categories`, `/api/cards`, `/api/transactions`, `/api/budgets`, `/api/stats`.
- Seguridad: JWT en el header `Authorization: Bearer <token>` para todas las rutas (excepto `auth`).

## Puesta en marcha
- Clonar: `git clone https://github.com/fernandofedora/goals-server`
- Instalar: `npm install`
- `.env` requerido:
  - `SECRET_KEY` (para firmar JWT)
  - Credenciales de DB (`DATABASE_URL` o variables por separado)
- Ejecutar: `npm run dev` (`http://localhost:4000`)

Base URL:
- `http://localhost:4000/api`

---

## Autenticación avanzada

=== "Bash (macOS/Linux)"
```bash
# Login y captura del token
TOKEN=$(curl -s -X POST http://localhost:4000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"ana@example.com","password":"secret123"}' | jq -r .token)

# Usar el token en una petición autenticada
curl -s http://localhost:4000/api/categories \
  -H "Authorization: Bearer $TOKEN" | jq
```

=== "PowerShell (Windows)"
```powershell
# Login y captura del token
$body = @{ email = 'ana@example.com'; password = 'secret123' } | ConvertTo-Json
$response = Invoke-RestMethod -Method Post -Uri 'http://localhost:4000/api/auth/login' -ContentType 'application/json' -Body $body
$TOKEN = $response.token

# Usar el token
Invoke-RestMethod -Uri 'http://localhost:4000/api/categories' -Headers @{ Authorization = "Bearer $TOKEN" }
```

=== "HTTPie"
```bash
# Login
http POST :4000/api/auth/login email=ana@example.com password=secret123
# Con bearer
http GET :4000/api/categories Authorization:"Bearer <jwt>"
```

---

## Autenticación (`/api/auth`)
- `POST /register`
  - Body: `{ name, email, password }`
  - Respuestas:
    - `200`: `{ id, name, email }`
    - `400`: `Email already registered`
- `POST /login`
  - Body: `{ email, password }`
  - `200`: `{ token, user: { id, name, email } }`
  - `400`: `Invalid credentials`
- `POST /reset-start`
  - Body: `{ email }`
  - `200`: `{ exists: boolean }`
- `POST /reset`
  - Body: `{ email, password }`
  - `200`: `{ success: true }` o `404: Email not found`

Uso del token:
- Añade `Authorization: Bearer <token>` en todas las demás rutas.

### Ejemplos
```json
// POST /api/auth/register (request)
{
  "name": "Ana",
  "email": "ana@example.com",
  "password": "secret123"
}

// 200 (response)
{
  "id": 1,
  "name": "Ana",
  "email": "ana@example.com"
}
```

```json
// POST /api/auth/login (request)
{
  "email": "ana@example.com",
  "password": "secret123"
}

// 200 (response)
{
  "token": "<jwt>",
  "user": { "id": 1, "name": "Ana", "email": "ana@example.com" }
}
```

```json
// POST /api/auth/reset-start (request)
{ "email": "ana@example.com" }

// 200 (response)
{ "exists": true }
```

```json
// POST /api/auth/reset (request)
{ "email": "ana@example.com", "password": "newPass123" }

// 200 (response)
{ "success": true }
```

=== "curl"
```bash
curl -X POST http://localhost:4000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"ana@example.com","password":"secret123"}'
```

=== "HTTPie"
```bash
http POST :4000/api/auth/login email=ana@example.com password=secret123
```

#### Códigos de estado
| Operación | 200 | 400 | 404 | 500 |
|---|---|---|---|---|
| register | creado usuario | email ya registrado | — | error servidor |
| login | token y usuario | credenciales inválidas | — | error servidor |
| reset-start | exists: true/false | email requerido | — | error servidor |
| reset | success: true | email/password requeridos | email no encontrado | error servidor |

---

## Categorías (`/api/categories`) [auth]
- `GET /` → Lista categorías del usuario.
- `POST /` → Crea categoría.
  - Body: `{ name, color, type }` con `type ∈ { 'expense', 'income' }`
  - `200`: `{ id, name, color, type, UserId }`
- `PUT /:id` → Actualiza nombre/color/type.
- `DELETE /:id` → Elimina.

Notas:
- El sistema separa categorías por tipo (gasto/ingreso) para simplificar el filtrado.

### Ejemplos
```json
// GET /api/categories (response)
[
  { "id": 10, "name": "Alimentación", "color": "#22c55e", "type": "expense", "UserId": 1 },
  { "id": 11, "name": "Salario", "color": "#3b82f6", "type": "income", "UserId": 1 }
]
```

```json
// POST /api/categories (request)
{ "name": "Transporte", "color": "#f59e0b", "type": "expense" }

// 200 (response)
{ "id": 12, "name": "Transporte", "color": "#f59e0b", "type": "expense", "UserId": 1 }
```

=== "curl"
```bash
curl -X GET http://localhost:4000/api/categories \
  -H "Authorization: Bearer <token>"
```

=== "HTTPie"
```bash
http :4000/api/categories "Authorization:Bearer <token>"
```

#### Códigos de estado
| Operación | 200 | 400 | 404 | 500 |
|---|---|---|---|---|
| list | array de categorías | — | — | error servidor |
| create | categoría creada | cuerpo inválido | — | error servidor |
| update | categoría actualizada | cuerpo inválido | no encontrada | error servidor |
| delete | { success: true } | — | no encontrada | error servidor |

---

## Tarjetas (`/api/cards`) [auth]
- `GET /` → Lista tarjetas.
- `POST /` → Crea tarjeta.
  - Body: `{ name, color, last4 }`
- `PUT /:id` → Actualiza `{ name, color, last4 }`
- `DELETE /:id` → Elimina.

Notas:
- Las tarjetas solo se usan cuando `paymentMethod = 'card'` en gastos.

### Ejemplos
```json
// GET /api/cards (response)
[
  { "id": 5, "name": "Visa Personal", "color": "#0ea5e9", "last4": "1234", "UserId": 1 }
]
```

```json
// POST /api/cards (request)
{ "name": "Mastercard Trabajo", "color": "#f43f5e", "last4": "9876" }

// 200 (response)
{ "id": 6, "name": "Mastercard Trabajo", "color": "#f43f5e", "last4": "9876", "UserId": 1 }
```

=== "curl"
```bash
curl -X POST http://localhost:4000/api/cards \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"name":"Visa Personal","color":"#0ea5e9","last4":"1234"}'
```

=== "HTTPie"
```bash
http POST :4000/api/cards Authorization:"Bearer <token>" \
  name="Visa Personal" color="#0ea5e9" last4="1234"
```

#### Códigos de estado
| Operación | 200 | 400 | 404 | 500 |
|---|---|---|---|---|
| list | array de tarjetas | — | — | error servidor |
| create | tarjeta creada | cuerpo inválido | — | error servidor |
| update | tarjeta actualizada | cuerpo inválido | no encontrada | error servidor |
| delete | { success: true } | — | no encontrada | error servidor |

---

## Transacciones (`/api/transactions`) [auth]
- `GET /`
  - Devuelve transacciones del usuario con `include: [Category, Card]` ordenadas por `date DESC`.
- `POST /`
  - Body: `{ type, description, categoryId, amount, date, paymentMethod, cardId }`
  - Crea y devuelve la transacción incluyendo relaciones.
- `PUT /:id`
  - Actualiza los mismos campos; mantiene coherencia: `CategoryId = categoryId || null`, `CardId = cardId || null`.
- `DELETE /:id`
  - Elimina la transacción si pertenece al usuario.

Validaciones de negocio:
- `type ∈ { 'expense','income' }`
- `paymentMethod ∈ { 'cash','card' }`; si es `card`, se recomienda validar `cardId` en el cliente.
- `amount` es decimal (10,2); `date` es `DATEONLY (YYYY-MM-DD)`.

### Ejemplos
```json
// GET /api/transactions (response)
[
  {
    "id": 100,
    "type": "expense",
    "description": "Supermercado",
    "amount": "45.90",
    "date": "2025-11-24",
    "paymentMethod": "card",
    "UserId": 1,
    "CategoryId": 10,
    "CardId": 5,
    "Category": { "id": 10, "name": "Alimentación", "color": "#22c55e", "type": "expense" },
    "Card": { "id": 5, "name": "Visa Personal", "color": "#0ea5e9", "last4": "1234" }
  }
]
```

```json
// POST /api/transactions (request)
{
  "type": "expense",
  "description": "Taxi",
  "categoryId": 12,
  "amount": "8.50",
  "date": "2025-11-25",
  "paymentMethod": "cash",
  "cardId": null
}

// 200 (response)
{
  "id": 101,
  "type": "expense",
  "description": "Taxi",
  "amount": "8.50",
  "date": "2025-11-25",
  "paymentMethod": "cash",
  "UserId": 1,
  "CategoryId": 12,
  "CardId": null,
  "Category": { "id": 12, "name": "Transporte", "color": "#f59e0b", "type": "expense" },
  "Card": null
}
```

=== "curl"
```bash
curl -X GET "http://localhost:4000/api/transactions" \
  -H "Authorization: Bearer <token>"
```

=== "HTTPie"
```bash
http :4000/api/transactions Authorization:"Bearer <token>"
```

#### Códigos de estado
| Operación | 200 | 400 | 404 | 500 |
|---|---|---|---|---|
| list | array con relaciones | — | — | error servidor |
| create | transacción creada | campos inválidos | — | error servidor |
| update | transacción actualizada | campos inválidos | no encontrada | error servidor |
| delete | { success: true } | — | no encontrada | error servidor |

---

## Presupuestos (`/api/budgets`) [auth]
- `GET /` → Lista presupuestos del usuario.
- `POST /` → Crea `{ month, year, amount }`.
- `PUT /:id` → Actualiza `{ month, year, amount }`.
- `DELETE /:id` → Elimina.

Notas:
- Un presupuesto es mensual (mes/año). El dashboard compara consumo de gastos contra el presupuesto del mes.

### Ejemplos
```json
// POST /api/budgets (request)
{ "month": 11, "year": 2025, "amount": "500.00" }

// 200 (response)
{ "id": 3, "month": 11, "year": 2025, "amount": "500.00", "UserId": 1 }
```

=== "curl"
```bash
curl -X PUT http://localhost:4000/api/budgets/3 \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"month":11,"year":2025,"amount":"650.00"}'
```

=== "HTTPie"
```bash
http PUT :4000/api/budgets/3 Authorization:"Bearer <token>" \
  month:=11 year:=2025 amount="650.00"
```

#### Códigos de estado
| Operación | 200 | 400 | 404 | 500 |
|---|---|---|---|---|
| list | array de presupuestos | — | — | error servidor |
| create | presupuesto creado | cuerpo inválido | — | error servidor |
| update | presupuesto actualizado | cuerpo inválido | no encontrado | error servidor |
| delete | { success: true } | — | no encontrado | error servidor |

---

## Estadísticas (`/api/stats`) [auth]
- `GET /summary?period=<YYYY-MM|all>`
  - Resumen: `{ totals, categories, incomeVsExpense, paymentMethods, perCard, budgetAmount }`.
  - Filtros: si `period !== 'all'`, aplica rango `[YYYY-MM-01, YYYY-MM-último día]`.
- `GET /export?period=<YYYY-MM|all>`
  - Devuelve un `.xlsx` con hojas: `Transactions`, `Overview`, `IncomeVsExpenses`, `Categories`, `BudgetVsActual`, `PerCard`.

### Ejemplos
```json
// GET /api/stats/summary?period=2025-11 (response)
{
  "totals": { "income": 1200.00, "expense": 800.00, "transactions": 42, "balance": 400.00 },
  "categories": [ { "name": "Alimentación", "amount": 230.50, "color": "#22c55e" } ],
  "incomeVsExpense": [ { "date": "2025-11-01", "income": 0, "expense": 35.20 } ],
  "paymentMethods": { "cash": 320.00, "card": 480.00 },
  "perCard": { "Visa Personal": 300.00, "Mastercard Trabajo": 180.00 },
  "budgetAmount": 500.00
}
```

```text
// GET /api/stats/export?period=2025-11
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename=transactions_2025-11.xlsx
```

=== "curl"
```bash
curl -G http://localhost:4000/api/stats/summary \
  -H "Authorization: Bearer <token>" \
  --data-urlencode period=2025-11
```

=== "HTTPie"
```bash
http GET :4000/api/stats/summary Authorization:"Bearer <token>" period==2025-11
```

#### Códigos de estado
| Operación | 200 | 400 | 404 | 500 |
|---|---|---|---|---|
| summary | métricas del período | `period` inválido | — | error servidor |
| export | archivo Excel | `period` inválido | — | error servidor |

---

## Resumen de endpoints

| Método | Ruta | Auth | Body (req) | Respuesta (principal) |
|---|---|---|---|---|
| POST | `/api/auth/register` | No | `name,email,password` | `id,name,email` |
| POST | `/api/auth/login` | No | `email,password` | `token,user` |
| POST | `/api/auth/reset-start` | No | `email` | `exists:boolean` |
| POST | `/api/auth/reset` | No | `email,password` | `success:boolean` |
| GET | `/api/categories` | Sí | — | array de categorías |
| POST | `/api/categories` | Sí | `name,color,type` | categoría |
| PUT | `/api/categories/:id` | Sí | `name,color,type` | categoría |
| DELETE | `/api/categories/:id` | Sí | — | `{ success:true }` |
| GET | `/api/cards` | Sí | — | array de tarjetas |
| POST | `/api/cards` | Sí | `name,color,last4` | tarjeta |
| PUT | `/api/cards/:id` | Sí | `name,color,last4` | tarjeta |
| DELETE | `/api/cards/:id` | Sí | — | `{ success:true }` |
| GET | `/api/transactions` | Sí | — | array con `Category` y `Card` |
| POST | `/api/transactions` | Sí | `type,description,categoryId,amount,date,paymentMethod,cardId` | transacción con relaciones |
| PUT | `/api/transactions/:id` | Sí | mismos campos | transacción con relaciones |
| DELETE | `/api/transactions/:id` | Sí | — | `{ success:true }` |
| GET | `/api/budgets` | Sí | — | array de presupuestos |
| POST | `/api/budgets` | Sí | `month,year,amount` | presupuesto |
| PUT | `/api/budgets/:id` | Sí | `month,year,amount` | presupuesto |
| DELETE | `/api/budgets/:id` | Sí | — | `{ success:true }` |
| GET | `/api/stats/summary?period=YYYY-MM|all` | Sí | — | métricas del período |
| GET | `/api/stats/export?period=YYYY-MM|all` | Sí | — | archivo `.xlsx` |

---

## Errores y seguridad
- Todos los endpoints atrapados devuelven `500` con `{ message: 'Server error' }` en excepciones.
- Endpoints protegidos verifican pertenencia de recursos con `UserId`.
- JWT expira en 7 días; renueva sesión con `login`.

## Buenas prácticas
- No registrar claves ni tokens.
- Validar entradas en cliente (campos requeridos) y servidor.
- Mantener `amount` como número positivo; `date` en formato `YYYY-MM-DD`.

---

## Validaciones por endpoint

### /api/auth

| Campo | Tipo | Requerido | Valores/Formato | Notas |
|---|---|---|---|---|
| name (register) | string | sí | texto | se almacena tal cual |
| email (register/login/reset) | string | sí | email válido | único en DB (register) |
| password (register/login/reset) | string | sí | texto | se cifra con bcrypt |

### /api/categories

| Campo | Tipo | Requerido | Valores/Formato | Notas |
|---|---|---|---|---|
| name | string | sí | texto | nombre visible en UI |
| color | string | sí | `#RRGGBB` recomendado | no se valida formato en backend |
| type | enum | sí | `expense`/`income` | determina el agrupamiento en reportes |

### /api/cards

| Campo | Tipo | Requerido | Valores/Formato | Notas |
|---|---|---|---|---|
| name | string | sí | texto | identifica la tarjeta |
| color | string | sí | `#RRGGBB` recomendado | usado para distinguir en UI |
| last4 | string | sí | `0000` recomendado | no se valida que sean 4 dígitos |

### /api/transactions

| Campo | Tipo | Requerido | Valores/Formato | Notas |
|---|---|---|---|---|
| type | enum | sí | `expense`/`income` | impacta en totales y balance |
| description | string | sí | texto | detalle del movimiento |
| amount | decimal(10,2) | sí | número positivo | mostrado con dos decimales |
| date | date | sí | `YYYY-MM-DD` | se usa para filtros y series |
| paymentMethod | enum | sí (gastos) | `cash`/`card` | para ingresos normalmente no aplica |
| categoryId | int | opcional | id válido o `null` | si falta, transacción queda “Uncategorized” |
| cardId | int | opcional | id válido o `null` | recomendado si `paymentMethod = 'card'` |

### /api/budgets

| Campo | Tipo | Requerido | Valores/Formato | Notas |
|---|---|---|---|---|
| month | int | sí | 1–12 recomendado | no se valida rango en backend |
| year | int | sí | año (p.ej. 2025) | |
| amount | decimal(10,2) | sí | número positivo | comparado contra gastos del mes |

### Reglas transversales
- `UserId` se asigna desde el JWT y filtra todos los recursos.
- En actualizaciones, el backend permite `CategoryId` y `CardId` `null` para dejar el campo vacío.

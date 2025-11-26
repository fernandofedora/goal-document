# Base de datos

## Esquema general
```mermaid
erDiagram
  User ||--o{ Category : owns
  User ||--o{ Card : owns
  User ||--o{ Transaction : owns
  User ||--o{ Budget : owns
  Category ||--o{ Transaction : categorizes
  Card ||--o{ Transaction : pays
  User {
    int id PK
    string name
    string email
    string passwordHash
  }
  Category {
    int id PK
    string name
    string color
    string type
    int UserId
  }
  Card {
    int id PK
    string name
    string color
    string last4
    int UserId
  }
  Transaction {
    int id PK
    string type
    string description
    float amount
    date date
    string paymentMethod
    int UserId
    int CategoryId
    int CardId
  }
  Budget {
    int id PK
    int month
    int year
    float amount
    int UserId
  }
```

## Tablas y campos
- `User`
  - `email` único; `passwordHash` con bcrypt.
- `Category`
  - `type`: separa categorías de gastos e ingresos.
  - `color`: usado para gráficos y chips en UI.
- `Card`
  - `last4`: últimos 4 dígitos; `color` para identificación visual.
- `Transaction`
  - `type`: `expense` o `income`.
  - `paymentMethod`: `cash` o `card`; si `card`, `CardId` puede ser requerido por UI.
  - `CategoryId` y `CardId` permiten `NULL`.
- `Budget`
  - Por mes/año; un usuario puede tener varios presupuestos históricos.

## Relaciones y consideraciones
- Todas las tablas (excepto `User`) tienen `UserId` para asegurar propiedad.
- El cálculo de estadísticas filtra por fecha y tipo; las categorías en estadísticas se computan solo para gastos.
- Mantén `date` como `YYYY-MM-DD` para simplificar filtros por rango.

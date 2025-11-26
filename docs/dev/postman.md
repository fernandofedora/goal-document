# Colección Postman

## Introducción
- Importa esta colección para probar rápidamente el API.
- Usa variables `base_url` y `token` para no repetir valores.

## Variables
- `base_url`: `http://localhost:4000/api`
- `token`: `Bearer <jwt>` obtenido desde `POST /api/auth/login`.

## Importar
- Copia el JSON y en Postman: "Import" → "Raw text" → pega el contenido.

## Colección (JSON)
```json
{
  "info": {
    "name": "goals-system API",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Auth",
      "item": [
        {
          "name": "Login",
          "request": {
            "method": "POST",
            "header": [ { "key": "Content-Type", "value": "application/json" } ],
            "url": { "raw": "{{base_url}}/auth/login", "host": ["{{base_url}}"], "path": ["auth","login"] },
            "body": { "mode": "raw", "raw": "{\n  \"email\": \"ana@example.com\",\n  \"password\": \"secret123\"\n}" }
          }
        }
      ]
    },
    {
      "name": "Categories",
      "item": [
        {
          "name": "List",
          "request": {
            "method": "GET",
            "header": [ { "key": "Authorization", "value": "{{token}}" } ],
            "url": { "raw": "{{base_url}}/categories", "host": ["{{base_url}}"], "path": ["categories"] }
          }
        },
        {
          "name": "Create",
          "request": {
            "method": "POST",
            "header": [ { "key": "Authorization", "value": "{{token}}" }, { "key": "Content-Type", "value": "application/json" } ],
            "url": { "raw": "{{base_url}}/categories", "host": ["{{base_url}}"], "path": ["categories"] },
            "body": { "mode": "raw", "raw": "{\n  \"name\": \"Transporte\",\n  \"color\": \"#f59e0b\",\n  \"type\": \"expense\"\n}" }
          }
        },
        {
          "name": "Update",
          "request": {
            "method": "PUT",
            "header": [ { "key": "Authorization", "value": "{{token}}" }, { "key": "Content-Type", "value": "application/json" } ],
            "url": { "raw": "{{base_url}}/categories/{{category_id}}", "host": ["{{base_url}}"], "path": ["categories","{{category_id}}"] },
            "body": { "mode": "raw", "raw": "{\n  \"name\": \"Transporte Urbano\",\n  \"color\": \"#f59e0b\",\n  \"type\": \"expense\"\n}" }
          }
        },
        {
          "name": "Delete",
          "request": {
            "method": "DELETE",
            "header": [ { "key": "Authorization", "value": "{{token}}" } ],
            "url": { "raw": "{{base_url}}/categories/{{category_id}}", "host": ["{{base_url}}"], "path": ["categories","{{category_id}}"] }
          }
        }
      ]
    },
    {
      "name": "Cards",
      "item": [
        {
          "name": "List",
          "request": {
            "method": "GET",
            "header": [ { "key": "Authorization", "value": "{{token}}" } ],
            "url": { "raw": "{{base_url}}/cards", "host": ["{{base_url}}"], "path": ["cards"] }
          }
        },
        {
          "name": "Create",
          "request": {
            "method": "POST",
            "header": [ { "key": "Authorization", "value": "{{token}}" }, { "key": "Content-Type", "value": "application/json" } ],
            "url": { "raw": "{{base_url}}/cards", "host": ["{{base_url}}"], "path": ["cards"] },
            "body": { "mode": "raw", "raw": "{\n  \"name\": \"Visa Personal\",\n  \"color\": \"#0ea5e9\",\n  \"last4\": \"1234\"\n}" }
          }
        },
        {
          "name": "Update",
          "request": {
            "method": "PUT",
            "header": [ { "key": "Authorization", "value": "{{token}}" }, { "key": "Content-Type", "value": "application/json" } ],
            "url": { "raw": "{{base_url}}/cards/{{card_id}}", "host": ["{{base_url}}"], "path": ["cards","{{card_id}}"] },
            "body": { "mode": "raw", "raw": "{\n  \"name\": \"Visa Personal\",\n  \"color\": \"#0ea5e9\",\n  \"last4\": \"5678\"\n}" }
          }
        },
        {
          "name": "Delete",
          "request": {
            "method": "DELETE",
            "header": [ { "key": "Authorization", "value": "{{token}}" } ],
            "url": { "raw": "{{base_url}}/cards/{{card_id}}", "host": ["{{base_url}}"], "path": ["cards","{{card_id}}"] }
          }
        }
      ]
    },
    {
      "name": "Transactions",
      "item": [
        {
          "name": "List",
          "request": {
            "method": "GET",
            "header": [ { "key": "Authorization", "value": "{{token}}" } ],
            "url": { "raw": "{{base_url}}/transactions", "host": ["{{base_url}}"], "path": ["transactions"] }
          }
        },
        {
          "name": "Create",
          "request": {
            "method": "POST",
            "header": [ { "key": "Authorization", "value": "{{token}}" }, { "key": "Content-Type", "value": "application/json" } ],
            "url": { "raw": "{{base_url}}/transactions", "host": ["{{base_url}}"], "path": ["transactions"] },
            "body": { "mode": "raw", "raw": "{\n  \"type\": \"expense\",\n  \"description\": \"Taxi\",\n  \"categoryId\": {{category_id}},\n  \"amount\": \"8.50\",\n  \"date\": \"2025-11-25\",\n  \"paymentMethod\": \"cash\",\n  \"cardId\": null\n}" }
          }
        },
        {
          "name": "Update",
          "request": {
            "method": "PUT",
            "header": [ { "key": "Authorization", "value": "{{token}}" }, { "key": "Content-Type", "value": "application/json" } ],
            "url": { "raw": "{{base_url}}/transactions/{{transaction_id}}", "host": ["{{base_url}}"], "path": ["transactions","{{transaction_id}}"] },
            "body": { "mode": "raw", "raw": "{\n  \"type\": \"expense\",\n  \"description\": \"Taxi nocturno\",\n  \"categoryId\": {{category_id}},\n  \"amount\": \"9.75\",\n  \"date\": \"2025-11-25\",\n  \"paymentMethod\": \"cash\",\n  \"cardId\": null\n}" }
          }
        },
        {
          "name": "Delete",
          "request": {
            "method": "DELETE",
            "header": [ { "key": "Authorization", "value": "{{token}}" } ],
            "url": { "raw": "{{base_url}}/transactions/{{transaction_id}}", "host": ["{{base_url}}"], "path": ["transactions","{{transaction_id}}"] }
          }
        }
      ]
    },
    {
      "name": "Budgets",
      "item": [
        {
          "name": "List",
          "request": {
            "method": "GET",
            "header": [ { "key": "Authorization", "value": "{{token}}" } ],
            "url": { "raw": "{{base_url}}/budgets", "host": ["{{base_url}}"], "path": ["budgets"] }
          }
        },
        {
          "name": "Create",
          "request": {
            "method": "POST",
            "header": [ { "key": "Authorization", "value": "{{token}}" }, { "key": "Content-Type", "value": "application/json" } ],
            "url": { "raw": "{{base_url}}/budgets", "host": ["{{base_url}}"], "path": ["budgets"] },
            "body": { "mode": "raw", "raw": "{\n  \"month\": 11,\n  \"year\": 2025,\n  \"amount\": \"500.00\"\n}" }
          }
        },
        {
          "name": "Update",
          "request": {
            "method": "PUT",
            "header": [ { "key": "Authorization", "value": "{{token}}" }, { "key": "Content-Type", "value": "application/json" } ],
            "url": { "raw": "{{base_url}}/budgets/{{budget_id}}", "host": ["{{base_url}}"], "path": ["budgets","{{budget_id}}"] },
            "body": { "mode": "raw", "raw": "{\n  \"month\": 11,\n  \"year\": 2025,\n  \"amount\": \"650.00\"\n}" }
          }
        },
        {
          "name": "Delete",
          "request": {
            "method": "DELETE",
            "header": [ { "key": "Authorization", "value": "{{token}}" } ],
            "url": { "raw": "{{base_url}}/budgets/{{budget_id}}", "host": ["{{base_url}}"], "path": ["budgets","{{budget_id}}"] }
          }
        }
      ]
    },
    {
      "name": "Stats",
      "item": [
        {
          "name": "Summary",
          "request": {
            "method": "GET",
            "header": [ { "key": "Authorization", "value": "{{token}}" } ],
            "url": { "raw": "{{base_url}}/stats/summary?period={{period}}", "host": ["{{base_url}}"], "path": ["stats","summary"], "query": [ { "key": "period", "value": "{{period}}" } ] }
          }
        },
        {
          "name": "Export",
          "request": {
            "method": "GET",
            "header": [ { "key": "Authorization", "value": "{{token}}" } ],
            "url": { "raw": "{{base_url}}/stats/export?period={{period}}", "host": ["{{base_url}}"], "path": ["stats","export"], "query": [ { "key": "period", "value": "{{period}}" } ] }
          }
        }
      ]
    }
  ],
  "variable": [
    { "key": "base_url", "value": "http://localhost:4000/api" },
    { "key": "token", "value": "Bearer <jwt>" },
    { "key": "period", "value": "2025-11" },
    { "key": "category_id", "value": "12" },
    { "key": "card_id", "value": "5" },
    { "key": "transaction_id", "value": "101" },
    { "key": "budget_id", "value": "3" }
  ]
}
```

## Consejos
- Guarda el `token` de la respuesta de Login en la variable `token`.
- Crea más items: `categories/cards/budgets` CRUD, `stats/export`.
 - Puedes usar Tests en la petición de Login para guardar el token automáticamente:
   ```js
   // En la pestaña Tests de Postman
   const json = pm.response.json();
   if (json.token) pm.collectionVariables.set('token', `Bearer ${json.token}`);
   ```

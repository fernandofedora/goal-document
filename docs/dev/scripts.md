# Scripts de prueba

## Bash (macOS/Linux)
```bash
# Login y capturar token
TOKEN=$(curl -s -X POST http://localhost:4000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"ana@example.com","password":"secret123"}' | jq -r .token)

# Crear categoría
curl -s -X POST http://localhost:4000/api/categories \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name":"Transporte","color":"#f59e0b","type":"expense"}' | jq

# Crear transacción (gasto efectivo)
curl -s -X POST http://localhost:4000/api/transactions \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"type":"expense","description":"Taxi","categoryId":12,"amount":"8.50","date":"2025-11-25","paymentMethod":"cash","cardId":null}' | jq

# Resumen del mes
curl -s -G http://localhost:4000/api/stats/summary \
  -H "Authorization: Bearer $TOKEN" \
  --data-urlencode period=2025-11 | jq
```

## PowerShell (Windows)
```powershell
# Login y capturar token
$loginBody = @{ email = 'ana@example.com'; password = 'secret123' } | ConvertTo-Json
$loginRes = Invoke-RestMethod -Method Post -Uri 'http://localhost:4000/api/auth/login' -ContentType 'application/json' -Body $loginBody
$TOKEN = $loginRes.token

# Crear categoría
$catBody = @{ name = 'Transporte'; color = '#f59e0b'; type = 'expense' } | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri 'http://localhost:4000/api/categories' -ContentType 'application/json' -Headers @{ Authorization = "Bearer $TOKEN" } -Body $catBody

# Crear transacción
$txBody = @{ type = 'expense'; description = 'Taxi'; categoryId = 12; amount = '8.50'; date = '2025-11-25'; paymentMethod = 'cash'; cardId = $null } | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri 'http://localhost:4000/api/transactions' -ContentType 'application/json' -Headers @{ Authorization = "Bearer $TOKEN" } -Body $txBody

# Resumen del mes
Invoke-RestMethod -Method Get -Uri 'http://localhost:4000/api/stats/summary?period=2025-11' -Headers @{ Authorization = "Bearer $TOKEN" }
```

## Notas
- Requiere tener usuarios y credenciales válidas.
- Ajusta `categoryId` y fechas según tus datos.

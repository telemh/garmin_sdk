# Garmin Health API — Version 1.2.0

Документация по **Garmin Connect Health API** для интеграции данных о здоровье и активности пользователей.  

Health API позволяет получать данные после согласия пользователя и использовать их в корпоративных wellness-программах, системах мониторинга пациентов и популяционных исследованиях.

---

## 📌 Возможности

- Данные о здоровье (сон, активность, стресс, пульс, дыхание, SpO₂, давление, температура кожи и др.).  
- Поддержка **Ping** и **Push** уведомлений.  
- Историческая выгрузка через **Backfill**.  
- Веб-инструменты для отладки и проверки интеграции.  

---

## ⚙️ Конфигурация

Конфигурация выполняется через [Endpoint Configuration Tool](https://apis.garmin.com/tools/endpoints/).  
- Каждый тип сводок настраивается на свой HTTPS endpoint.  
- Поддержка режима **On Hold** для обслуживания (данные сохраняются до 7 дней).  

---

## 🔔 Ping Service

**Назначение**: уведомляет о новых данных без передачи самих данных. Уведомление содержит `callbackURL` для запроса.  

### Workflow
1. Garmin отправляет POST на ваш endpoint.  
2. Вы возвращаете `HTTP 200 OK`.  
3. Асинхронно запрашиваете данные по `callbackURL`.  

### Пример Ping-запроса

```bash
curl -v -X POST -H "Content-Type: application/json" -d '{
  "epochs": [{
    "userId": "4aacafe82427c251df9c9592d0c06768",
    "callbackURL": "https://apis.garmin.com/wellness-api/rest/epochs?uploadStartTimeInSeconds=1444937651&uploadEndTimeInSeconds=1444937902"
  }]
}' http://localhost:8080/garmin/ping
```

### Минимальный ответ Ping
```json
{
  "epochs": [{
    "userId": "4aacafe82427c251df9c9592d0c06768",
    "callbackURL": "https://apis.garmin.com/wellness-api/rest/epochs?uploadStartTimeInSeconds=1444937651&uploadEndTimeInSeconds=1444937902"
  }]
}
```

---

## 📡 Push Service

**Назначение**: отправляет данные напрямую в POST-запросе.  

### Пример Push-уведомления
```json
{
  "epochs": [{
    "userId": "4aacafe82427c251df9c9592d0c06768",
    "summaryId": "x153a9f3-5a9478d4-6",
    "activityType": "WALKING",
    "steps": 93,
    "distanceInMeters": 49.11,
    "durationInSeconds": 840
  }]
}
```

### Минимальный ответ сервера
```http
HTTP/1.1 200 OK
```

---

## 📊 Summary Endpoints

### 1. Daily Summaries
**URL:**  
```http
GET https://apis.garmin.com/wellness-api/rest/dailies
```

**Пример запроса:**  
```bash
curl -X GET "https://apis.garmin.com/wellness-api/rest/dailies?uploadStartTimeInSeconds=1452470400&uploadEndTimeInSeconds=1452556800"   -H "Authorization: Bearer <ACCESS_TOKEN>"
```

**Минимальный ответ:**  
```json
[{
  "summaryId": "EXAMPLE_67891",
  "calendarDate": "2025-09-01",
  "steps": 4210,
  "activeKilocalories": 321,
  "distanceInMeters": 3146.5
}]
```

---

### 2. Epoch Summaries
**URL:**  
```http
GET https://apis.garmin.com/wellness-api/rest/epochs
```

**Пример запроса:**  
```bash
curl -X GET "https://apis.garmin.com/wellness-api/rest/epochs?uploadStartTimeInSeconds=1454418900&uploadEndTimeInSeconds=1454422800"   -H "Authorization: Bearer <ACCESS_TOKEN>"
```

**Минимальный ответ:**  
```json
[{
  "summaryId": "EXAMPLE_1234",
  "activityType": "RUNNING",
  "steps": 427,
  "durationInSeconds": 900
}]
```

---

### 3. Sleep Summaries
**URL:**  
```http
GET https://apis.garmin.com/wellness-api/rest/sleeps
```

**Пример запроса:**  
```bash
curl -X GET "https://apis.garmin.com/wellness-api/rest/sleeps?uploadStartTimeInSeconds=1611840660&uploadEndTimeInSeconds=1611850660"   -H "Authorization: Bearer <ACCESS_TOKEN>"
```

**Минимальный ответ:**  
```json
[{
  "summaryId": "EXAMPLE_567890",
  "calendarDate": "2025-09-01",
  "durationInSeconds": 15264,
  "deepSleepDurationInSeconds": 5000,
  "lightSleepDurationInSeconds": 7000,
  "remSleepInSeconds": 2000
}]
```

---

### 4. Body Composition
```http
GET https://apis.garmin.com/wellness-api/rest/bodyComps
```
**Минимальный ответ:**
```json
[{
  "summaryId": "EXAMPLE_678901",
  "weightInGrams": 75450,
  "bodyMassIndex": 23.2,
  "bodyFatInPercent": 17.1
}]
```

---

### 5. Stress Details
```http
GET https://apis.garmin.com/wellness-api/rest/stressDetails
```
**Минимальный ответ:**
```json
[{
  "summaryId": "EXAMPLE_6789124",
  "calendarDate": "2025-09-01",
  "timeOffsetStressLevelValues": { "0": 18, "180": 51 }
}]
```

---

### 6. User Metrics
```http
GET https://apis.garmin.com/wellness-api/rest/userMetrics
```
**Минимальный ответ:**
```json
[{
  "summaryId": "EXAMPLE_843244",
  "calendarDate": "2025-09-01",
  "vo2Max": 48.0,
  "fitnessAge": 32
}]
```

---

### 7. Pulse Ox
```http
GET https://apis.garmin.com/wellness-api/rest/pulseOx
```
**Минимальный ответ:**
```json
[{
  "summaryId": "Example1234",
  "calendarDate": "2025-09-01",
  "timeOffsetSpo2Values": { "7140": 94, "10740": 98 }
}]
```

---

### 8. Respiration
```http
GET https://apis.garmin.com/wellness-api/rest/respiration
```
**Минимальный ответ:**
```json
[{
  "summaryId": "x15372ea-5d7866b4",
  "startTimeInSeconds": 1568171700,
  "timeOffsetEpochToBreaths": { "0": 14, "60": 15 }
}]
```

---

### 9. Blood Pressure
```http
GET https://apis.garmin.com/wellness-api/rest/bloodPressures
```
**Минимальный ответ:**
```json
[{
  "summaryId": "BP_EXAMPLE_1",
  "systolic": 120,
  "diastolic": 80
}]
```

---

### 10. Skin Temperature
```http
GET https://apis.garmin.com/wellness-api/rest/skinTemp
```
**Минимальный ответ:**
```json
[{
  "summaryId": "TEMP_EXAMPLE_1",
  "temperatureInCelsius": 36.7
}]
```

---

## 🛠 Веб-инструменты

- **Data Viewer** — просмотр данных.  
- **Backfill** — историческая выгрузка.  
- **Summary Resender** — пересоздание уведомлений.  
- **Data Generator** — генерация тестовых данных.  
- **Partner Verification** — проверка интеграции.  

---

## 🔑 Production Key

Условия получения:  
- Обработка только через Ping/Push.  
- Ответ на уведомления `HTTP 200`.  
- Минимум 2 реальных аккаунта Garmin.  
- Поддержка deregistration endpoint.  

---

## 📬 Поддержка

- Email: [connect-support@developer.garmin.com](mailto:connect-support@developer.garmin.com)  
- [Garmin Developer Portal](https://developer.garmin.com/)  

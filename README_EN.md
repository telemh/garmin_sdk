# Garmin Health API — Version 1.2.0

Documentation for **Garmin Connect Health API** integration with user health and activity data.  

The Health API provides access to health metrics after user consent and enables integration into corporate wellness platforms, patient monitoring systems, and population health studies.

---

## 📌 Features

- Access to health data: sleep, activity, stress, heart rate, respiration, SpO₂, blood pressure, skin temperature, and more.  
- Supports **Ping** and **Push** notifications.  
- Historical data retrieval with **Backfill**.  
- Web tools for debugging and partner verification.  

---

## ⚙️ Configuration

Configuration is managed via [Endpoint Configuration Tool](https://apis.garmin.com/tools/endpoints/).  
- Each summary type must be assigned to a valid HTTPS endpoint.  
- **On Hold** mode supported (stores notifications up to 7 days).  

---

## 🔔 Ping Service

**Purpose**: Notifies partners when new data is available. The notification includes a `callbackURL` for retrieval.  

### Workflow
1. Garmin sends a POST to your endpoint.  
2. You return `HTTP 200 OK`.  
3. Fetch the data asynchronously via the `callbackURL`.  

### Example Ping request

```bash
curl -v -X POST -H "Content-Type: application/json" -d '{
  "epochs": [{
    "userId": "4aacafe82427c251df9c9592d0c06768",
    "callbackURL": "https://apis.garmin.com/wellness-api/rest/epochs?uploadStartTimeInSeconds=1444937651&uploadEndTimeInSeconds=1444937902"
  }]
}' http://localhost:8080/garmin/ping
```

### Minimal Ping response
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

**Purpose**: Sends updated summary data directly in the POST body.  

### Example Push payload
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

### Minimal server response
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

**Example request:**  
```bash
curl -X GET "https://apis.garmin.com/wellness-api/rest/dailies?uploadStartTimeInSeconds=1452470400&uploadEndTimeInSeconds=1452556800"   -H "Authorization: Bearer <ACCESS_TOKEN>"
```

**Minimal response:**  
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
```http
GET https://apis.garmin.com/wellness-api/rest/epochs
```
**Minimal response:**
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
```http
GET https://apis.garmin.com/wellness-api/rest/sleeps
```
**Minimal response:**
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
**Minimal response:**
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
**Minimal response:**
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
**Minimal response:**
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
**Minimal response:**
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
**Minimal response:**
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
**Minimal response:**
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
**Minimal response:**
```json
[{
  "summaryId": "TEMP_EXAMPLE_1",
  "temperatureInCelsius": 36.7
}]
```

---

## 🛠 Web Tools

- **Data Viewer** — view user summaries.  
- **Backfill** — request historical data.  
- **Summary Resender** — regenerate notifications.  
- **Data Generator** — generate simulated data.  
- **Partner Verification** — check readiness before Production key.  

---

## 🔑 Production Key

Requirements:  
- Only use Ping/Push for data retrieval.  
- Must return `HTTP 200` to notifications.  
- Integration tested with at least 2 Garmin accounts.  
- Deregistration endpoint enabled.  

---

## 📬 Support

- Email: [connect-support@developer.garmin.com](mailto:connect-support@developer.garmin.com)  
- [Garmin Developer Portal](https://developer.garmin.com/)  

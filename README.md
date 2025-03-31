# 🔒 candle-auth  
**Enterprise JWT-Authentifizierung mit Spring Boot**  
![Java](https://img.shields.io/badge/Java-17%2B-blue)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.1-green)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-blue)

---

## 📌 Kernfeatures
- ✅ Sichere JWT-Authentifizierung (Access + Refresh Tokens)
- 🔐 Rollenbasierte Zugriffskontrolle (USER/ADMIN)
- ⚡ Automatische Token-Erneuerung
- 🛡️ Rate-Limiting & Security Hardening
- 📊 Spring Boot Actuator Monitoring

---

## 🛠️ Technologien
| Bereich           | Technologie               |
|-------------------|---------------------------|
| Framework         | Spring Boot 3.1           |
| Sicherheit        | Spring Security 6 + JJWT  |
| Datenbank         | PostgreSQL 15             |
| API-Dokumentation | SpringDoc OpenAPI 3       |
| Testing           | JUnit 5, Testcontainers   |

---

## ⚙️ Konfiguration
### application.yml
```yaml
server:
  port: 8080

spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/jwtauth
    username: admin
    password: secret

jwt:
  secret: "${JWT_SECRET}"  # Env-Variable
  access-token-expiration: 15m
  refresh-token-expiration: 7d
```

---

## 🚀 Schnellstart
1. **Umgebungsvariablen setzen**:
   ```bash
   export JWT_SECRET="mein_geheimer_schlüssel"
   ```

2. **Datenbank starten**:
   ```bash
   docker-compose up -d
   ```

3. **Anwendung builden**:
   ```bash
   ./gradlew bootRun
   ```

---

## 📂 Projektstruktur
```
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── candleauth/
│   │           ├── config/       # SecurityConfig, WebConfig
│   │           ├── controller/   # AuthController, UserController
│   │           ├── dto/          # Request/Response DTOs
│   │           ├── entity/       # User, Role
│   │           ├── repository/   # JPA Repositories
│   │           ├── service/      # AuthService, UserService
│   │           └── CandleAuthApplication.java
│   └── resources/
│       ├── application.yml
│       └── db/migration/         # Flyway-Skripte
```

---

## 🔍 API-Endpunkte
| Endpoint               | Methode | Beschreibung                |
|------------------------|---------|-----------------------------|
| `/api/v1/auth/signup`  | POST    | Neuen Benutzer registrieren |
| `/api/v1/auth/login`   | POST    | JWT-Token erhalten         |
| `/api/v1/auth/refresh` | POST    | Token aktualisieren        |

---

## 🐳 Docker Deployment
```bash
docker build -t candle-auth .
docker run -p 8080:8080 -e JWT_SECRET=geheim candle-auth
```

---

## 📜 Lizenz
MIT License | Copyright © 2015 midorlo

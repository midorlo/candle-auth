# 🔥 candle-auth  

**Enterprise JWT-Authentifizierung mit Spring Boot**  
*„Sichere, skalierbare Identity-Lösung für moderne Anwendungen“*

---

## 📜 Inhaltsverzeichnis
1. [Technologien](#-technologien)  
2. [Features](#-features)  
3. [Architektur](#-architektur)  
4. [Sicherheit](#-sicherheit)  
5. [Deployment](#-deployment)  
6. [Testing](#-testing)  

---

## 🛠️ Technologien
**Core Stack:**
- **Spring Boot 3** (+ Web, Security, Data JPA, Validation, Actuator)
- **PostgreSQL 15** (mit Flyway für Migrationen)
- **JJWT 0.12.3** (JWT-Implementierung)

**Dev-Tools:**
- Lombok  
- MapStruct  
- Bucket4j (Rate Limiting)  
- Testcontainers  
- ArchUnit  

---

## 🚀 Features
### 1. Benutzerverwaltung
```java
@Entity
public class User {
    @Id @GeneratedValue
    private Long id;
    private String username;
    private String email;
    private String password; // BCrypt-gehasht
    @Enumerated(EnumType.STRING)
    private Role role; // USER, ADMIN
}
```

### 2. JWT-Authentifizierung
```yaml
# application.yml
jwt:
  secret: "${JWT_SECRET}" 
  access-token-expiration: 15m
  refresh-token-expiration: 7d
```

### 3. Sicherheit
- 🔒 CSRF- & CORS-Schutz  
- 🛡️ Rate Limiting (10 Requests/Minute)  
- 📜 OWASP-konforme Passwortrichtlinien  

---

## 🏗️ Architektur
### Schichtenmodell
```
📁 com.candleauth
├── 📂 config        → SecurityConfig, WebConfig
├── 📂 controller    → AuthController (REST-Endpoints)
├── 📂 service       → AuthService, JwtService
├── 📂 repository    → UserRepository (JPA)
└── 📂 entity        → User, Role
```

### ArchUnit-Regeln
```java
@ArchTest
static final ArchRule layer_dependencies = 
    layeredArchitecture()
        .layer("Controller").definedBy("..controller..")
        .layer("Service").definedBy("..service..")
        .whereLayer("Controller").mayOnlyBeAccessedByLayers("Service");
```

---

## 🔐 Sicherheit
### SecurityConfig
```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    http
        .csrf(AbstractHttpConfigurer::disable)
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/api/public/**").permitAll()
            .requestMatchers("/api/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated()
        )
        .addFilterBefore(jwtAuthFilter(), UsernamePasswordAuthenticationFilter.class);
    return http.build();
}
```

### Rate Limiting
```java
@Bean
public Bucket bucket() {
    return Bucket.builder()
        .addLimit(limit -> limit.capacity(10).refillIntervally(10, Duration.ofMinutes(1)))
        .build();
}
```

---

## 🐳 Deployment
### Docker-Compose
```yaml
version: "3.8"
services:
  app:
    image: candle-auth:latest
    ports: ["8080:8080"]
    env_file: .env
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    volumes:
      - pg_data:/var/lib/postgresql/data
```

### Kubernetes (Auszug)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: candle-auth
spec:
  replicas: 3
  template:
    spec:
      containers:
        - name: app
          image: candle-auth:latest
          envFrom:
            - secretRef:
                name: jwt-secrets
```

---

## 🔍 Testing
| Testtyp               | Tools                      | Beispiel-Coverage          |
|-----------------------|----------------------------|----------------------------|
| **Unit-Tests**        | JUnit 5 + Mockito          | Service-Logik              |
| **Integration**       | Testcontainers + SpringBootTest | REST-APIs + DB            |
| **Sicherheit**        | OWASP ZAP                  | Penetrationstests          |
| **Architektur**       | ArchUnit                   | Schichtentrennung          |

---

## 📜 Lizenz
MIT License | Copyright © 2025 Midorlo  
*„Frei für den Einsatz in kommerziellen Projekten“*

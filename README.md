# candle-auth

JWT-Authentifizierungssystem mit Spring Boot

## **1. Projekt-Setup & Abhängigkeiten**
**Framework:** Spring Boot  
**Datenbank:** PostgreSQL/MySQL (mit JPA/Hibernate)  
**Security:** Spring Security, JWT (JJWT oder Nimbus)  
**Sonstige Tools:** Lombok, MapStruct, Flyway/Liquibase, Testcontainers, ArchUnit

### **Benötigte Abhängigkeiten:**
- `spring-boot-starter-security` (Spring Security)
- `spring-boot-starter-web` (REST API)
- `spring-boot-starter-data-jpa` (Datenbankverwaltung)
- `spring-boot-starter-validation` (Eingabevalidierung)
- `jjwt` oder `nimbus-jose-jwt` (JWT-Handling)
- `lombok` (Reduziert Boilerplate-Code)
- `mapstruct` (DTO-Mapping)
- `testcontainers` (DB-Integrationstests)
- `archunit` (Architekturtests)
- `bucket4j` (Rate Limiting)

---
## **2. Benutzer-Management & Registrierung**
- **User-Entity**:
    - Benutzername, E-Mail, Passwort (gehasht mit BCrypt)
    - Rollen (`USER`, `ADMIN`) für Zugriffskontrolle
    - Optional: E-Mail-Verifizierung

- **Registrierungs-Endpoint:**
    - Speicherung neuer Nutzer mit Validierung
    - Schutz vor doppelten Accounts

- **Tests:**
    - Unit-Tests: UserServiceTest (Speicherung & Hashing)
    - Integrationstest: UserControllerTest (API-Registrierung)

---
## **3. Authentifizierung & JWT-Handling**
- **Login-Endpoint:**
    - Nutzer-Authentifizierung mit `UserDetailsService`
    - Erstellung von JWT (Access- & Refresh-Token)

- **JWT-Service:**
    - Generierung, Validierung & Parsing von Tokens
    - Token-Signierung mit `HS256`
    - Refresh-Token-Mechanismus

- **Tests:**
    - Unit-Tests: JwtServiceTest, AuthServiceTest (Token-Logik)
    - Integrationstest: AuthControllerTest (API-Login & Token-Refresh)

---
## **4. Sicherheitskonfiguration & Endpoint-Schutz**
- **Spring Security Konfiguration:**
    - `SecurityFilterChain` zur Konfiguration von Endpunkten
    - Geschützte Endpunkte: Nur authentifizierte Nutzer
    - Rollen- & Berechtigungsmanagement
    - CORS & CSRF Schutz

- **Rate Limiting mit Bucket4j:**
    - Schutz gegen Brute-Force-Angriffe

- **Tests:**
    - Unit-Tests: SecurityConfigTest (Rollenbasierter Zugriff)
    - Lasttests: RateLimitingTest (DDoS-Schutz)

---
## **5. Logout & Token-Invalidierung**
- **Logout-Mechanismus:**
    - Möglichkeit, Refresh-Tokens ungültig zu machen
    - Blacklist oder Token Store für aktive Sessions

- **Tests:**
    - Unit-Tests: LogoutServiceTest (Token-Invalidierung)
    - Integrationstest: LogoutControllerTest (API-Logout)

---
## **6. Architektur- & Sicherheits-Tests**
- **Architektur-Tests mit ArchUnit:**
    - Controller dürfen nur Services nutzen, keine Repositories
    - Namenskonventionen (`*Controller`, `*Service`, `*Repository`)
    - Keine Spring-Security-Logik in Entities oder Repositories

- **Sicherheits-Tests mit OWASP ZAP:**
    - Automatisierte Sicherheitsanalyse gegen API-Schwachstellen

- **Tests:**
    - ArchUnit-Tests: ArchitectureTest (Schichtentrennung)
    - Penetrationstests: Manuelle & automatisierte Sicherheitsprüfung

---
## **7. Logging, Monitoring & Deployment**
- **Logging & Überwachung:**
    - Spring Boot Actuator zur API-Analyse
    - Audit-Logging für sicherheitskritische Events

- **Deployment-Strategie:**
    - Docker & Kubernetes für skalierbare Bereitstellung
    - Nutzung eines Secret Managers für JWT-Schlüssel

### **Docker-Setup für Deployment**
#### 📌 Dockerfile für Spring Boot:
```dockerfile
FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY target/JwtAuthSpring.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

#### 📌 `docker-compose.yml` für lokale Umgebung:
```yaml
version: "3.8"
services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/jwtauth
      - SPRING_DATASOURCE_USERNAME=admin
      - SPRING_DATASOURCE_PASSWORD=secret
    depends_on:
      - db

  db:
    image: postgres:15
    restart: always
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: jwtauth
    ports:
      - "5432:5432"
```

---
## **8. Versionierungsstrategie**
### **API-Versionierung**
- `GET /api/v1/auth/login` (v1)
- `GET /api/v2/auth/login` (v2 mit neuen Features)

### **Code-Versionierung**
- `main` → Stabile Produktionsversion
- `develop` → Aktuelle Entwicklungsbasis
- `feature/{name}` → Neue Features
- `hotfix/{name}` → Kritische Bugfixes
- `release/{version}` → Vorbereitete Releases

### **Semantische Versionierung (SemVer)**
- `MAJOR.MINOR.PATCH`

### **Datenbank-Versionierung (Flyway/Liquibase)**
- `V1.0.0__initial_schema.sql` → Erstes Schema
- `V1.1.0__add_refresh_token_table.sql` → Neues Feature
- `V1.1.1__fix_token_expiry.sql` → Bugfix




## Pregled mikrostoritev

Sistem je razdeljen na naslednje storitve:

-  `gateway-service`: Vstopna točka (reverse-proxy) za vse API klice.
-  `user-service`: Upravljanje uporabnikov in profilov.
-   `list-service`: Logika nakupovalnih seznamov in artiklov.
-   `recipe-service`: Uvoz receptov z OpenAI API in upravljanje kataloga.
-   `notification-service`: Obveščanje uporabnikov.

## Navodila za namestitev in zagon

```bash
cd shopsync-infra
docker compose up --build
```
## Uporabljeni porti in okoljske spremenljivke

| Storitev | Port (Host) | Spremenljivke (primer) |
|---------|-------------|------------------------|
| PostgreSQL (DB) | 5433 | `POSTGRES_USER`, `POSTGRES_PASSWORD` (baze `user_db`, `list_db`, `recipe_db` se ustvarijo preko inicializacijskega skripta v `docker-compose.yml`) |
| Zookeeper | 2181 | `ZOOKEEPER_CLIENT_PORT` |
| Kafka (broker) | 9092 | `KAFKA_ADVERTISED_LISTENERS`, `KAFKA_ZOOKEEPER_CONNECT`, aplikacije: `SPRING_KAFKA_BOOTSTRAP_SERVERS` |
| Gateway (`gateway-service`) | 8080 | `USER_SERVICE_URL`, `LIST_SERVICE_URL`, `RECIPE_SERVICE_URL`; Auth0 nastavitve v `application.properties` (`issuer-uri`, `audiences`) oz. CI secrets |
| User Service (`user-service`) | 8083 | `SPRING_DATASOURCE_URL`, `SPRING_KAFKA_BOOTSTRAP_SERVERS`; uporabniško ime/geslo za bazo sta v `application.properties` |
| List Service (`list-service`) | 8082 | `SPRING_DATASOURCE_URL`, `SPRING_KAFKA_BOOTSTRAP_SERVERS`; uporabniško ime/geslo za bazo sta v `application.properties` |
| Recipe Service (`recipe-service`) | 8084 | `SPRING_DATASOURCE_URL`, `SPRING_KAFKA_BOOTSTRAP_SERVERS`, `OPENAI_API_KEY`; uporabniško ime/geslo za bazo sta v `application.properties` |
| Notification Service (`notification-service`) | ni izpostavljeno | `SPRING_KAFKA_BOOTSTRAP_SERVERS` (v `docker-compose.yml`), ostala konfiguracija (port 8085, DB) v `application.properties` |
| Mobile App | na napravi | `BASE_URL` / API endpoint (kaže na gateway), `Authorization` token (JWT) |

Global / CI: glej `.env` v tem repozitoriju in GitHub Secrets — npr. `DOCKERHUB_USERNAME`, `OPENAI_API_KEY`, Auth0 nastavitve.

API dokumentacija:
- `API_DOCUMENTATION.md` — pregled vseh REST endpointov po mikrostoritvah (opis, metode, odgovori).
- `openapi.yaml` — OpenAPI 3 specifikacija za uporabo s Swagger UI / ReDoc ali generiranje klientov.

Opomba: za natančne nastavitve portov in spremenljivk preverite `docker-compose.yml` v tem repozitoriju in `src/main/resources/application.*` v posamezni storitvi.

## Poslovna logika
Poslovna logika je dokumentirana neposredno v izvorni kodi posameznih storitev (mapa `src/main/java/com/shopsync/`).
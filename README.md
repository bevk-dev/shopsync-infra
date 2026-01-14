# shopsync-infra

Kratek opis
- Infrastrukturni viri za lokalni razvoj in deploy: `docker-compose.yml` in `k8s/` mape z manifesti.
 - Centralna sistemska dokumentacija: `DOCUMENTATION.md` (pregled vseh mikrostoritev, portov in okolijskih spremenljivk).

Hitri zagon (Docker Compose)

```bash
cd shopsync-infra
docker compose up --build
```

Opombe za Kubernetes
- Manifeste najdete v `shopsync-infra/k8s/` (po storitvah + `shared`).
- Pred uporabo poskrbite, da imate image-e v registry, ki ga Kubernetes uporablja, ali pa uporabite lokalni build pristop (`minikube`/`kind` + lokalni registry).
- Postgres manifest pričakuje ConfigMap `postgres-init-config` z `init.sql` — ustvarite ga, če ni prisoten:

```bash
kubectl create configmap postgres-init-config --from-file=init.sql=./k8s/shared/init.sql -n <namespace>
```

Konfiguracija
- `docker-compose.yml` — host:container porte in env spremenljivke za lokalni razvoj.
- `k8s/` — per-service deploy+service manifesti.

Za podroben pregled vseh storitev, portov in okolijskih spremenljivk glej `DOCUMENTATION.md` v tem repozitoriju. Občutljive vrednosti (npr. `DOCKERHUB_USERNAME`, `OPENAI_API_KEY`) konfiguriraj preko lokalnega okolja ali GitHub Secrets, ne v repozitoriju.

API dokumentacija
- **Markdown**: `API_DOCUMENTATION.md` — pregledna dokumentacija vseh endpoint-ov po mikrostoritvah.
- **OpenAPI/Swagger**: `openapi.yaml` — OpenAPI 3.0 specifikacija za uporabo z Swagger UI, ReDoc ali generiranje klientov.
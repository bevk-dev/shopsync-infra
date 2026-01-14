## ShopSync API dokumentacija

Ta dokument opisuje glavne REST API endpoint-e posameznih mikrostoritev. Vsi klici gredo v praksi skozi `gateway-service` na naslovu `http://<gateway-host>:8080/api/...` in zahtevajo veljaven JWT (Auth0).

---

## User Service (`user-service`)

Osnovni path: `/api/users`

- **POST `/api/users/register`**  
  - **Opis**: Ustvari ali sinhronizira uporabnika na podlagi podatkov iz Auth0 žetona.  
  - **Auth**: Zahtevan Auth0 JWT (`Authorization: Bearer <token>`).  
  - **Body**: brez (podatki se preberejo iz JWT).  
  - **Odgovori**:
    - `201 Created` + `User` JSON
    - `400 Bad Request`, če JWT ne vsebuje e‑pošte.

- **GET `/api/users/me`**  
  - **Opis**: Vrne podatke o trenutno prijavljenemu uporabniku; po potrebi uporabnika tudi ustvari/sinhronizira.  
  - **Auth**: Zahtevan JWT.  
  - **Odgovori**:
    - `200 OK` + `User` JSON.

- **GET `/api/users/{email}`**  
  - **Opis**: Vrne uporabnika po e‑poštnem naslovu.  
  - **Path parametri**:  
    - `email` – e‑poštni naslov (dovoljena pika v path-u).  
  - **Odgovori**:
    - `200 OK` + `User` JSON
    - `404 Not Found`, če uporabnik ne obstaja.

- **PUT `/api/users/update-name`**  
  - **Opis**: Posodobi prikazno ime trenutno prijavljenega uporabnika.  
  - **Body**: plain string (npr. `"Novo Ime"`).  
  - **Odgovori**:
    - `200 OK` + posodobljen `User` JSON.

---

## List Service (`list-service`)

### Shopping seznami

Osnovni path: `/api/shopping-lists`

- **POST `/api/shopping-lists`**  
  - **Opis**: Ustvari nov nakupovalni seznam za prijavljenega uporabnika.  
  - **Body**: `ShoppingList` JSON (npr. ime seznama).  
  - **Odgovori**:
    - `200 OK` + ustvarjen `ShoppingList`.

- **GET `/api/shopping-lists`**  
  - **Opis**: Vrne vse sezname prijavljenega uporabnika.  
  - **Odgovori**:
    - `200 OK` + seznam `ShoppingList[]`.

- **DELETE `/api/shopping-lists/{listId}`**  
  - **Opis**: Izbriše nakupovalni seznam (če pripada prijavljenemu uporabniku).  
  - **Path parametri**:
    - `listId` – ID seznama.  
  - **Odgovori**:
    - `200 OK` + sporočilo o uspehu.

- **PATCH `/api/shopping-lists/{listId}/name`**  
  - **Opis**: Posodobi ime seznama.  
  - **Body**: plain string z novim imenom (npr. `"Tedenski nakup"`).  
  - **Odgovori**:
    - `200 OK` + posodobljen `ShoppingList`.

- **POST `/api/shopping-lists/{listId}/share`**  
  - **Opis**: Deli seznam z drugim uporabnikom.  
  - **Body**: JSON:
    ```json
    {
      "friendAuth0Id": "auth0|123..."
    }
    ```  
  - **Odgovori**:
    - `200 OK` + sporočilo o uspehu.

### Artikli na seznamu

- **POST `/api/shopping-lists/{listId}/items`**  
  - **Opis**: Doda artikel na izbrani seznam.  
  - **Body**: `Item` JSON.  
  - **Odgovori**:
    - `200 OK` + ustvarjen `Item`.

- **GET `/api/shopping-lists/{listId}/items`**  
  - **Opis**: Vrne vse artikle na seznamu.  
  - **Odgovori**:
    - `200 OK` + `Item[]`.

- **GET `/api/shopping-lists/{listId}/pending`**  
  - **Opis**: Vrne vse “nekupljene” artikle na seznamu.  
  - **Odgovori**:
    - `200 OK` + `Item[]`.

### Posodobitve / brisanje artiklov

Osnovni path: `/api/items`

- **PATCH `/api/items/{itemId}`**  
  - **Opis**: Posodobi lastnosti artikla (npr. ime, količino).  
  - **Body**: `Item` JSON z vrednostmi, ki se posodabljajo.  
  - **Odgovori**:
    - `200 OK` + posodobljen `Item`.

- **PATCH `/api/items/{itemId}/purchase`**  
  - **Opis**: Preklopi stanje “kupljeno” za artikel.  
  - **Odgovori**:
    - `200 OK` + posodobljen `Item`.

- **DELETE `/api/items/{itemId}`**  
  - **Opis**: Izbriše artikel iz seznama.  
  - **Odgovori**:
    - `200 OK` + sporočilo o uspehu.

### Product katalog

Osnovni path: `/api/products`

- **GET `/api/products`**  
  - **Opis**: Vrne vse izdelke uporabnika, opcijsko filtrirane po kategoriji.  
  - **Query parametri**:
    - `category` *(optional)* – ime kategorije.  
  - **Odgovori**:
    - `200 OK` + `Product[]`.

- **GET `/api/products/{productId}`**  
  - **Opis**: Vrne podrobnosti enega izdelka.  
  - **Odgovori**:
    - `200 OK` + `Product`.

- **POST `/api/products`**  
  - **Opis**: Doda nov izdelek v osebni katalog uporabnika.  
  - **Body**: `Product` JSON.  
  - **Odgovori**:
    - `200 OK` + ustvarjen `Product`.

- **PATCH `/api/products/{productId}`**  
  - **Opis**: Posodobi podatke o izdelku (ime, cena, kategorija …).  
  - **Body**: `Product` JSON z novimi podatki.  
  - **Odgovori**:
    - `200 OK` + posodobljen `Product`.

- **DELETE `/api/products/{productId}`**  
  - **Opis**: Izbriše izdelek iz kataloga.  
  - **Odgovori**:
    - `200 OK` + sporočilo o uspehu.

---

## Recipe Service (`recipe-service`)

Osnovni path: `/api/recipes`

- **POST `/api/recipes/import`**  
  - **Opis**: Uvozi recept z danega URL naslova s pomočjo OpenAI in ga shrani uporabniku.  
  - **Body**: plain string z URL-jem (npr. `"https://primer.si/recept"`).  
  - **Odgovori**:
    - `200 OK` + tekstovno sporočilo o uspehu ali napaki (string).

- **GET `/api/recipes`**  
  - **Opis**: Vrne vse recepte, ki jih je ustvaril prijavljen uporabnik.  
  - **Odgovori**:
    - `200 OK` + `Recipe[]`.

- **GET `/api/recipes/{id}`**  
  - **Opis**: Vrne podrobnosti posameznega recepta.  
  - **Odgovori**:
    - `200 OK` + `Recipe`
    - `404 Not Found`, če recept ne obstaja.

- **DELETE `/api/recipes/{id}`**  
  - **Opis**: Izbriše recept, vendar le, če ga je ustvaril trenutni uporabnik.  
  - **Odgovori**:
    - `204 No Content` ob uspehu
    - `403 Forbidden`, če recept ni v lasti uporabnika
    - `404 Not Found`, če recept ne obstaja.

- **POST `/api/recipes/{id}/create-list`**  
  - **Opis**: Pošlje Kafka dogodek za ustvarjanje nakupovalnega seznama iz recepta.  
  - **Odgovori**:
    - `200 OK` + tekstovno sporočilo, da je zahteva poslana.

---

## Gateway Service (`gateway-service`)

Gateway servis uporablja Spring Cloud Gateway in definira route, ki preslikajo zunanje URL-je na zgornje mikrostoritve:

- `/api/users/**` → `user-service` (`USER_SERVICE_URL`)
- `/api/shopping-lists/**`, `/api/items/**`, `/api/products/**` → `list-service` (`LIST_SERVICE_URL`)
- `/api/recipes/**` → `recipe-service` (`RECIPE_SERVICE_URL`)

Vsi zgornji endpointi so v praksi dostopni preko gateway-ja na naslovu:

```text
http://<gateway-host>:8080/<path>
```

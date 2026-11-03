# Laboratorul 5: Modelează Dashboard-ul și definește-i API-ul

## Obiectiv

Descrie modelul datelor și API-ul HTTP pentru Personal Investment Dashboard.
Apoi generează OpenAPI din definiții din codul sursă care corespund proiectării
tale.

Citește [Lecția 6](../../lectures/lecture-06-api-design-and-data-modeling.md).
Folosește proiectarea revizuită din Laboratoarele 1-4.

```text
Data model -> endpoints -> source definitions -> OpenAPI -> client flow
```

## Domeniu de aplicare

- Consultarea generală a pieței, Acțiuni în tendințe, filtrare și căutare după
  nume sau simbol.
- Detaliile Acțiunilor, cele mai recente prețuri acceptate și istoricul zilnic
  al prețurilor.
- Citirea unui Watchlist privat, adăugarea unei Acțiuni și eliminarea unei
  Acțiuni.

Comenzile, deținerile, profitul și pierderea, SSE și partajarea publică nu fac
parte din acest laborator.

## 1. Descrie modelul datelor

Completează modelul înainte să proiectezi endpoint-urile.

1. Enumeră entitățile necesare pentru datele de piață și Watchlist. Include
   înregistrări interne de sincronizare dacă proiectarea anterioară are nevoie
   de ele.
1. Pentru fiecare entitate, definește scopul, identitatea, câmpurile și
   relațiile. Dă fiecărui câmp un sens, un exemplu și o regulă pentru valoarea
   lipsă.
1. Desenează o diagramă Mermaid ER cu cardinalitatea relațiilor și apartenența
   datelor. Modelează Utilizatorul autentificat ca identitate externă; nu sunt
   necesare un tabel local User sau endpoint-uri de autentificare.
1. Separă informațiile stocate de valorile derivate, precum numerele de elemente
   și actualitatea datelor. Folosește **snake_case** pentru câmpurile stocate,
   **camelCase** pentru câmpurile API și nume booleene directe, precum `active`.

Păstrează marcajele temporale ale sursei și politica de actualitate din
laboratoarele anterioare. Definește ordonarea listei de Acțiuni în tendințe.
Folosește sensuri logice pentru câmpuri; tipurile SQL, constrângerile și
indexurile vor fi tratate ulterior.

## 2. Descrie toate endpoint-urile clientului

Asociază fiecare acțiune din domeniul de aplicare cu un endpoint REST. Un
endpoint poate acoperi mai multe acțiuni prin parametri.

| Acțiunea produsului | Metodă și cale cu versiune | Parametri sau corp | Succes | Regulă de acces |
| ------------------- | -------------------------- | ------------------ | ------ | --------------- |
|                     |                            |                    |        |                 |

Pentru fiecare operație, descrie:

- Parametrii și câmpurile de intrare: valori obligatorii, valori implicite și
  validare.
- Modelele cererii și răspunsului, cu exemple JSON realiste. Exclude câmpurile
  controlate de server din modelele de intrare.
- Codurile de stare pentru succes și erori, cu o singură structură JSON pentru
  erori. Folosește `400` pentru date de intrare invalide.
- Autentificarea și accesul. Obține proprietarul din autentificare și verifică
  apartenența datelor la fiecare citire sau modificare a datelor private.
- Pentru liste: limitele dimensiunii paginii, ordonarea, filtrele și
  informațiile pentru pagina următoare. Arată o pagină filtrată și un rezultat
  gol.

Notează politicile API comune:

1. Locul versiunii API-ului, o schimbare compatibilă și o schimbare
   incompatibilă.
1. Mecanismul de autentificare.
1. Regulile de paginare și modul în care clientul tratează limitele de frecvență
   `429`.

Descrie apelurile către Collector ca integrări ale serverului. Copiile din cache
nu au nevoie de endpoint-uri pentru client.

## 3. Generează și inspectează OpenAPI

Folosește un framework precum FastAPI sau NestJS cu Swagger.

1. Definește rutele, modelele, validarea, erorile, autentificarea și exemplele
   în codul sursă. Funcțiile neimplementate sunt suficiente; marchează-le clar.
1. Generează `openapi.yaml` sau `openapi.json`. Corectează nepotrivirile în
   sursă și regenerează.
1. Deschide documentul într-un instrument de vizualizare OpenAPI. Compară-l cu
   inventarul endpoint-urilor și verifică exemplele, inclusiv o pagină filtrată,
   o listă goală și o eroare.

Instrumente utile:

- [FastAPI: Primii pași](https://fastapi.tiangolo.com/tutorial/first-steps/)
- [NestJS: Introducere în OpenAPI](https://docs.nestjs.com/openapi/introduction)
- [Swagger Editor](https://editor.swagger.io/)

## 4. Parcurge un flux al clientului

```text
Open trending Stocks -> open one Stock for more market data -> add it to Watchlist
```

Enumeră **toate endpoint-urile** necesare pentru acest flux, în ordinea
apelurilor. Folosește exemplele tale API pentru a arăta URL-ul de bază,
autentificarea, cererile și răspunsurile. Include cererile de date de piață
necesare pentru ecranul cu detaliile Acțiunii.

Precizează ce afișează UI-ul la încărcare, la succes, pentru o listă goală și
când o cerere eșuează. Nu este necesară implementarea în Browser.

## Ce trebuie să predai

1. `SOLUTION.md`: modelul datelor, diagrama ER, inventarul endpoint-urilor,
   exemplele JSON, politicile API și fluxul clientului.
1. Definițiile din codul sursă și informațiile despre dependențe pentru
   generarea OpenAPI.
1. Documentul OpenAPI generat, comanda de generare și o captură de ecran sau o
   scurtă înregistrare a verificării din instrumentul de vizualizare.

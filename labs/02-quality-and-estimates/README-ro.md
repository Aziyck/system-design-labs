# Laboratorul 2: Cuantifică citirile Dashboard-ului

## Scop

Transformă comportamentul analizat în Laboratorul 1 în ținte măsurabile de
calitate, într-o estimare a volumului de citiri pentru trei niveluri și într-o
estimare a stocării datelor despre piață.

Înainte să începi, citește
[ghidul de lectură pentru acasă al Lecției 3](../../lectures/lecture-03-quality-requirements-workload-and-capacity.md).

## Punctul de pornire din analiza Laboratorului 1

- Utilizatorul autentificat este actorul uman direct.
- Market Data Provider este sistemul extern.
- Prima versiune acceptă Overview, Filter, Search, Stock detail, price history și
  un Watchlist privat.
- Prețurile pieței afișează ora furnizorului sau întârzierea.
- Un preț lipsă este indisponibil, nu zero.
- Un Utilizator nu poate citi sau modifica Watchlist-ul altui Utilizator.

Setul de citiri analizat pentru acest laborator este:

- Overview;
- Filter;
- Stock price;
- History;
- Watchlist;
- Search.

## Informațiile de la client

Clientul oferă aceste așteptări:

- Prețurile pentru Stock trebuie să pară imediate.
- Majoritatea citirilor corecte trebuie să se finalizeze în cel mult 2 secunde în
  perioadele aglomerate.
- Întârzierea așteptată a furnizorului este de aproximativ 15 minute.
- Datele mai vechi trebuie marcate vizibil ca întârziate sau indisponibile.
- Timpul de funcționare al Dashboard-ului trebuie să fie măsurabil separat în
  timpul orelor de tranzacționare și în restul zilei.

Tratează aceste afirmații ca date de intrare pentru produs. Când lipsește o
țintă, selectează una și explică presupunerea. O cerință de calitate trebuie să
conțină:

```text
measure + target + operating condition
```

Folosește același comportament al Utilizatorului pentru fiecare nivel:

| Citire      | Comportamentul Utilizatorului                |
| ----------- | -------------------------------------------- |
| Overview    | 70% reîmprospătează la fiecare 30 de secunde |
| Filter      | 50% modifică filtrul de 3 ori pe minut       |
| Stock price | 20% solicită o dată pe secundă               |
| History     | 20% solicită o dată la 5 minute              |
| Watchlist   | 60% reîmprospătează o dată pe minut          |
| Search      | 10% caută de 3 ori pe minut                  |

Folosește aceste grupuri de Utilizatori concurenți:

- 300;
- 3,000;
- 30,000.

La deschiderea pieței:

- 30% reîmprospătează Overview o dată într-un interval de 10 secunde;
- 60% din acest grup reîmprospătează și Watchlist;
- adaugă aceste acțiuni la traficul stabil;
- adaugă o marjă de capacitate de 10% după ce calculezi subtotalul;
- rotunjește în sus numai ținta finală de capacitate.

Presupune că fiecare acțiune enumerată a Utilizatorului creează o cerere către
Dashboard. Nu presupune că o cerere către Dashboard creează o cerere către
furnizor. Traficul furnizorului necesită un model separat și dovezi.

## Ce trebuie să trimiți

1. Cerințe de calitate.
2. Estimări RPS în regim stabil.
3. Estimări RPS la deschiderea pieței.
4. Estimări de stocare.
5. Posibile blocaje.

## 1. Cerințe de calitate

Scrie cerințe măsurabile pentru cele patru calități de bază:

### Latența citirilor

- Definește începutul și sfârșitul măsurării.
- Folosește o percentilă pentru "majoritatea" citirilor.
- Precizează ce se întâmplă la limita de 2 secunde.

### Disponibilitate bazată pe timpul de funcționare

- Definește când Dashboard-ul este utilizabil.
- Definește orele care aparțin intervalului orelor de tranzacționare.
- Alege o țintă a timpului de funcționare exprimată ca număr de nouari pentru
  orele de tranzacționare.
- Alege o țintă a timpului de funcționare exprimată ca număr de nouari pentru
  restul zilei.
- Justifică de ce cele două intervale au nevoie de ținte identice sau diferite.
- Măsoară separat cele două intervale pe parcursul a 30 de zile și calculează
  bugetul de indisponibilitate pentru fiecare.

### Consistență

- Pentru un Stock price, precizează vechimea maximă acceptată a datelor și când
  rezultatul devine indisponibil.
- Pentru o modificare a Watchlist-ului, precizează ce trebuie să arate următoarea
  citire a Watchlist-ului de către același Utilizator după finalizarea
  modificării.

Fiecare cerință trebuie să precizeze măsura, ținta și condiția de operare.
Păstrează un rezultat indisponibil separat de un rezultat cu date corecte. Un
rezultat indisponibil rapid poate respecta ținta de latență și poate încălca o
altă cerință de calitate.

## 2. Estimări RPS în regim stabil

Folosește această formulă pentru fiecare rând și pentru fiecare nivel:

```text
RPS = concurrent Users x participating share x actions per User / seconds
```

Completează acest tabel. Arată calculul, unitățile și rezultatul nerotunjit
înainte de fiecare total.

| Citire                    | 300 Utilizatori | 3,000 Utilizatori | 30,000 Utilizatori |
| ------------------------- | --------------- | ----------------- | ------------------ |
| Overview                  |                 |                   |                    |
| Filter                    |                 |                   |                    |
| Stock price               |                 |                   |                    |
| History                   |                 |                   |                    |
| Watchlist                 |                 |                   |                    |
| Search                    |                 |                   |                    |
| **Total în regim stabil** |                 |                   |                    |

## 3. Estimări RPS la deschiderea pieței

Folosește comportamentul de la deschiderea pieței din informațiile oferite de
client. Estimează traficul stabil continuu și cele două fluxuri suplimentare
afișate mai jos. Nu număra de două ori traficul stabil pentru Overview sau
Watchlist. Completează toate calculele pentru deschiderea pieței într-un singur
tabel:

| Calcul la deschiderea pieței                     | 300 Utilizatori | 3,000 Utilizatori | 30,000 Utilizatori |
| ------------------------------------------------ | --------------- | ----------------- | ------------------ |
| Trafic stabil de citire                          |                 |                   |                    |
| Flux suplimentar de reîmprospătare Overview      |                 |                   |                    |
| Flux suplimentar de reîmprospătare Watchlist     |                 |                   |                    |
| **Subtotal la deschiderea pieței**               |                 |                   |                    |
| Marjă de capacitate de 10%                       |                 |                   |                    |
| **Țintă rotunjită în sus la deschiderea pieței** |                 |                   |                    |

Rotunjește în sus numai ținta finală pentru deschiderea pieței.

## 4. Estimări de stocare

Dashboard-ul trebuie să sincronizeze în propriul sistem datele despre piață de
care are nevoie de la Market Data Provider. Estimează datele brute pe care
Dashboard-ul trebuie să le stocheze.

### Cercetează și definește un Stock

Cercetează ce poate însemna un "Stock" într-un produs cu date despre piață.
Apoi definește ce înseamnă acesta pentru acest Dashboard.

Ia și justifică aceste decizii despre produs:

- țările, piețele sau bursele pe care le acceptă prima versiune;
- tipurile de instrumente incluse sau excluse, precum common stock, preferred
  stock, ADRs, ETFs sau funds;
- dacă instrumentele inactive sau delistate rămân disponibile;
- câte Stocks conține domeniul de aplicare selectat.

Citează cel puțin o sursă pentru definiția instrumentului și o sursă pentru
numărul estimat de Stocks acceptate. Precizează data la care a fost observat
fiecare număr.

### Definește datele sincronizate

Enumeră datele necesare pentru fluxurile din Laboratorul 1. Decide ce stochează
Dashboard-ul pentru:

- informațiile despre identitatea Stock, Search și Filter;
- cel mai recent preț și ora furnizorului;
- price history, inclusiv intervalul și perioada de păstrare;
- orice alte date ale furnizorului necesare pentru domeniul de aplicare selectat
  al produsului.

Conectează fiecare câmp sau set de date stocat la o nevoie a produsului. Nu copia
fiecare câmp al furnizorului fără un motiv. Precizează frecvența de sincronizare
ca presupunere, dar nu estima traficul cererilor către furnizor în această
secțiune.

### Calculează stocarea

Creează o înregistrare stocată reprezentativă sau un eșantion de date și
folosește-l pentru a estima media de octeți per înregistrare. Completează acest
tabel:

```text
raw storage = record count x average bytes per record

history record count
  = supported Stocks x history points per Stock per day x retained days
```

| Set de date              | Decizia despre produs și perioada de păstrare | Calculul numărului de înregistrări | Octeți per înregistrare | Stocare brută |
| ------------------------ | --------------------------------------------- | ---------------------------------- | ----------------------- | ------------- |
| Date de referință Stock  |                                               |                                    |                         |               |
| Cele mai recente prețuri |                                               |                                    |                         |               |
| Price history            |                                               |                                    |                         |               |
| Alte date selectate      |                                               |                                    |                         |               |
| **Total**                |                                               |                                    |                         |               |

## 5. Găsește și analizează posibilele blocaje

Folosește cerințele de calitate, estimările RPS și System Context view. Găsește
cel puțin un posibil blocaj pentru fiecare calitate. O valoare RPS mare este un
motiv pentru a investiga o cale. Nu este o dovadă că acea cale este un blocaj.

| Calitate        | Posibil blocaj | Dovezi din acest laborator | Efect posibil | Ce trebuie măsurat în continuare |
| --------------- | -------------- | -------------------------- | ------------- | -------------------------------- |
| Latență         |                |                            |               |                                  |
| Consistență     |                |                            |               |                                  |
| Debit           |                |                            |               |                                  |
| Disponibilitate |                |                            |               |                                  |

Pentru fiecare rând, explică:

1. calea de citire, calea stării sau dependența externă care creează presiunea;
2. cum poate cauza încălcarea țintei de calitate;
3. dovezile care susțin ipoteza;
4. măsurarea care ar confirma-o sau ar respinge-o.

## Listă de verificare

- [ ] Am scris cerințe măsurabile pentru toate calitățile de bază.
- [ ] Am ales și am justificat ținte ale timpului de funcționare pentru ambele
      părți ale zilei.
- [ ] Am arătat calculele RPS în regim stabil și la deschiderea pieței pentru
      toate cele trei niveluri.
- [ ] Am cercetat și am definit domeniul de aplicare pentru Stocks acceptate.
- [ ] Am estimat stocarea brută inițială, zilnică și pentru un an a datelor despre
      piață.
- [ ] Am precizat presupunerile, unitățile, intervalele și rotunjirea finală.
- [ ] Am analizat un posibil blocaj pentru fiecare calitate de bază.

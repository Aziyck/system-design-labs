# Laboratorul 1: Definește produsul inițial

## Scop

Definește un Personal Investment Dashboard restrâns fără să proiectezi
arhitectura sa internă sau să selectezi tehnologia.

Înainte să începi, citește
[ghidul de lectură pentru acasă al Lecției 2](../../lectures/lecture-02-product-scope-and-system-boundaries-ro.md).

Cererea clientului este:

```text
Ajută-mă să îmi urmăresc investițiile.
```

Această cerere este prea vagă. Folosește dovezi și metoda din Lecția 2 pentru a o
transforma într-un contract consecvent al produsului:

```text
dovezi din cercetare
  -> părți interesate și actori
  -> promisiunea produsului
  -> obiective și obiective excluse
  -> povești de Utilizator și definiții de Făcut
  -> limita sistemului
  -> C4 System Context view
```



## Ce trebuie să trimiți

Trimite un document cu aceste secțiuni:

1. Cercetarea produsului.
2. Părți interesate și actori.
3. Promisiunea și domeniul de aplicare ale produsului.
4. Cerințe funcționale.
5. C4 System Context view.



## 1. Cercetarea produsului

Petrece 20-30 de minute analizând două produse existente:

- un produs cu date despre piață, precum Google Finance, Yahoo Finance sau Apple
Stocks;
- un produs de tranzacționare, precum Interactive Brokers sau TradingView.

Paginile publice, descrierile produselor, capturile de ecran și listările
aplicațiilor sunt suficiente. Nu ai nevoie de un cont sau de acces plătit.

Începe cu această întrebare de cercetare:

```text
Cum ajută produsele existente un Utilizator să urmărească informațiile despre
piață și ce părți aparțin primei versiuni a acestui Dashboard?
```

Completează acest tabel:


| Produs | Utilizatorul probabil și obiectivul său | Model reutilizabil |
| ------ | --------------------------------------- | ------------------ |
|        |                                         |                    |
|        |                                         |                    |




## 2. Părți interesate și actori

Folosește
[ghidul Guvernului Regatului Unit pentru cartografierea părților interesate](https://analysisfunction.civilservice.gov.uk/policy-store/stakeholder-mapping/)
ca metodă simplă:

1. Enumeră persoanele sau organizațiile care sunt afectate de Dashboard sau îl
  pot influența.
2. Marchează motivația lor ca ridicată sau scăzută.
3. Marchează influența lor ca ridicată sau scăzută.
4. Oferă un motiv pentru fiecare poziționare.

Completează acest tabel:


| Parte interesată | Motivație | Influență | Motiv |
| ---------------- | --------- | --------- | ----- |
|                  |           |           |       |
|                  |           |           |       |
|                  |           |           |       |


Apoi plasează părțile interesate în această matrice:


| Motivație | Influență scăzută | Influență ridicată |
| --------- | ----------------- | ------------------ |
| Ridicată  |                   |                    |
| Scăzută   |                   |                    |


La final, clasifică fiecare candidat ca actor uman direct, sistem extern sau
altă parte interesată. Numai actorii și sistemele conectate direct aparțin în
System Context view.

Folosește această distincție:

- O parte interesată este afectată de produs sau poate modifica o decizie privind
produsul.
- Un actor este o persoană sau un sistem extern care interacționează direct cu
Dashboard.



## 3. Promisiunea și domeniul de aplicare ale produsului

Scrie o promisiune a produsului:

```text
<Produs> ajută <Utilizatorul principal> să rezolve <problema>, astfel încât
<rezultatul util>.
```

Scrie:

- cinci obiective;
- trei obiective excluse.

Fiecare obiectiv trebuie să precizeze un rezultat vizibil pentru Utilizator.
Fiecare obiectiv exclus trebuie să elimine activitate din prima versiune. Nu
folosi un ecran, o componentă sau o tehnologie ca obiectiv sau obiectiv exclus.

## 4. Cerințe funcționale

Scrie in jur de cinci povești de Utilizator. Folosește `DASH-1`  ca ID pentru fiecare cerință.

Urmează
[ghidul Agile Alliance pentru poveștile de Utilizator](https://agilealliance.org/glossary/user-stories/)
și modelul său [Three Cs](https://agilealliance.org/glossary/three-cs/): scrie o
poveste scurtă, discută cazurile sale importante și confirmă-le prin definiții
de Făcut.

Pentru fiecare poveste, folosește trei pași:

1. Obiectivul actorului: precizează ce trebuie actorul să obțină sau să afle.
2. Povestea de Utilizator: precizează actorul, obiectivul și rezultatul util.
3. Definițiile de Făcut: enumeră între două și patru verificări observabile.

Folosește această formă pentru povestea de Utilizator:

```text
Ca <actor>, vreau <obiectiv>, astfel încât <rezultat util>.
```

Pentru fiecare poveste, scrie:

```text
Definițiile de Făcut:
- <rezultat normal observabil>
- <rezultat alternativ important>
- <limită de acces sau de domeniu de aplicare, când este relevantă>
```

Împreună, cele cinci povești trebuie să acopere cele mai importante fluxuri.

Cel puțin două povești trebuie să aibă o verificare în Definiția de Făcut
pentru un rezultat lipsă, învechit, neacceptat sau neautorizat.

Nu include ecrane, componente, baze de date, API-uri, memorii cache, cozi,
servicii, produse cloud, limbaje de programare sau framework-uri.

## 5. C4 System Context

Apoi desenează un C4 System Context view. Urmează
[ghidul oficial C4 System Context](https://c4model.com/diagrams/system-context):

1. Pune Personal Investment Dashboard în centru.
2. Adaugă fiecare actor uman direct.
3. Adaugă fiecare sistem extern conectat direct.
4. Etichetează fiecare relație cu scopul ei pentru produs.

Nu arăta în interiorul Dashboard aplicații, API-uri, baze de date, memorii cache,
cozi, servicii sau produse cloud.

Pentru fiecare sistem extern, verifică:

- ce responsabilitate a Dashboard-ului are nevoie de acesta;
- ce rezultat furnizează acesta;
- ce vede Utilizatorul când rezultatul lipsește, este învechit sau nu este
acceptat.

Sistemul extern deține rezultatul său sursă. Dashboard-ul deține în continuare
modul în care acel rezultat devine clar și sigur pentru Utilizator.

## Listă de verificare

- [ ] Am cercetat cel puțin două produse existente.
- [ ] Am citat dovezi pentru fiecare model de produs selectat.

- [ ] Cercetarea mea a modificat sau a confirmat cel puțin o decizie despre domeniul de aplicare.

- [ ] Am cartografiat motivația și influența părților interesate.
- [ ] Am separat părțile interesate, actorii umani direcți și sistemele externe.
- [ ] Promisiunea produsului meu este o propoziție clară.

- [ ] Obiectivele și obiectivele mele excluse sunt compatibile cu promisiunea produsului.

- [ ] Am scris cel puțin cinci povești de Utilizator.
- [ ] Fiecare poveste are între două și patru definiții de Făcut.
- [ ] Cel puțin două povești includ un rezultat alternativ important.

- [ ] Dependențele mele externe includ un rezultat lipsă, învechit sau neacceptat, care este vizibil pentru Utilizator.

- [ ] Am creat un C4 System Context view pentru Dashboard.

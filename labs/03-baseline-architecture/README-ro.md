# Laboratorul 3: Desenează limita Dashboard-ului

## Scop

Creează o diagramă Mermaid de tip System Context, una de tip Container și una
de tip Component pentru Dashboard. Apoi testează proiectarea cu trei diagrame
de secvență. Pune toate cele șase diagrame în blocuri de cod `mermaid` și
păstrează sursa în lucrarea predată.

Citește [Lecția 4](../../lectures/lecture-04-architecture-decomposition-and-data-flows.md),
inclusiv exemplul detaliat despre Market Data Collector.

## Informații date

Un Utilizator autentificat poate să răsfoiască și să filtreze Stocks, să
citească prețuri și istoric și să administreze un Watchlist privat. Market Data
Provider furnizează date întârziate și poate returna date invalide, poate
limita cererile sau poate fi indisponibil.

Folosește țintele pentru volumul de lucru din Laboratorul 2.

## 1. Diagrama System Context

Desenează limita sistemului, actorii și sistemele externe, precum și relațiile
etichetate. Nu arăta părțile interne de execuție.

## 2. Diagrama Container

Deschide limita sistemului. Alege cel mai mic set de părți de execuție și
stocări de date care poate îndeplini cerințele. Dă fiecărei părți o
responsabilitate și etichetează fiecare relație.

## 3. Diagrama Component

Deschide Dashboard Application din diagrama Container. Arată componentele
principale, responsabilitățile lor și containerele sau elementele externe care
comunică direct cu ele. Nu arăta clase, funcții sau aplicații lansate
separat ca fiind componente.

## 4. Diagrame de secvență

Desenează aceste trei fluxuri. Folosește participanți din view-urile de
arhitectură și păstrează numele consecvente. Arată cererile, rezultatele și
rezultatele alternative cu blocuri `alt`. Precizează ipotezele inițiale sub
fiecare diagramă.

### A. Sincronizează datele de piață

Arată ce pornește o sincronizare, cum sunt verificate datele furnizorului și
când devin datele acceptate disponibile pentru citirile ulterioare. Include
date invalide și expirarea timpului de așteptare la furnizor sau limitarea
cererilor. Precizează ce se întâmplă cu datele acceptate anterior când
sincronizarea eșuează. Păstrează datele de autentificare ale furnizorului pe
server.

### B. Citește prețul unui Stock

Arată verificările identității și ale datelor de intrare, citirea datelor și
rezultatul afișat Utilizatorului. Include un preț lipsă, date mai vechi decât
limita acceptată de tine și o defectare a stocării. Precizează clar dacă o
citire a Utilizatorului are nevoie de o cerere nouă către furnizor.

### C. Modifică un Watchlist privat

Arată verificările identității, ale datelor de intrare și ale permisiunii
înainte de modificare. Include o încercare neautorizată și o defectare a
stocării. Marchează punctul în care succesul poate fi raportat și precizează
ce trebuie să arate următoarea citire a aceluiași Utilizator.

## Listă de verificare

- [ ] Am inclus o diagramă System Context, una Container și una Component.
- [ ] Am inclus cele trei diagrame de secvență cerute.
- [ ] Toate cele șase blocuri Mermaid se afișează fără erori.
- [ ] Diagrama Component deschide numai Dashboard Application.
- [ ] Fluxurile mele arată rezultate normale, acțiuni respinse și rezultate în
      caz de defectare.
- [ ] Proiectarea acoperă citirile normale, datele private, preluarea datelor de
      la furnizor și defectările.

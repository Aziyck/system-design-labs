# Laboratorul 4: Scalează traseul de citire al Dashboard-ului

## Obiectiv

Aplică [Lecția 5](../../lectures/lecture-05-horizontal-scaling-caching-and-availability.md)
la Personal Investment Dashboard. Scalează serviciul și Market Data Store,
alege cum ajung cererile la instanțele pregătite și proiectează cel puțin două
cazuri de utilizare a cache-ului, cu memorie locală și Redis.

Păstrează cerințele de calitate din Laboratorul 2 și limita sistemului din
Laboratorul 3. Notează orice schimbare a acelor decizii anterioare.

## Informații date

- Cazul cu 3,000 de Utilizatori generează 600 de citiri ale prețurilor Acțiunilor
  pe secundă (RPS). Folosește o marjă de capacitate de 20% pentru acest laborator.
  Păstrează acea țintă după oprirea unei instanțe Dashboard.
- O instanță Dashboard gestionează în siguranță 250 RPS pentru prețurile
  Acțiunilor, respectând ținta de latență. Presupune că acest test de încărcare a
  folosit setările de resurse din exemplul HPA al Lecției 5. Cererile pentru
  prețurile Acțiunilor au costuri similare de procesare.
- Market Data Store are inițial un singur nod. Acesta poate servi cel mult 400 de
  citiri publice ale prețurilor Acțiunilor pe secundă, respectând ținta de latență
  și acceptând scrieri ale datelor de piață. Fiecare replică de citire poate servi
  aceleași 400 RPS de citire. Rezervă liderul de scriere pentru scrierea datelor
  acceptate și pentru citirile care au nevoie de cea mai recentă valoare a sa.
  Nu îl include în capacitatea pentru citirile publice obișnuite.
- Market Data Store păstrează durabil prețurile acceptate și marcajele temporale
  ale furnizorului. Un Market Data Provider poate returna date întârziate, date
  invalide, limite de rată sau niciun răspuns. Nu fiecare citire din Browser are
  nevoie de o cerere nouă către furnizor.
- Un cache în memoria locală poate pierde orice intrare în orice moment. Redis
  poate fi gol sau indisponibil, inclusiv chiar înainte de deschiderea pieței.
  Nu presupune o rată de găsire a datelor în cache când stabilești numărul de
  instanțe ale serviciului sau de replici de citire.
- Laboratorul 2 include și citiri Overview, History, Filter și Search. Folosește
  un rezultat din cache pentru mai mulți Utilizatori doar dacă nu conține date
  private ale Utilizatorilor.

Acestea sunt ipoteze didactice. Într-un sistem real, măsoară capacitatea
instanțelor și a stocării prin teste reprezentative de încărcare și defectare.

## 1. Scalează serviciul și rutează cererile

1. Calculează ținta pentru prețurile Acțiunilor cu marja inclusă. Calculează cel
   mai mic număr de instanțe Dashboard pregătite care păstrează ținta după oprirea
   unei instanțe.
2. Alege distribuirea traficului la L4 sau L7 și o metodă de selecție: round robin,
   cele mai puține conexiuni sau hash după IP-ul clientului. Explică de ce se
   potrivește acestor cereri și care este limita sa principală.
3. Precizează ce date trebuie să se păstreze după pierderea unei instanțe
   Dashboard și ce date temporare pot rămâne în memoria acelei instanțe.

## 2. Scalează Market Data Store

Refolosește diagrama Container View a Dashboard-ului și adaugă următoarele elemente:

- Distribuitor de trafic
- Instanțe ale serviciului
- Instanțe ale bazei de date

## 3. Pune în cache două citiri publice

Folosește **prețul Acțiunii** ca prim caz. Alege cel puțin încă o citire din
Overview, History, Filter sau Search care nu conține date private ale
Utilizatorilor. Pentru fiecare caz, explică munca repetată pe care o elimină
cache-ul și definește un contract pentru cache:

| Decizie               | Ce trebuie să precizezi                                                                                          |
| --------------------- | ---------------------------------------------------------------------------------------------------------------- |
| Cheie și valoare      | Include fiecare intrare care schimbă rezultatul; exclude datele private ale Utilizatorilor                       |
| Sursă                 | Numește datele stocate care pot reconstrui un cache gol                                                          |
| Durată de viață L1    | Stabilește o durată locală scurtă, care nu depășește limita permisă de politica datelor                          |
| Actualitate           | Alege TTL simplu sau stale-while-revalidate; stabilește limitele pentru date actuale, date învechite și expirare |
| Rezultat la defectare | Precizează ce vede Utilizatorul când oricare dintre nivelurile cache sau sursa sa durabilă se defectează         |

Desenează **o diagramă Mermaid de secvență pentru citirea din cache pentru fiecare
caz de utilizare**. Fiecare secvență trebuie să arate o valoare găsită în L1, o
valoare lipsă din L1 urmată de găsirea ei în Redis și o valoare lipsă din ambele
niveluri urmată de citirea sursei durabile și completarea cache-ului. Arată și
rezultatul pentru o sursă indisponibilă.
Dacă alegi stale-while-revalidate, arată un rezultat învechit permis și o
actualizare eșuată în fundal.

Explică ce set limitat de chei ai încărca în avans înainte de deschiderea pieței
și după recuperare. Precizează ce vede Utilizatorul când încărcarea inițială este
incompletă sau capacitatea sigură de acces de rezervă la sursă este epuizată.

## 4. Implementează traseul cache-ului Redis prin comenzi

Pentru a rula comenzile local, urmează
[ghidul oficial de instalare](https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/)
Redis pentru sistemul tău. [Ghidul de început](https://redis.io/docs/latest/develop/get-started/)
Redis arată cum să pornești un server, iar [ghidul CLI](https://redis.io/docs/latest/develop/tools/cli/)
arată cum să trimiți comenzi. Verifică conexiunea cu `redis-cli PING`;
Redis ar trebui să returneze `PONG`.

Pentru **fiecare** caz de utilizare a cache-ului, arată o transcriere scurtă a
comenzilor și rezultatelor `redis-cli`. Folosește chei și valori concrete.
Execută acești pași în ordine:

1. Folosește `GET` pentru cheie și arată că valoarea lipsește.
2. Citește sursa durabilă în diagrama de secvență. Apoi folosește `SET` cu o
   valoare JSON de exemplu și o expirare `EX` în secunde pentru a completa Redis.
3. Folosește din nou `GET` pentru cheie și `TTL` pentru a verifica durata de viață rămasă.

Arată valoarea JSON pe care o stochezi. Păstrează distincte `cached_at` și
marcajele temporale ale sursei acolo unde contează. Explică de ce un TTL Redis nu
dovedește actualitatea datelor sursă. Precizează dacă arhitectura ta Redis nu
folosește persistență pe disc sau folosește RDB ori AOF și ce se întâmplă după ce
Redis repornește cu un cache gol.

## Ce trebuie să predai

1. Calculul numărului de instanțe Dashboard, o alegere de distribuire a traficului
   cu motivul și limita ei și starea care trebuie să se păstreze după pierderea
   unei instanțe.
2. Diagrama Mermaid Container View actualizată din Laboratorul 3. Arată
   distribuitorul de trafic, instanțele serviciului Dashboard și instanțele Market Data Store.
3. Un contract pentru cache și o diagramă Mermaid de secvență pentru citirea din
   cache pentru fiecare citire publică aleasă, cu cel puțin două cazuri de
   utilizare în total.
4. Regulile cu limite pentru încărcarea în avans a cache-ului și accesul de
   rezervă la sursă, inclusiv rezultatul pentru Utilizator când încărcarea este
   incompletă sau capacitatea de rezervă este epuizată.
5. O transcriere `redis-cli` pentru fiecare caz de utilizare a cache-ului, cu
   valori JSON, comenzi de expirare și alegerea persistenței Redis.

## Listă de verificare

- [ ] Calculul meu pentru instanțele Dashboard include marja și pierderea unei instanțe.
- [ ] Am ales un nivel și o metodă de distribuire a traficului și am explicat
      motivul și o limită.
- [ ] Container View actualizat arată distribuitorul de trafic, instanțele
      serviciului și instanțele bazei de date. Starea necesară se păstrează după
      pierderea unei instanțe a serviciului.
- [ ] Am cel puțin două cazuri de utilizare a cache-ului, cu un contract și o
      diagramă de secvență pentru fiecare, care arată L1, Redis și sursa durabilă.
- [ ] Cheile și valorile mele din cache nu conțin date private ale Utilizatorilor.
- [ ] Secvențele mele arată valori găsite în cache, valori lipsă, defectarea sursei
      și orice comportament stale-while-revalidate ales.
- [ ] Încărcarea mea în avans este limitată, iar încărcarea incompletă sau
      capacitatea de rezervă epuizată are un rezultat explicit vizibil pentru Utilizator.
- [ ] Fiecare transcriere Redis arată o valoare lipsă la `GET`, `SET ... EX`, o
      valoare găsită la `GET` și `TTL`, cu o valoare JSON și o alegere de persistență.
- [ ] Explic de ce TTL-ul Redis nu dovedește actualitatea datelor sursă.
- [ ] Diagramele mele Mermaid se randează fără erori.

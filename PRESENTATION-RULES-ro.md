# Reguli de prezentare și predare a laboratoarelor

Aceste reguli se aplică fiecărui laborator din acest curs. Versiunea în engleză
este [`PRESENTATION-RULES.md`](./PRESENTATION-RULES.md). Ambele versiuni conțin
aceleași cerințe.

## 1. Unde îți publici lucrarea

Creează un fork al acestui repository o singură dată, la începutul cursului.
Fork-ul se face dintr-un singur buton de pe această pagină și este singura
operație nouă de Git pe care ți-o cere cursul.

- Fork-ul tău trebuie să rămână **public**.
- Folosește același fork pentru tot cursul. Nu crea un al doilea fork sau un al
  doilea repository.
- Poți redenumi fork-ul din pagina lui de Settings, dacă preferi alt nume. Este
  opțional.
- Scrie soluțiile în **engleză sau română**. Alege o limbă și păstreaz-o pentru
  toate laboratoarele.

Când apar laboratoare noi aici, deschide fork-ul tău și apasă **Sync fork** ca
să le aduci la tine. Este un buton, nu o comandă. O sincronizare nu îți atinge
niciodată fișierele proprii.

## 2. Structura repository-ului

Adaugă un singur folder `SD/` în rădăcina fork-ului și ține toate soluțiile în
el:

```text
SD/
  README.md
  .gitignore
  lab-1/
    README.md
    assets/      (opțional)
  lab-2/
  lab-3/
  lab-4/
  lab-5/
  lab-6/
  lab-7/
  lab-8/
```

- Câte un folder pentru fiecare laborator, numit de la `lab-1` la `lab-8`.
- Fiecare folder de laborator conține exact un fișier `README.md`. Acel fișier
  este soluția ta. GitHub îl afișează imediat ce cineva deschide folderul,
  deci munca ta se vede fără niciun click.
- Fișierul `SD/README.md` este altul: el precizează numele tău, grupa, limba
  aleasă și conține link către fiecare folder de laborator.
- Folderul `lab-N/assets/` este opțional. Creează-l doar dacă păstrezi
  fișiere cu capturi de ecran lângă o soluție (vezi secțiunea 4).

Fork-ul tău conține și `labs/`, `lectures/` și fișierul `README.md` din
rădăcină. Acestea vin din acest repository — nu le modifica. Tot ce scrii tu
stă în `SD/`, astfel încât o sincronizare nu poate intra niciodată în conflict
cu munca ta.

## 3. Ordinea și ritmul prezentărilor

- **Cel mult două laboratoare pe sesiune.** O sesiune nu acoperă niciodată
  trei.
- Laboratoarele se prezintă în ordine. Nu poți prezenta `lab-4` înainte de
  `lab-3`.

| Etapă | Ce se prezintă    |
| ----- | ----------------- |
| 1     | Laboratoarele 1-3 |
| 2     | Evaluarea 1       |
| 3     | Laboratoarele 4-6 |
| 4     | Evaluarea 2       |
| 5     | Laboratoarele 7-8 |

Un laborator se prezintă doar după ce fișierul lui `README.md` este comis și
urcat. Ce este comis în timpul prezentării sau după ea nu se ia în calcul
pentru acea sesiune.

## 4. Cercetarea trebuie demonstrată explicit

Nu este suficient să afirmi că ai făcut cercetare. Demonstreaz-o într-unul din
două moduri:

- prin **capturi de ecran**, fie inserate direct în `README.md`, fie comise
  în `SD/lab-N/assets/` și referite din el, sau
- printr-o **demonstrație live** în timpul prezentării.

Pentru fiecare sursă, indică URL-ul. Pentru fiecare laborator care cere
cercetare, precizează ce decizie despre scop a fost schimbată sau confirmată de
acea cercetare.

## 5. Un laborator se consideră prezentat când

- [ ] Fișierul lui `README.md` există în folderul corect și este urcat în
      fork-ul tău public.
- [ ] Conține fiecare secțiune cerută de fișierul `README.md` al acelui
      laborator.
- [ ] Cercetarea este demonstrată prin capturi de ecran sau live.
- [ ] Poți răspunde la întrebări despre deciziile din el.

## 6. Limba

Poți lucra în engleză sau în română. Nicio alegere nu relaxează vreo regulă:
ambele versiuni ale acestui document descriu aceleași cerințe.

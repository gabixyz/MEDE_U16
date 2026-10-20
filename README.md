# MEDE - U16 - CP1

Gabriel-Alex Ciocan | 20.10.2026

Repository public: https://github.com/gabixyz/MEDE_U16

## Intrebare de cercetare
In ce masura regimul de operare mascheaza degradarea motoarelor si ce
tipare devin vizibile dupa normalizarea masuratorilor pe regim?

Directia CP0 este pastrata. In Moodle, CP0 apare predat, fara nota sau
comentarii la verificarea din 19.10.2026; validarea profesorului nu este
presupusa. Fereastra de 30 de cicluri ramane o ipoteza pentru CP2, nu un
parametru implementat ori justificat in CP1.

## Rulare de la zero
Este necesar minim Python 3.10. Loader-ul si testele nu au
dependente externe. Clonati repository-ul, apoi rulati:

```powershell
git clone https://github.com/gabixyz/MEDE_U16.git
Set-Location MEDE_U16
python -m unittest discover -s tests -v
python src\load_fd002.py --download
```

Daca endpoint-ul NASA nu mai este disponibil, nu se schimba automat
sursa si nu se ignora eroarea. Copiati cele trei fisiere originale din
arhiva `CMAPSSData.zip` in `data\raw`, apoi rulati:

```powershell
python src\load_fd002.py
```

Alternativ, folositi un director existent:

```powershell
python src\load_fd002.py --raw-dir "C:\cale\catre\MEDE_U16_date"
```

## Intrari si provenienta
NASA Ames PCoE, C-MAPSS legacy FD002; nu N-CMAPSS si nu datasetul
distinct PHM08 challenge. Download original:
https://data.nasa.gov/docs/legacy/CMAPSSData.zip

Pagina oficiala actuala:
https://www.nasa.gov/intelligent-systems-division/discovery-and-systems-health/pcoe/pcoe-data-set-repository/

Fisiere necesare: `train_FD002.txt`, `test_FD002.txt`, `RUL_FD002.txt`.
Versiunea CP1 este fixata prin SHA-256 in `src\load_fd002.py` si in
`outputs\quality_report.json`. Nu se descarca si nu se publica automat
documente sau date brute in Git.

## Doua surse complementare, un singur dataset
- **S1:** coloanele 6-26, 21 de senzori.
- **S2:** coloanele 3-5, trei setari de operare.

Cheia comuna este `(split, engine_id, cycle)`. ID-urile din train si test
nu identifica aceleasi motoare. Sunt doua perspective deja co-indexate,
nu doua colectii independente. Cele sase regimuri provin din documentatia
FD002; CP1 nu a construit inca etichete discrete de regim.

## Iesiri

| Fisiere generate local | Continut |
| --- | --- |
| `outputs\train_sensors.csv`, `outputs\test_sensors.csv` | Cheia + 21 senzori |
| `outputs\train_operating.csv`, `outputs\test_operating.csv` | Cheia + 3 setari |
| `outputs\evaluation_only_test_rul.csv` | Etichete RUL la ultimul ciclu observat din test, pastrate separat de ambele perspective |
| `outputs\quality_report.json` | Acoperire, intervale, lungimi, schema, controale si hash-uri |

## Controlul datelor
Se verifica numarul exact de campuri, valori finite, ID-uri si cicluri
intregi pozitive, chei unice, cicluri continue in fiecare motor, numarul
etichetelor RUL, hash-urile si volumele FD002. Orice abatere opreste
rularea cu o eroare explicita; nu se imputeaza si nu se sterg randuri.
Traiectoriile test se termina intentionat inaintea defectarii.

## Limite si etape viitoare
Date simulate; fara coordonate si fara calendar ori durata in secunde
per ciclu. Lipsa golurilor in fisiere nu implica lipsa zgomotului sau
ca traiectoriile test sunt complete. Unitati S1: vezi `docs\datacard_U16.pdf`;
unitatile setarilor S2 nu sunt explicite in readme si nu sunt ghicite.

CP1 nu pretinde rezultate PCA, clustering, HI sau normalizare.
CP2: aliniere documentata, statistici descriptive si trasaturi pe
ferestre de lungime justificata. Normalizarea si orice transformare se
invata numai pe train; validarea se separa pe motoare, nu pe randuri.
RUL ramane reper de evaluare, nu intrare in explorarea nesupervizata.

## Bibliografie si livrabile

| Fisier | Continut |
| --- | --- |
| `docs\CP1_U16.pdf` | Prezentarea de exact trei slide-uri |
| `docs\datacard_U16.pdf` | Fise S1, S2 si etichete auxiliare |
| `docs\SOA_U16.pdf` | Zece articole, metode, metrici, rezultate si limite |

SOA este o sinteza a rezultatelor publicate, nu o reproducere a lor.
Rezultatele FD001, FD002, N-CMAPSS si t-SNE generic sunt etichetate
separat si nu sunt comparate ca un clasament numeric comun.

## Utilizarea datelor
Repository-ul NASA solicita recunoasterea sursei si a donatorilor si
precizeaza utilizarea pe riscul utilizatorului. Nu a fost identificata
pe pagina verificata o licenta specifica CC0/CC-BY pentru aceste fisiere;
nu se atribuie una prin presupunere. Datele brute sunt excluse din Git.
Codul acestui proiect nu modifica conditiile de utilizare ale datelor.

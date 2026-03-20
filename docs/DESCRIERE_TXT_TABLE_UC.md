# Descrierea Cazurilor de Utilizare - Sistemul EmployeeManager

---

### UC1: Autentificare (Login)
* **Actori:** Angajat, Administrator
* **Obiectiv:** Securizarea accesului in aplicatie pe baza de credentiale si stabilirea rolului.
* **Preconditii:** Utilizatorul are un cont creat in sistem.
* **Postconditii:** Utilizatorul este autentificat si redirectionat catre pagina specifica rolului sau.

| Pas | Actiune Actor | Raspuns Sistem |
| :--- | :--- | :--- |
| 1 | Actorul acceseaza pagina de Login. | Sistemul afiseaza formularul (Email, Parola). |
| 2 | Actorul introduce email-ul si parola si apasa "Login". | Sistemul preia datele, le cripteaza (hash) si interogheaza baza de date. |
| 3 | | Sistemul valideaza credentialele si determina rolul (Admin/User). |
| 4 | | Sistemul redirectioneaza utilizatorul la pagina de start si afiseaza "Autentificare reusita". |

**Scenarii Alternative / Exceptie:**
* **E1 (Credentiale invalide):** La pasul 3, daca datele nu se potrivesc, sistemul afiseaza: "Email sau parola incorecte" si goleste campul parolei.

---

### UC2: Efectuare Pontaj (Check-in/out)
* **Actori:** Angajat
* **Obiectiv:** Inregistrarea prezentei la munca (ora de intrare / iesire).
* **Preconditii:** Angajatul este autentificat in sistem.
* **Postconditii:** Un nou log de timp (TimeLog) este salvat sau actualizat in baza de date.

| Pas | Actiune Actor | Raspuns Sistem |
| :--- | :--- | :--- |
| 1 | Actorul acceseaza sectiunea "Pontaj". | Sistemul afiseaza ora curenta si statusul pontajului (prezent/absent). |
| 2 | Actorul apasa butonul "Check-in" sau "Check-out". | Sistemul preia timestamp-ul curent si il trimite catre baza de date. |
| 3 | | Sistemul actualizeaza tabela `TimeLogs` si afiseaza mesajul "Pontare realizata cu succes". |

**Scenarii Alternative / Exceptie:**
* **A1 (Actiune duplicata):** Daca angajatul este deja pontat la intrare, butonul "Check-in" este dezactivat vizual.

---

### UC3: Vizualizare Istoric Personal
* **Actori:** Angajat
* **Obiectiv:** Vizualizarea orelor lucrate in trecut pentru o anumita perioada.
* **Preconditii:** Angajatul este autentificat.
* **Postconditii:** Datele sunt afisate sub forma tabelara pe ecran.

| Pas | Actiune Actor | Raspuns Sistem |
| :--- | :--- | :--- |
| 1 | Actorul acceseaza "Istoric Pontaj". | Sistemul afiseaza implicit istoricul pe luna curenta. |
| 2 | Actorul selecteaza o alta luna/an din filtru. | Sistemul interogheaza BD pentru `EmployeeId` si perioada selectata. |
| 3 | | Sistemul randeaza tabelul cu zilele, orele de intrare/iesire si totalul orelor calculate. |

**Scenarii Alternative / Exceptie:**
* **A1 (Lipsa date):** La pasul 3, daca nu exista pontaje, sistemul afiseaza mesajul "Nu exista inregistrari pentru perioada selectata".

---

### UC4: Trimitere Cerere Concediu
* **Actori:** Angajat
* **Obiectiv:** Solicitarea de zile libere catre administratie.
* **Preconditii:** Angajatul este autentificat.
* **Postconditii:** O cerere cu status "Pending" este salvata in tabela `TimeOffs`.

| Pas | Actiune Actor | Raspuns Sistem |
| :--- | :--- | :--- |
| 1 | Actorul acceseaza formularul "Cerere Concediu".| Sistemul afiseaza tipurile de concediu si un selector de calendar. |
| 2 | Actorul alege perioada, tipul si apasa "Trimite".| Sistemul verifica logica de business (daca perioada este in viitor). |
| 3 | | Sistemul salveaza cererea si afiseaza "Cerere trimisa spre aprobare". |

**Scenarii Alternative / Exceptie:**
* **E1 (Date invalide):** Daca data de inceput este mai mare decat data de sfarsit, sistemul blocheaza trimiterea si afiseaza o eroare de validare.

---

### UC5: Gestionare Angajati (CRUD)
* **Actori:** Administrator
* **Obiectiv:** Adaugarea, modificarea sau stergerea datelor angajatilor si departamentelor.
* **Preconditii:** Administratorul este autentificat.
* **Postconditii:** Modificarile sunt persistate in tabela `Employees` sau `Departments`.

| Pas | Actiune Actor | Raspuns Sistem |
| :--- | :--- | :--- |
| 1 | Admin-ul navigheaza la "Gestiune Angajati". | Sistemul afiseaza lista completa a angajatilor. |
| 2 | Admin-ul apasa "Adauga Angajat", completeaza datele si da "Salveaza". | Sistemul valideaza unicitatea email-ului. |
| 3 | | Sistemul insereaza noul angajat in baza de date si reincarca lista. |

**Scenarii Alternative / Exceptie:**
* **E1 (Email duplicat):** La pasul 2, daca email-ul introdus exista deja, sistemul afiseaza eroarea: "Un utilizator cu acest email exista deja."

---

### UC6: Import Date (JSON)
* **Actori:** Administrator
* **Obiectiv:** Popularea automata a bazei de date cu angajati folosind un fisier JSON.
* **Preconditii:** Administratorul este autentificat si detine un fisier JSON formatat corect.
* **Postconditii:** Baza de date este populata cu datele din fisier.

| Pas | Actiune Actor | Raspuns Sistem |
| :--- | :--- | :--- |
| 1 | Admin-ul acceseaza "Import JSON". | Sistemul afiseaza zona de upload. |
| 2 | Admin-ul incarca fisierul si confirma. | Sistemul citeste fisierul folosind `System.Text.Json` si valideaza structura. |
| 3 | | Sistemul parcurge colectia, salveaza noile inregistrari in tabelele corespunzatoare. |
| 4 | | Sistemul afiseaza raportul: "Import reusit pentru [X] inregistrari". |

**Scenarii Alternative / Exceptie:**
* **A1 (JSON Invalid):** La pasul 2, daca fisierul este corupt, sistemul afiseaza "Eroare: Format JSON invalid".

---

### UC7: Generare Rapoarte / Export
* **Actori:** Administrator
* **Obiectiv:** Descarcarea datelor de pontaj in formate externe.
* **Preconditii:** Administratorul este autentificat.
* **Postconditii:** Sistemul returneaza un fisier descarcabil.

| Pas | Actiune Actor | Raspuns Sistem |
| :--- | :--- | :--- |
| 1 | Admin-ul navigheaza la "Rapoarte". | Sistemul afiseaza filtrele (Departament, Perioada). |
| 2 | Admin-ul selecteaza filtrele si apasa "Export CSV".| Sistemul aduna datele filtrate din baza de date. |
| 3 | | Sistemul formateaza datele ca text (CSV) si trimite fisierul catre browser spre descarcare. |

**Scenarii Alternative / Exceptie:**
* **A1 (Raport gol):** La pasul 2, daca filtrele aplicate nu produc rezultate, butonul de export returneaza mesajul "Nu exista date de exportat pentru aceste filtre".

---

### UC8: Vizualizare Dashboard Statistici
* **Actori:** Administrator
* **Obiectiv:** Vizualizarea rapida a starii generale a companiei (angajati prezenti, total ore).
* **Preconditii:** Administratorul este autentificat.
* **Postconditii:** Un panou cu metrice agregate este randeat pe ecran.

| Pas | Actiune Actor | Raspuns Sistem |
| :--- | :--- | :--- |
| 1 | Admin-ul acceseaza "Dashboard". | Sistemul initiaza interogari agregate in DB (`Count` pentru prezenti, `Sum` pentru ore lucrate pe departament). |
| 2 | | Sistemul calculeaza rezultatele si le transmite interfetei grafice. |
| 3 | | Sistemul afiseaza graficele si valorile numerice. |

**Scenarii Alternative / Exceptie:**
* **E1 (Timp de raspuns ridicat):** Daca interogarea datelor dureaza mult, la pasul 2 sistemul afiseaza un "Loading spinner" pana cand datele devin disponibile.
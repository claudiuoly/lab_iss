# Descrierea Cazurilor de Utilizare - Sistemul EmployeeManager

---

### UC1: Autentificare (Login)
| Nume | UC1: Autentificare (Login) |
| :--- | :--- |
| Actori participanti | Angajat, Administrator |
| Flux de evenimente (scenariu normal) | 1. Actorul acceseaza pagina de Login.<br>2. Sistemul afiseaza formularul (Email, Parola).<br>3. Actorul introduce email-ul si parola si apasa "Login".<br>4. Sistemul preia datele, le cripteaza (hash) si interogheaza baza de date.<br>5. Sistemul valideaza credentialele si determina rolul (Admin/User).<br>6. Sistemul redirectioneaza utilizatorul la pagina de start si afiseaza "Autentificare reusita". |
| Scenarii alternative | E1 (Credentiale invalide):<br>5a. Daca datele nu se potrivesc, sistemul afiseaza: "Email sau parola incorecte" si goleste campul parolei. |
| Obiectiv | Securizarea accesului in aplicatie pe baza de credentiale si stabilirea rolului. |
| Preconditii | Utilizatorul are un cont creat in sistem. |
| Postconditii | Utilizatorul este autentificat si redirectionat catre pagina specifica rolului sau. |

---

### UC2: Efectuare Pontaj (Check-in/out)
| Nume | UC2: Efectuare Pontaj (Check-in/out) |
| :--- | :--- |
| Actori participanti | Angajat |
| Flux de evenimente (scenariu normal) | 1. Actorul acceseaza sectiunea "Pontaj".<br>2. Sistemul afiseaza ora curenta si statusul pontajului (prezent/absent).<br>3. Actorul apasa butonul "Check-in" sau "Check-out".<br>4. Sistemul preia timestamp-ul curent si il trimite catre baza de date.<br>5. Sistemul actualizeaza tabela `TimeLogs` si afiseaza mesajul "Pontare realizata cu succes". |
| Scenarii alternative | A1 (Actiune duplicata):<br>3a. Daca angajatul este deja pontat la intrare, butonul "Check-in" este dezactivat vizual. |
| Obiectiv | Inregistrarea prezentei la munca (ora de intrare / iesire). |
| Preconditii | Angajatul este autentificat in sistem. |
| Postconditii | Un nou log de timp (TimeLog) este salvat sau actualizat in baza de date. |

---

### UC3: Vizualizare Istoric Personal
| Nume | UC3: Vizualizare Istoric Personal |
| :--- | :--- |
| Actori participanti | Angajat |
| Flux de evenimente (scenariu normal) | 1. Actorul acceseaza "Istoric Pontaj".<br>2. Sistemul afiseaza implicit istoricul pe luna curenta.<br>3. Actorul selecteaza o alta luna/an din filtru.<br>4. Sistemul interogheaza BD pentru `EmployeeId` si perioada selectata.<br>5. Sistemul randeaza tabelul cu zilele, orele de intrare/iesire si totalul orelor calculate. |
| Scenarii alternative | A1 (Lipsa date):<br>5a. Daca nu exista pontaje, sistemul afiseaza mesajul "Nu exista inregistrari pentru perioada selectata". |
| Obiectiv | Vizualizarea orelor lucrate in trecut pentru o anumita perioada. |
| Preconditii | Angajatul este autentificat. |
| Postconditii | Datele sunt afisate sub forma tabelara pe ecran. |

---

### UC4: Trimitere Cerere Concediu
| Nume | UC4: Trimitere Cerere Concediu |
| :--- | :--- |
| Actori participanti | Angajat |
| Flux de evenimente (scenariu normal) | 1. Actorul acceseaza formularul "Cerere Concediu".<br>2. Sistemul afiseaza tipurile de concediu si un selector de calendar.<br>3. Actorul alege perioada, tipul si apasa "Trimite".<br>4. Sistemul verifica logica de business (daca perioada este in viitor).<br>5. Sistemul salveaza cererea si afiseaza "Cerere trimisa spre aprobare". |
| Scenarii alternative | E1 (Date invalide):<br>3a. Daca data de inceput este mai mare decat data de sfarsit, sistemul blocheaza trimiterea si afiseaza o eroare de validare. |
| Obiectiv | Solicitarea de zile libere catre administratie. |
| Preconditii | Angajatul este autentificat. |
| Postconditii | O cerere cu status "Pending" este salvata in tabela `TimeOffs`. |

---

### UC5: Gestionare Angajati (CRUD)
| Nume | UC5: Gestionare Angajati (CRUD) |
| :--- | :--- |
| Actori participanti | Administrator |
| Flux de evenimente (scenariu normal) | 1. Admin-ul navigheaza la "Gestiune Angajati".<br>2. Sistemul afiseaza lista completa a angajatilor.<br>3. Admin-ul apasa "Adauga Angajat", completeaza datele si da "Salveaza".<br>4. Sistemul valideaza unicitatea email-ului.<br>5. Sistemul insereaza noul angajat in baza de date si reincarca lista. |
| Scenarii alternative | E1 (Email duplicat):<br>4a. Daca email-ul introdus exista deja, sistemul afiseaza eroarea: "Un utilizator cu acest email exista deja." |
| Obiectiv | Adaugarea, modificarea sau stergerea datelor angajatilor si departamentelor. |
| Preconditii | Administratorul este autentificat. |
| Postconditii | Modificarile sunt persistate in tabela `Employees` sau `Departments`. |

---

### UC6: Import Date (JSON)
| Nume | UC6: Import Date (JSON) |
| :--- | :--- |
| Actori participanti | Administrator |
| Flux de evenimente (scenariu normal) | 1. Admin-ul acceseaza "Import JSON".<br>2. Sistemul afiseaza zona de upload.<br>3. Admin-ul incarca fisierul si confirma.<br>4. Sistemul citeste fisierul folosind `System.Text.Json` si valideaza structura.<br>5. Sistemul parcurge colectia, salveaza noile inregistrari in tabelele corespunzatoare.<br>6. Sistemul afiseaza raportul: "Import reusit pentru [X] inregistrari". |
| Scenarii alternative | A1 (JSON invalid):<br>4a. Daca fisierul este corupt, sistemul afiseaza "Eroare: Format JSON invalid". |
| Obiectiv | Popularea automata a bazei de date cu angajati folosind un fisier JSON. |
| Preconditii | Administratorul este autentificat si detine un fisier JSON formatat corect. |
| Postconditii | Baza de date este populata cu datele din fisier. |

---

### UC7: Generare Rapoarte / Export
| Nume | UC7: Generare Rapoarte / Export |
| :--- | :--- |
| Actori participanti | Administrator |
| Flux de evenimente (scenariu normal) | 1. Admin-ul navigheaza la "Rapoarte".<br>2. Sistemul afiseaza filtrele (Departament, Perioada).<br>3. Admin-ul selecteaza filtrele si apasa "Export CSV".<br>4. Sistemul aduna datele filtrate din baza de date.<br>5. Sistemul formateaza datele ca text (CSV) si trimite fisierul catre browser spre descarcare. |
| Scenarii alternative | A1 (Raport gol):<br>4a. Daca filtrele aplicate nu produc rezultate, butonul de export returneaza mesajul "Nu exista date de exportat pentru aceste filtre". |
| Obiectiv | Descarcarea datelor de pontaj in formate externe. |
| Preconditii | Administratorul este autentificat. |
| Postconditii | Sistemul returneaza un fisier descarcabil. |

---

### UC8: Vizualizare Dashboard Statistici
| Nume | UC8: Vizualizare Dashboard Statistici |
| :--- | :--- |
| Actori participanti | Administrator |
| Flux de evenimente (scenariu normal) | 1. Admin-ul acceseaza "Dashboard".<br>2. Sistemul initiaza interogari agregate in DB (`Count` pentru prezenti, `Sum` pentru ore lucrate pe departament).<br>3. Sistemul calculeaza rezultatele si le transmite interfetei grafice.<br>4. Sistemul afiseaza graficele si valorile numerice. |
| Scenarii alternative | E1 (Timp de raspuns ridicat):<br>3a. Daca interogarea datelor dureaza mult, sistemul afiseaza un "Loading spinner" pana cand datele devin disponibile. |
| Obiectiv | Vizualizarea rapida a starii generale a companiei (angajati prezenti, total ore). |
| Preconditii | Administratorul este autentificat. |
| Postconditii | Un panou cu metrice agregate este randeat pe ecran. |
# EmployeeManager - Project Specification

**Project Code:** EMP-TRACK (Employee Management & Time Tracking)

## Overview

EmployeeManager este un sistem web-based conceput pentru eficientizarea proceselor de resurse umane, axat pe gestionarea inregistrarilor angajatilor si monitorizarea pontajului (check-in/check-out). Sistemul este complet containerizat folosind Docker si utilizeaza o baza de date PostgreSQL pentru persistenta.

## Scope

Acesta este un proiect universitar care se concentreaza pe functionalitatile de baza ale managementului angajatilor. Sistemul pune accent pe automatizarea calculului orelor lucrate, gestionarea cererilor de concediu si oferirea unor statistici vizuale pentru administratori prin intermediul unui dashboard.

## Features

### F1. Gestiunea Angajatilor (CRUD)
- **Administrare Completa**: Administratorul poate adauga, edita sau sterge inregistrari ale angajatilor (Nume, Email, Departament, Salariu).
- **Import Date**: Posibilitatea de a popula baza de date in masa prin importul de fisiere JSON.
- **Fara Stergere Fizica**: Se recomanda marcarea angajatilor ca inactivi pentru a pastra integritatea istoricului de pontaj.

### F2. Autentificare si Autorizare
- **Login Securizat**: Utilizatorii acceseaza sistemul pe baza de email si parola.
- **Roluri (RBAC)**:
    - **Administrator**: Acces total la gestiune, importuri si rapoarte.
    - **Angajat**: Acces limitat la propriul profil, pontaj si istoric personal.

### F3. Modul de Pontaj (Time Tracking)
- **Check-in/Check-out**: Angajatii pot marca inceputul si sfarsitul programului de lucru printr-un singur click.
- **Pontare Manuala**: Posibilitatea de a introduce manual intervale de timp in cazul in care s-a omis pontarea in timp real (necesita aprobare sau logare separata).
- **Calcul Automat**: Sistemul calculeaza in timp real numarul de ore lucrate per sesiune si per zi.

### F4. Istoric si Filtrare
- **Vizualizare Tabelara**: Angajatii isi pot vedea propriile pontaje intr-un tabel organizat.
- **Filtrare Avansata**: Posibilitatea de a filtra datele dupa perioada (zi, luna) sau departament (pentru admin).

### F5. Dashboard si Statistici
- **Vizualizare Rapida**: O pagina dedicata administratorului care afiseaza:
    - Numarul de angajati prezenti in ziua curenta.
    - Totalul de ore lucrate grupate pe departamente.
    - Grafice simple de activitate.

### F6. Cereri de Concediu
- **Flux de Solicitare**: Angajatii pot trimite cereri pentru zile libere, specificand perioada si tipul (concediu de odihna, medical etc.).
- **Status Cerere**: Urmarirea stadiului cererii (In asteptare, Aprobat, Respins).

## Non-Functional Requirements

- **Usability**: Interfata simpla si intuitiva construita cu ASP.NET Core MVC.
- **Securitate**: Parolele sunt stocate sub forma de hash (PasswordHash) in baza de date.
- **Performanta**: Dashboard-ul trebuie sa calculeze statisticile in mai putin de 1 secunda.
- **Containerizare**: Intreg stack-ul (Aplicatie + Postgres) trebuie sa porneasca folosind Docker Compose.
- **Integritate**: Validarea fisierelor JSON la import pentru a preveni coruperea datelor.
- **Arhitectura**: Aplicatia va respecta un model stratificat (Layered Architecture):
    - **Presentation Layer**: Interfata Web MVC.
    - **Business Layer**: Logica de calcul a orelor si validarea cererilor.
    - **Data Layer**: Accesul la Postgres prin Entity Framework Core (ORM).

## Technical Constraints

- **Limbaj**: C# (.NET 10).
- **Baza de date**: PostgreSQL.
- **ORM**: Entity Framework Core.
- **Deployment**: Docker & Docker Compose.
- **UML Modeling**: Crearea diagramelor de cazuri de utilizare si de clase inainte de implementare.

## Project Planning (3 Iterations)

Planificarea proiectului este impartita in 3 iteratii incrementale:

| Iteratie | Obiectiv Principal | Functionalitati Incluse (Use Cases) |
| :--- | :--- | :--- |
| **I1** | **Fundatia si Gestiunea** | Setup Docker/DB, Autentificare (UC1), CRUD Angajati (UC5), Import JSON (UC6). |
| **I2** | **Logica de Pontaj** | Modulul de Check-in/out (UC2), Calcul automat ore, Vizualizare Istoric (UC3). |
| **I3** | **Analiza si Concedii** | Dashboard Statistici (UC8), Export Rapoarte (UC7), Cereri Concediu (UC4). |
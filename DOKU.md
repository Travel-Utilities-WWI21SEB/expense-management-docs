### Dokumentation

## Projektidee
Da ein Teil unseres Teams im nächsten Praxissemester gemeinsam im Ausland arbeiten wird, haben wir uns entschieden, eine 
Anwendungen zum Tracken und Auteilen von Kosten beim gemeinsames Reisen zu entwickeln. In der Anwendung sollen Reisen und 
Kosten angelegt werden können und ein Überblick über offene Schulden und Forderungen gegeben werden.
## Anforderungen
* Nutzer sollen sich registrieren und authentisiert werden können.
* Nutzer sollen Reisen anlegen und andere Nutzer zu einer Reise einladen können.
* Nutzer sollen Kosten für eine Reise anlegen können. Kosten können auf mehrere Nutzer aufgeteilt werden. 
Hieraus werden automatisch Schulden/Forderungen errechnet.
* Kosten werden Kategorien zugeordnet. In einem Kreisdiagramm wird eine Übersicht über die bisher angefallenen Kosten pro Kategorie angezeigt.

## Architektur

![ArchitectureTravel](https://github.com/Travel-Utilities-WWI21SEB/expense-management-docs/assets/74607524/91993824-d6a0-4422-b3f4-85d9a57f61c9)

## Auth Flow

![authorizationSequence](https://github.com/Travel-Utilities-WWI21SEB/expense-management-docs/assets/74607524/65278270-343e-4281-93bf-cddcaee678a4)

## Aufteilung
Aidan (4803747): Trips anlegen/bearbeiten, Tripübersicht, Transaktionshistorie

Johanna (4940972): Trip-Details - Kosten, Schulden, Transaktionen (anlegen, bearbeiten, Übersicht)

Kevin (8110106): Login, Registrierung (Frontend, Backend), Landingpage, generelles Layout, Cost-/Debt-Overview, Profil, Homepage

Lisa (3798263): Translations, Homepage

Luca (3047210): Go-Backend (DB, Logik, API-Schnittstelle)

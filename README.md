# expense-management-docs

## **Lokales Aufsetzen**

Im folgenden Abschnitt werden zwei Methoden erläutert, um die komplette Infrastruktur für dieses Projekt aufzusetzen. Die erste Methode benutzt hierfür Docker und ist die präferierte Methode, da dies unserem Produktiv-System am nähesten kommt. Für die lokale Entwicklung bietet sich das Aufsetzen ohne Docker an.

### **Mit Docker**

Um die Umgebung zusammen mit Docker aufzusetzen, wird folgende Ordner-Struktur vorausgesetzt:

- > Root-Verzeichnis (Oberverzeichnis)
  - > expense-management-service (Backend-Service)
    - .env (Umgebungsvariablen für das Backend)
    - ...
  - > expense-management-ui (Frontend-Service)
    - .env.production (Umgebungsvariablen für das Frontend)
    - ...
  - docker-compose.yml (Container aufsetzen)
  - init.sql (Datenbankschema)

Die Inhalte dieser Dateien wird im Folgenden Schritt für Schritt erklärt:

#### **expense-management-service**

In diesem Ordner sollte sich das Backend-Repository befinden, welche man über [Github](https://github.com/Travel-Utilities-WWI21SEB/expense-management-service) auf die präferierte Weise clonen kann. 

##### **.env**

In dieser Datei sollten sich die nötigen Umgebungsvariablen für das Backend befinden, diese sind in der [README](https://github.com/Travel-Utilities-WWI21SEB/expense-management-service/blob/main/README.md) des Backend-Repositories beschrieben.

#### **expense-management-ui**

In diesem Ordner sollte sich das Frontend-Repository befinden, welche man über [Github](https://github.com/Travel-Utilities-WWI21SEB/expense-management-ui) auf die präferierte Weise clonen kann. 

##### **.env**

In dieser Datei sollten sich die nötigen Umgebungsvariablen für das Frontend befinden, diese sind in der [README](https://github.com/Travel-Utilities-WWI21SEB/expense-management-ui/blob/main/README.md) des Frontend-Repositories beschrieben (noch nicht, deswegen hier kurze Erklärung).

```txt
PUBLIC_BASE_URL=http://expense-api-container:8080
```

Dies setzt die BASE_URL für die Kommunikation mit dem Backend direkt auf den Container-Namen, welcher im internen Docker-Netzwerk effizient aufgelöst wird.

#### **docker-compose.yml**

In dieser Datei wird die Orchestrierung für das Starten der Container vorgenommen, insgesamt werden vier Services gestartet, diese sind:

| **Service** | **Image** | **Beschreibung** | **Container-Name** |
| ----------- | --------- | ---------------- | ------------------ |
| Datenbank | postgres:15.3-alpine | Postgresql-Cluster und Datenbank | expense-db-container
| PGAdmin | dpage/pgadmin4 | Dient dazu, die Datenbank visuell darzustellen (optional) | travelutilities-pgadmin-1
| Backend-Service | expense-api | Backend-Service auf Alpine Base Image | expense-api-container |
| Frontend-Service | expense-ui | Frontend-Service auf Slim-Node Base Image | expense-ui-container |

Die `docker-compose.yml` sieht wie folgt aus:

```yml
version: '3'
services:
  # POSTGRES DATABASE
  expense-db:
    image: postgres:15.3-alpine
    container_name: expense-db-container
    restart: always
    ports:
      - 5432:5432
    networks:
      - default
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: password
      POSTGRES_DB: travel_expenses-db
    volumes:
      - ./init.sql:/docker-entrypoint-initdb.d/docker_postgres_init.sql
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "admin", "-d", "travel_expenses-db"]
      interval: 5s
      timeout: 5s
      retries: 5
    
  # PG ADMIN
  pgadmin:
    image: dpage/pgadmin4
    environment: 
        PGADMIN_DEFAULT_EMAIL: "admin@admin.de"
        PGADMIN_DEFAULT_PASSWORD: "admin1234!"
    ports: 
        - "16543:80"
    depends_on: 
      expense-db:
        condition: service_healthy

  # BACKEND SERVICE
  expense-api:
    image: expense-api
    container_name: expense-api-container
    depends_on:
      expense-db:
        condition: service_healthy
    ports:
      - 8080:8080
    networks:
      - default
    env_file:
      - expense-management-service/.env
    healthcheck:
      test: ["CMD", "curl", "-f", "expense-api-container:8080/lifecheck"]
      interval: 15s
      start_period: 10s
      timeout: 10s
      retries: 4

  # IMAGES SERVICE
  expense-images:
    image: expense-images
    container_name: travel_expenses_images
    depends_on:
      expense-db:
        condition: service_healthy
    ports:
      - 8082:8082
    networks:
      - default
    env_file:
      - expense-management-service/.env
    healthcheck:
      test: ["CMD", "curl", "-f", "travel_expenses_images:8080/lifecheck"]
      interval: 15s
      start_period: 10s
      timeout: 10s
      retries: 4
  
  # FRONTEND-SERVICE
  expense-ui:
    image: expense-ui
    container_name: expense-ui-container
    depends_on:
      expense-api:
        condition: service_healthy
    ports:
      - 4174:3000
    networks:
      - default
```

Mit dem Befehl `docker compose up` werden die Container gestartet, sobald das UI fertig geladen ist, sind alle Services betriebsbereit und können genutzt werden. 

NOTE: Damit `docker compose up` funktioniert, müssen vorher die jeweiligen Images im UI und im Backend gebaut werden. Für das UI kann dies über den Befehl `pnpm docker:build` ausgeführt werden, im Backend muss der Befehl `docker build . -t expense-api` ausgeführt werden. Ebenfalls muss Docker Desktop auf der Maschine installiert sein, auf der der Befehl ausgeführt werden soll.

#### **init.sql**

In dieser Datei sollte sich das aktuelle Datenbankschema befinden, damit beim Ausführen des Befehls `docker compose up` das aktuelle Datenbankschema initial geladen wird. Dieses wird gepflegt und ist im [Backend-Repository](https://github.com/Travel-Utilities-WWI21SEB/expense-management-service/blob/main/expenses_db.sql) zu finden.

### **Ohne Docker**

Um die Services ohne Docker aufzusetzen, muss jeder Service nacheinander aufgesetzt werden.

#### **Backend**

Das Aufsetzen des Backends wird in der [README](https://github.com/Travel-Utilities-WWI21SEB/expense-management-service/blob/main/README.md) des Backend-Repositories beschrieben.

### **Image Service**
Das Aufsetzen des Image Services wird in der [README](https://github.com/Travel-Utilities-WWI21SEB/expense-management-service/blob/main/README.md) des Image Service-Repositories beschrieben.

#### **Frontend**

Das Aufsetzen des Frontends wird in der [README](https://github.com/Travel-Utilities-WWI21SEB/expense-management-ui/blob/main/README.md) des Frontend-Repositories beschrieben.

#### **Datenbank**

##### MacOS

Vorraussetzung hierfür ist `brew`, sollte das auf der Maschine nicht installiert sein, ist dir sowieso nicht mehr zu helfen (Brew kann mit `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"` installiert werden). Sollte `brew` installiert sein, kann mit folgendem Befehl Postgresql 15 installiert werden: 

```bash
brew install postgresql@15
```

Bei erfolgreicher Installation muss der `PATH` angepasst werden, die jeweiligen Befehle sind aus dem Installations-Log von `brew` zu entnehmen. Das Postgresql-Cluster kann ebenfalls über `brew` gestartet werden:

```bash
brew services start postgresql@15
```

Für die nächsten Schritte werden die Werte aus der .env-Datei im [Backend-Repository](https://github.com/Travel-Utilities-WWI21SEB/expense-management-service/blob/main/README.md#developing) benutzt, solltest du andere Werte benutzen wollen, pass bitte sowohl die env-Datei, als auch die Werte in den folgenden Befehlen an.

1. **User erstellen**

Im folgenden werden nacheinander die Befehle aufgezählt, um einen neuen User in Postgresql zu erstellen.

```bash
psql postgres

<!-- Postgres-Terminal -->
CREATE ROLE admin WITH LOGIN PASSWORD ‘password’;
ALTER ROLE admin CREATEDB;
\q
```

2. Datenbank erstellen

Nun soll mit dem neuen User eine Datenbank erstellt werden.

```bash
psql postgres -U admin

<!-- Postgres-Terminal -->
CREATE DATABASE travel_expenses-db
```

###### **PGAdmin**

Für eine einfachere Bedienung kann auch PGAdmin installiert werden, eine grafische Benutzeroberfläche mit der man Postgresql-Cluster verwalten kann. Dies lässt sich ebenfalls über `brew` mit folgendem Befehl installieren `brew install --cask pgadmin4`. In PGAdmin selbst muss erst die Verbindung zum Server hergestellt werden, hierfür einfach die Werte aus der [.env-Datei](https://github.com/Travel-Utilities-WWI21SEB/expense-management-service/blob/main/README.md#developing) im Backend übernehmen.

##### **Windows (buh)**

Folgt irgendwann, wenn ich Lust hab das Ganze für Windows zu machen. Für jetzt: quasi das was da oben steht, nur ohne brew.
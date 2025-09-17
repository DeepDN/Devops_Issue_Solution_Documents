# docker-compose file for database

```bash
version: "3.4"
services:
  mediator:
    image: postgres:latest
    command: postgres -c 'max_connections=500'
    volumes:
      - ./mediator-volume/volumes/postgres-data:/postgres/postgres-data:z
      - ./mediator-volume/volumes/postgres-data-backup:/var/lib/postgresql/data

    ports:
      - 5432:5432
    healthcheck:
      test: [ "CMD", "pg_isready", "-q", "-d", "postgres", "-U", "postgres" ]
      timeout: 45s
      interval: 10s
      retries: 10
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=Wi@h5m!qnvA
      - POSTGRES_DB=postgres
  wallet-db:
    image: postgres:latest
    command: postgres -c 'max_connections=500'
    volumes:
      - ./wallet-volume/volumes/postgres-data:/postgres/postgres-data:z
      - ./wallet-volume/volumes/postgres-data-backup:/var/lib/postgresql/data
    ports:
      - 5452:5432
    healthcheck:
      test: [ "CMD", "pg_isready", "-q", "-d", "postgres", "-U", "platform-admin" ]
      timeout: 45s
      interval: 10s
      retries: 10
    restart: always
    environment:
      - POSTGRES_USER=platform-admin
      - POSTGRES_PASSWORD=Wi@h5m!qnvA
      - POSTGRES_INITDB_ARGS=--max_connections=1000
  Platform-db:
    image: postgres:latest
    command: postgres -c 'max_connections=500'
    volumes:
      - ./platform-volume/volumes/postgres-data:/postgres/postgres-data:z
      - ./platform-volume/volumes/postgres-data-backup:/var/lib/postgresql/data
    ports:
      - 5450:5432
    healthcheck:
      test: [ "CMD", "pg_isready", "-q", "-d", "postgres", "-U", "dev-postgres" ]
      timeout: 45s
      interval: 10s
      retries: 10
    restart: always
    environment:
      - POSTGRES_USER=dev-postgres
      - POSTGRES_PASSWORD=D7qVgCEYPetZRqnQ
      - POSTGRES_INITDB_ARGS=--max_connections=1000
 Keycloak-db:
    image: postgres:latest
    volumes:
      - ./Keycloak-volume/volumes/postgres-data:/postgres/postgres-data:z
      - ./Keycloak-volume/volumes/postgres-data-backup:/var/lib/postgresql/data
    ports:
      - 5450:5451
    healthcheck:
      test: [ "CMD", "pg_isready", "-q", "-d", "postgres", "-U", "postgres" ]
      timeout: 45s
      interval: 10s
      retries: 10
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=D7qVgCEYPetr
      - POSTGRES_DB=keycloak
  redis:
    image: redis:6.2-alpine
    restart: always
    ports:
      - '6379:6379'
    command: redis-server --save 20 1 --loglevel warning --requirepass eYVX7EwVmmxKPCDmwMtyKVge8oLd2t81
    volumes: 
      - cache:/data
volumes:
  mediator:
    driver: local
  cache:
    driver: local
  wallet-db:
    driver: local
  Platform-db
    driver: local
```
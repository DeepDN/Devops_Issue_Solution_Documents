# Databse Access Using Rolebase Authentication

<aside>
 create docker compose

</aside>

```yaml
version: "3.4"
services:
  WalletBackupDB: # Corrected indentation
    image: postgres:latest
    volumes:
      - ./volumes/postgres-data:/postgres/postgres-data:z
    ports:
      - 5452:5432
    healthcheck:
      test: ["CMD", "pg_isready", "-q", "-d", "ndi_wallet_backup_db", "-U", "ndi_super_user"]
      timeout: 45s
      interval: 10s
      retries: 10
    restart: always
    env_file:
      - ./db.env
```

<aside>
 docker compose up

</aside>

```yaml
POSTGRES_USER=ndi_super_user 
POSTGRES_PASSWORD=l9eBMN7BaF33Fll
POSTGRES_DB=ndi_foundation_issuer_db
```

```yaml
docker-compose up -d
docker exec -it id sh

```

## Access container
docker exec -it f5c447b1e3b9 sh

# access databse as super user
psql -U ndi_super_user -d ndi_foundation_issuer_db
psql -U ndi_super_user -d 
# create db role
CREATE ROLE db_role;
# grant permission
GRANT CONNECT ON DATABASE ndi_database_service_db TO db_role;
GRANT USAGE ON SCHEMA public TO db_role;
GRANT SELECT, INSERT, UPDATE ON ALL TABLES IN SCHEMA public TO db_role;
GRANT USAGE ON SCHEMA public TO db_role;
GRANT CREATE ON SCHEMA public TO db_role;

# creste user
CREATE USER ndi_core_user WITH PASSWORD 'lh6moPLzUPFcb4c';
GRANT db_role TO ndi_core_user;

# check oermission
SELECT r.rolname, m.roleid, m.member
FROM pg_roles r
JOIN pg_auth_members m ON r.oid = m.member



# readl only acces to user

# Database commands

<aside>
 check database users

</aside>

```bash
SELECT usename FROM pg_user WHERE usesuper = true;
```

<aside>
 list all the users in a PostgreSQL database along with their details, you can use the following SQL query:

</aside>

```bash
SELECT * FROM pg_user;
```

<aside>
 command to reset the password for the "supabase_admin" user:

</aside>

```bash
ALTER USER supabase_admin PASSWORD 'new_password';
ALTER USER postgres PASSWORD 'Password123;
```

<aside>
 To list all the databases in a PostgreSQL server

</aside>

```bash
\l 
SELECT datname FROM pg_database;
```

<aside>
 no of ideal connections

</aside>

```bash
SELECT count(*) FROM pg_stat_activity WHERE state = 'idle';
```

<aside>
 no of ideal connection with pid

</aside>

```bash
SELECT pg_stat_activity.pid
FROM pg_stat_activity
WHERE state = 'idle' AND usename = 'postgres';
```

<aside>
 delete or terminate single ideal connections

</aside>

```bash
SELECT pg_terminate_backend(pid);
```

<aside>
 delete all idea connection with psql function

</aside>

```bash
#To use the PL/pgSQL script to terminate all idle connections for a specific user in PostgreSQL, follow these steps:
#Open your PostgreSQL client or command-line interface (e.g., psql) and connect to your PostgreSQL database.
#In your client, execute the PL/pgSQL script that was provided in the previous response. You can do this by pasting the script into the PostgreSQL command-line client. For example:

DO $$ 
DECLARE
    conn record;
BEGIN
    FOR conn IN (SELECT pg_stat_activity.pid
                 FROM pg_stat_activity
                 WHERE state = 'idle' AND usename = 'postgres') 
    LOOP
        EXECUTE 'SELECT pg_terminate_backend(' || conn.pid || ')';
    END LOOP;
END $$;
```

<aside>
 delete process if start with particular no

</aside>

```bash
#If you want to terminate connections that have process IDs starting with "60," you can use a PL/pgSQL script to achieve this. Here's how you can do it:

DO $$ 
DECLARE
    conn record;
BEGIN
    FOR conn IN (SELECT pg_stat_activity.pid
                 FROM pg_stat_activity
                 WHERE state = 'idle' AND pg_stat_activity.pid::text LIKE '60%') 
    LOOP
        EXECUTE 'SELECT pg_terminate_backend(' || conn.pid || ')';
    END LOOP;
END $$;
```

<aside>
 check no of ideal connection in postures within time interval

</aside>

```
SELECT COUNT(*) 
FROM pg_stat_activity 
WHERE state = 'idle' 
  AND state_change >= NOW() - INTERVAL '1 day';
```

<aside>
 ideal connection time out

</aside>
```bash
show idle_in_transaction_session_timeout;

```

# SadServers Challenge: "Bucharest"

### Question
A PostgreSQL 13 database exists on the server, but connections to the `app1` database for user `app1user` are failing. Identify and resolve the connection failure.

**Success Criteria:**
`PGPASSWORD=app1user psql -h 127.0.0.1 -d app1 -U app1user -c '\q'` returns no error.

---

### Solution
The issue is caused by "catch-all" `reject` rules at the top of the PostgreSQL Host-Based Authentication (`pg_hba.conf`) file, which override specific `md5` rules defined later.

1.  **Edit the configuration file:**
    ```bash
    sudo nano /etc/postgresql/13/main/pg_hba.conf
    ```
2.  **Remove or comment out** the following lines at the top of the file:
    ```text
    host    all             all             all             reject
    host    all             all             all             reject
    ```
3.  **Restart the service:**
    ```bash
    sudo systemctl restart postgresql
    ```
4.  **Verify:**
    ```bash
    PGPASSWORD=app1user psql -h 127.0.0.1 -d app1 -U app1user -c '\q'
    ```

---

### Key Learning
*   **Order of Precedence:** PostgreSQL processes `pg_hba.conf` from **top to bottom**. The **first** matching record is used; all subsequent records are ignored.
*   **Catch-all Rules:** Placing a broad `reject` rule at the beginning of the file will block all specific `allow` or `md5` rules defined later.

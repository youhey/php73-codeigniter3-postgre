# Apache httpd + PHP 7.3 + Codeigniter 3 + PostgreSQL 9.4

## Step 1. Build

```console
docker-compose build
```

## Step 2. Up

```console
docker-compose up -d
```

## Confirmation

### PHP Info

```console
cat <<'EOF' > phpinfo.php
<?php

phpinfo();

EOF

open http://localhost:8080/phpinfo.php
```

### Show PotgreSQL Tables

```console
cat <<'EOF' > postgresql.php
<?php

$pdo = new PDO('pgsql:host=postgres;port=5432;dbname=postgres;user=postgres;password=secret');
$pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

$sql = <<<SQL
SELECT pg_statio_user_tables.relname
    FROM pg_catalog.pg_class, pg_catalog.pg_statio_user_tables
    WHERE relkind='r'
          AND pg_catalog.pg_statio_user_tables.relid=pg_catalog.pg_class.relfilenode
SQL;

$stmt = $pdo->query($sql);
var_dump($stmt->fetchAll());

EOF

open http://localhost:8080/postgresql.php
```

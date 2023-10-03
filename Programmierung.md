# PostgreSQL Programming Interfaces

## PHP

### pg-Library

- pg_connect
- pg_query
- pg_fetch_arry
- pg_free_sesults
- pg_close

```php
<?php
// Verbindungsaufbau und Auswahl der Datenbank
$dbconn = pg_connect("host=localhost dbname=db01 user=pg password=pgpw")
    or die('Verbindungsaufbau fehlgeschlagen: ' . pg_last_error());
...
```

### pdo-Library

Voraussetzungen:<br>
PHP PDO PostgreSQL driver muss im file php.ini freigeschalten werden

```php
<?php
  ...
  try {
	  $dsn = "pgsql:host=$host;port=5432;dbname=$db;";
	  // make a database connection
	  $pdo = new PDO($dsn, $user, $password, [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]);

	  if ($pdo) {
		  echo "Connected to the $db database successfully!";
	  }
  } catch (PDOException $e) {
	  die($e->getMessage());
  } finally {
	  if ($pdo) {
		  $pdo = null;
	  }
  }
```

### Python

### JavaScript

```javascript
# npm install pg
const { Pool } = require('pg')

const pool = new Pool({
  user: 'postgres',
  host: 'localhost',
  database: 'db01',
  password: 'postgres',
  port: 5432,
})

pool.query('SELECT NOW()', (err, res) => {
  console.log(err, res) 
  pool.end() 
})
```

## Links

- [https://www.php.net/manual/de/pgsql.examples.php](https://www.php.net/manual/de/pgsql.examples.php)
- [https://www.phptutorial.net/php-pdo/](https://www.phptutorial.net/php-pdo/)
- [https://www.cloudsigma.com/connecting-postgresql-with-node-js-applications-a-tutorial/](https://www.cloudsigma.com/connecting-postgresql-with-node-js-applications-a-tutorial/)

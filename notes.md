### About PHP
* `print_r()` can print out array while `echo` cannot
* Obeject oriented PHP
  * Instantiating an Object: `$car1 = new Car('red');`
  * Accessing properties (use an `->`): `echo $car1->color;` the output is red
  * Accessing methods (also use `->`): `$car1->stop();`
* Database Objects
  * Calling an object's method often returns a new object. This usually contains the results of a query or a prepared statement
    * `$result = $db->query($sql);` is calling the database connection object's query method and pass it a select query. It returns an object containing the result set in both PDO and MySQLi.
    * `$row = $result->fetch();`
    * `echo $db->affected_rows;`
  

### PHP with PDO
* Prepared statement
  * About prepared statement
    * Template for SQL query that uses values from user input
    * Placeholders for values stored in variables
    * Prevents SQL injection
    * More efficient when same query is reused
    * Can bind results to named variables
  * Placeholders
    * Can be used only for column values
    * Cannot be used for column names or operators, because values that contain non-numeric characters are automatically escaped and wrapped in quotes. Column names and opertors cannot be in quotes
    * `?` is used as anonymous placeholders in both PDO and MySQLi
    ```
    $sql = 'SELECT user_id, first_name, last_name
           FROM users
           WHERE username = ? AND password = ?';
    ```
    * **named placeholders**
    ```
    $sql = 'SELECT user_id, first_name, last_name
            FROM users
            WHERE username = :username AND password = :pwd';
    ```
      Neither anonymous or named placeholders need quotation marks which makes it a lot easier to build an SQL query because there's no need to worry about getting the correct combination of single and double quotes.
  * Steps to use prepared statements
    * Prepare and validate the SQL with placeholders
    * Bind values to the placeholders
    * Execute the statement
    * Bind output values to variables (optional)
    * Fetch the results
  * Efficiency
    * Repeated Query: 
      * Prepared statements analyzes and optimizes SQL once
      * Values are sent separately
      * Non-prepared statement analyzes SQL every time
    * Single query
      * Prepared statement makes two round-trips to server (first time to validate and optimize the SQL, the second time to send the values for the placeholders)
      * Valid non-prepared statement executes immediately
      * User input still needs to be sanitized
* Transaction
  * About transactions
    * Set of SQL queries executed as a unit
    * Operation is committed only if all parts succeed
    * Transaction can be rolled back if an error occurs
    * Particularly useful for financial transfers where it's vital that all parts of the transaction succeed
    * Prevents rows being modified by another connection
* PDO Basics
  * Database Source Name (DSN)
    * A string that identifies which database to connect to
    * It begins with a prefix indicating the database system you are using. Prefix followed by colon identifies PDO driver
    * Name/value pairs separated by semicolons
    * DSN format depends on the driver
    * Examples:
      * MySQL: `$dsn = 'mysql:host=localhost; dbname=oophp';`. You can spcify the port if it's not standard: `mysql:host=localhost; port=3307; dbname=oophp';`
      * SQLite3: `$dsn = 'sqlite:/path/to/oophp.db';`
      * MS SQL Server: `$dsn = 'sqlsrv:Server=localhost; Database=oophp';`
    * Only code for DSN are different from database to database but other PDO code are the same
  * Looping directly over a SELECT query (see the video or exerices file for example)
    * Note: this only works with `foreach()` loop
  * Fetching a result set
    * `fetch()` gets the next row from a result set
    * `fetchAll()` creates an array containing all rows
    * `fetchColumn()` gets a single column from the next row
    * `fetchObject()` gets the next row as an object
  * Executing simple non-SELECT queries
    * `query()` method returns different values depending on the type of query
      * Returns the result set for SELECT queries
      * Returns a string contains SQL query with INSERT, UPDATE, and DELETE
    * `exec()`
      * Returns the number of rows affected
      * Better for INSERT, UPDATE, and DELETE
      ```
      $sql = 'INSERT INTO names (name, meaning, gender)
              VALUES ("William, "resolute guardian", "boy")';
              
      $affected = $db->exec($sql);
      
      echo $affected . " row inserted with ID ' . $db->lastInsertId();
      
      // output: 1 row inserted with ID 14
      ```
      * Should NEVER be used for SELECT queries because it doesn't return a result set
  * Getting error messages
    * Use `errorInfo()` method. Example: `$errorInfo = $db->errorInfo();`
    
    

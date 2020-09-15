# PHP-MySQL-Tutorial
PHP with MySQL Essential Training from LinkedIn Learning

## Course resources
1. [PHP with MySQL Essential Training: 1 The Basics](https://www.linkedin.com/learning/php-with-mysql-essential-training-1-the-basics/default-values-for-url-parameters?u=76215914)
2. [PHP with MySQL Essential Training: 2 Build a CMS](https://www.linkedin.com/learning/php-with-mysql-essential-training-2-build-a-cms/introduction?u=76215914)

## Notes
### Part 1
#### Build Web Pages with PHP
* Ternary conjunctions:
```
$page = isset($_GET['page']) ? $_GET['page'] : '1';
// This checks if there's an existing value for page in $_GET, if there is then use it but if there's not then use 1 instead
$page = $_GET['page'] ?? '1'; // PHP > 7.0
```
* Reserved characters in URL
![URL Reserved Characters](Ex_Files_PHP_MySQL_EssT_Basics/Notes/URL_reserved_chars.png)
   * Four principle ones:
   <img src="https://github.com/ZR55/PHP-MySQL-Tutorial/blob/master/Ex_Files_PHP_MySQL_EssT_Basics/Notes/four_important_reserved_chars.png" width="35%"/>
* `urlencode` VS `rawurlencode` (For reserved characters)
   * Letters, numbers, underscore, and dash are unchanged.
   * Reserved characters are encoded.
   * `urlencode`: spaces become "+".
   * `rawurlencode`: spaces become "%20".
   * When to use them?
      * `rawurlencode` the path. Path is the part before "?". Spaces must be encoded as "%20"
      * `urlencode` the query string. Query string is the part after "?". Spaces are better encoded as "+". (This is the one is needed far more often)
* `urldecode` and `rawurldecode`
   * You'll rarely need them because PHP automatically decode the URL when it receives it.
* `htmlspecialchars($string)` (For the four principle reserved chars. This will be used a LOT)
   * MAKE SURE to use it whenever you use dynamic data and output that data, otherwise you might have XSS (cross-site scripting attack) which is a major security vulnerability.
   
#### Headers and Redirects
* Headers are a little information that gets sent before HTML.
   * `header($string)` can be used to change the default information in the header and/or add additional information. For example, `header("Content-Type: application/pdf");` tells the browser that instead of html we are about to send pdf file. `header("HTTP/1.1 404 Not Found");` returns an error message.
   * Modify headers:
      * Headers are sent before page data
      * So changes must be made before any HTML output (like before a single space or line return, whitespace in included files.
* Page Redirection
   * Page redirects are sent in headers
   * Headers are sent before page data
   * Header changes must be made before any HTML output
   * Page redirects must be sent before any HTML output
* Output Buffer
   * Data in output buffer is still editable
   * Headers can be changed
   * Whitespace can be sent before header edits and redirects
   * Two ways to turn on output buffer:
      * In `php.ini` file. How to tell if it's turned on? Use `<?php phpinfo(); ?>`. The number shows for the output_buffer will be the size of character that is going to be buffered.
      * In your php code, use `ob_start()`, which start the buffering (it has to come before anything), and `ob_end_flush()`, which ends buffering and flushes whatever results have accumulated in the buffer (it doesn't have to be called explicitly because the end of php code will automatically do that for you).
      * Which one to use? -> it's a good idea to have both.

#### Build Forms with PHP (NEEDS TO BE ADDED AGAIN!!!)
* About checkbox:
   * Good to have a hidden box to send attribute data if users don't check the box.
   
#### MySQL Basics
* Database advantages
   * Read and write data
   * Store more data
   * Keep data organized
   * Access data faster
   * Relate data to data
* Basic MySQL commands (capitalization is not required, but good practice):
   * `SHOW DATABASES;`
   * `SHOW TABLES;`
   * `SHOW FIELDS FROM table_name;`
   * `CREATE DATABASE db_name;`
   * `USE db_name;`
   * `DROP DATABASE db_name;`
   * Create tables:
   ```
   CREATE TABLE table_name (
    id INT(11) NOT NULL AUTO_INCREMENT,
    menu_name VARCHAR(255),
    position INT(3),
    visible TINYINT(1),
    PRIMARY KEY (id)
   );
   ```
* CRUD (Create, Read, Update, and Delete)
   * SQL SELECT (Read)
   ```
   SELECT *
   FROM table
   WHERE column1 = "some_text"
   ORDER BY column1 ASC;
   ```
   * SQL INSERT (Create)
   ```
   INSERT INTO table (col1, col2, col3)
   VALUES (val1, val2, val3);
   ``` 
   (col and val orders need to match)
   * SQL UPDATE (Update)
   ```
   UPDATE table
   SET col1 = 'this', col2 = 'that'
   WHERE id = 1;
   ```
   * SQL DELETE (Delete)
   ```
   DELETE FROM table
   WHERE id = 1
   LIMIT 1;          /*LIMIT only allows 1 record to be deleted, it's a good thing to do*/
   ```
* Relational database tables
   * SQL ADD INDEX (since the pk will automatically be assigned index but fk won't, index is better to be added for fks)
   ```
   ALTER TABLE table
   ADD INDEX index_name (column);
   ```

#### Use PHP to access MySQL
* **APT** (Application Programming Interface): a set of functions that define the way we use the software.
* Database APIs in PHP (int this tutorial, mysqli is used):
   * **mysql**: original MySQL API
   * **mysqli**: MySQL "improved" API
   * **PDO**: PHP Data Objects ([PDO tutorial](https://phpdelusions.net/pdo#dsn))
   * Differences between three APIs: <img src="https://github.com/ZR55/PHP-MySQL-Tutorial/blob/master/Ex_Files_PHP_MySQL_EssT_Basics/Notes/Differences_between_mysql_api.png" width="50%"/>
   * [PHP manual page that helps choosing between different APIs](https://www.php.net/manual/en/mysqlinfo.api.choosing.php)
* PHP-Database interaction steps (step 1 and 5 should only happen **once** for one PHP script):
   * Create a database connection
   ```
   mysqli_connect($host, $user, $password, $database)
   ```
   * Perform a database query
     `mysqli_query($connection, $query)`
     `mysqli_free_result($result_set)` (not freeing the result set won't be a big deal when retrieving small amount of data, but it DOES matter when retrieving lots of data)
   * Use returned data (if any)
      * `mysqli_fetch_row`: return the result in a simple array. For example: `['1', 'About Global bank', '1', '1']` which is not ideal since it's easy to forget which value belongs to which column
      * `mysqli_fetch_assoc`: return the result in an associative array that associate the value with the column name. For example, `['id' => '1', 'menu_name' => 'About Global Bank', 'position' => '1', 'visible' => '1']`
      ```
      // A better way to loop through the result array.
   
      // Traditional way:
      $result = find_all_subjects();   // "find_all_subjects()" is a query function that retrieves all the rows in subject table
      $count = mysqli_num_rows($result);
      for ($i = 0; $i < $count; $i++)
      {
          $subject = mysqli_fetch_assoc($result);
          echo $subject['menu_name'];
      }
   
      // A better way is to use a while loop
      $result = find_all_subjects();
      while($subject = mysqli_fetch_assoc($result))
      {
          echo $subject['menu_name'];
      }
      // Analysis: the while loop doesn't need to figure out the row count.
      // The while loop condition is doing testing and assignment at the same time. What the condition is saying is that
      // go and get another row from the result, as long as the assignment returns true, the loop keeps going.
      ```
      * `mysqli_fetch_array`
   * Release returned data
   * Close the database connection (even though database is automatically closed at the end of php script but it's recommended to write it explicitly)
   ```
   mysqli_close($connection)
   ```
* Error handling
  * Database connection error:
    * `mysqli_connect_errno()`: "errno" is the short for "error number". This retrieves the error code from last call connect to the database
    * `mysqli_connect_error()`: returns a string description of that last connect error.
  * Query error

#### CRUD with PHP
* While writing SQL query, it's the best practice to put single quotes around all the variables that you submit to MySQL. For example, 
```
$sql = "SELECT * FROM subjects ";   // The space at the end is super important.
$sql .= "WHERE id='" . $id . "'";  
```
* Use form data to create records
`mysqli_insert_id($connection)`: tells mySQL that you just inserted a new record can you give the id back to me
* Use form data to update records
  * Use UPDATE query
  * Updating a single record requires PK
  * Usually from form data (we'll display the data that is already in the database first and then allow users to change the data by submitting the form)
  * Returns true or false
* Delete a record
  * Use DELETE query
  * Deleting a single record requires record id
  * Form is optional; POST is best practice. (A search engine spider can click on every link you have on the website, but they won't submit any forms)
  * Returns true or false.

#### Common data validation types
* Validate forms
  * Do not blindly trust user-provided data.
  * Applications have data requirements
  * Code must enforce requirements on user-provided data.
  * "Validating data", "passing validations"
* Common data validation types
  * Presence (data cannot be left blank)
  * String length
  * Type
  * Inclusino in a set
  * Format (e.g. emails, currency, time...)
  * Uniqueness (e.g. usernames ...)
  * [PHP regular expression (preg_match())](http://php.net/manual/en/function.preg-match.php)
* Validate form values
  * Validate data before creating and updating records
  * Same validations are often used for both
  * Put validation code in a reusable function
  * If validations fail, create or update is prevented
  * User needs to know what was wrong so they can fix it.
  * Keep track of errors and report them
  * Reporting all errors at once is the best user experience
  * It's a better idea to let data validation always run instead of run only when user submit the form. (So put the data validtation function inside the query functions)
* Display validation errors
  * Repopulate form fields so user can fix them
  * Can indicate errors as a list (used by this tutorial)
  * Can indicate errors field-by-field (instead of putting all the error messages in one list, you need to use an associate array. When adding error messages to that array, instead of `$errors[] = "Name can't be blank.";`, array key should be specified like `$errors['menu_name'] = "Name can't be blank.";`
* Problems with validation logic
  * Surprisingly True (All of the following will return true)
    * `0 == FALSE`
    * `4 == TRUE`
    * `0 == NULL`
    * `0 == "0"`
    * `0 == ""`
    * `0 == "a"`
    * `"" == NULL`
    * `"abc" == TRUE`
    * `100 == 100.00`
    * `3 == "3 dogs"`
    * `"1" == "01"`
    * `"123" == "     123"`
    * `"123" == "+0123"`
    * `100 == "1e2"`
  * Type juggling during comparisons
    * When comparing two items with different data types, PHP has to decide how to compare two objects so PHP will make some adjustments.
    * String vs. null: converts null to ""
    * Boolean vs. other: converts other to boolean
    * Number vs. other: converts other to number
    * `===` allows quality comparison which means exactly the same and the type juggling doesn't take effect
    ```
    echo 0 == FALSE ? 'true' : 'false';   // true
    echo 0 === FALSE ? 'true' : 'false';  // false
    ```
  * Surprisingly Empty (Calling empty() on all of the following will return true, those are all considered empty by PHP)
    * `""`
    * `0`
    * `"0"`
    * `null`
    * `false`
    * `array()`
  * Be careful with basic operators: `<, <=, >, >=`, `&&, ||` (make sure to get those right)
  * Be careful with regular expressions
    * Symbolic language is powerful, but easy to make mistakes
    * Easy to overlook rare cases
    * ["Learning Regular Expression" taught by the same instructor](https://www.linkedin.com/learning/learning-regular-expressions-2/write-text-matching-patterns?u=76215914)
* Validation with deleting an item
  * Not only updating and creating a page require data validation, they are by far the most common use of validation, but validation before deleting also makes sense.
  * Validate that a subject does not have any pages assigned to it which would become orphaned after the subject is deleted (we can require users to delete pages inside of that subjects or reassign those pages to another object before we allow the subject to be deleted).
    
#### Understand SQL injection
* Example:
Below is the sql code we have for inserting data
```
$sql = "INSERT INTO subjects ";
$sql .= "(menu_name, position, visible) ";
$sql .= "VALUES (";
$sql .= "'" . $subject['menu_name'] . "', ";
$sql .= "'" . $subject['position'] . "', ";
$sql .= "'" . $subject['visible'] . "', ";
$sql .= ")";
```
imagine if we are going to insert `$subject['menu_name'] = "Davide's Story"`, we are going to get a sql query like this: `"INSERT INTO subjects (menu_name, position, visible) VALUES ('David's Story', '1', '1')"`
* Another example:
The query code we have for selecting a record: `$sql = "SELECT * FROM subjects WHERE id='" . $id . "'";`, and we expect to have `$id="5"`.
But what if a hacker typed in `$id="'; INSERT INTO admins (username, password) VALUES ('hacker', 'abcd1234'); SELECT * FROM subjects WHERE id='5";`, then the entrie sql query will be like this: `SELECT * FROM subjects WHERE id=''; INSERT INTO admins (username, password) VALUES ('hacker', 'abcd1234'); SELECT * FROM subjects WHERE id='5'`
* SQL injection
  * Execute arbitrary SQL requests
  * Craft string to manipulate SQL syntax and inject code
  * Any query which uses dynamic-data is vulnerable
  * URL parameters, form data, cookies, database data
  * "SQLi" for short
    * is the single biggest security concern
    * Ranked #1 security threat by OWASP (Open Web Application Security Project)
    * Why?
      * easy for attackers to detect and exploit
      * Gives access to large amounts of important data
      * Web applications have high level access
      * Circumvents normal access controls
* SQL Injection Uses
  * Bypass application authentication
  * Steal data
  * Alter data
  * Destory data
* Sanitize Data for SQL
  * Convert characters with meaning in SQL syntax to data
  * Add a backslash before all single quotes (e.g. `$subject['menu_name'] = "David\'s Story";`
  * In old PHP, there's a escape configuration called magic quotes that would try to escape these values for you while they are on their way to MySQL. Unfortunately, it created more problems than it solved. So the magic quotas is removed and it's up to us to add escape ourselves. 
    * `addslashes($string)` takes a string as an argument and it returns a string that adds backslashes in front of all the characters that needs to be escaped ( which including `'`, `"`, `\`, `a special character that represents null`.
    * `mysqli_real_escape_string($db, $string)`: it does the same thing as `addslashes($string)` and even more. It's part of mysql API and designed especially for MySQL. Besides the characters that are mentioned above, it also escapes line returns and other odd control character that you might not think of. If MySQL API is used, then better off to use `mysql_real_escape_string($connection, $string)` but it's better to change its name.
* Delimit data values
  * SQL and Single-Quoted Values
    * Required for strings, dates, and times
    * Not required for numbers and booleans
    * Samll performance penalty to convert to correct type (but you won't notice)
    * Large security benefit to quoting all values (prevents SQL injection)
  * Example:
    * Original SQL query: `$sql = "SELECT * FROM subjects `; $sql .= "WHERE id=" . db_escape($db, $id);`
    * If a hacker typed in following as id: `$id = "1; DROP TABLE payments";`
    * Because we don't have single quotes around the $id variable, even though we called escape but there's no special characters to escape, the final SQL query will look like: `SELECT * FROM subjects WHERE id=1; DROP TABLE payments`
  * Delimit Data Values
    * The best practice is to **put single quotes around all values** and always **escape** them
    * Helps prevent SQL injection
    * Forces attackers to circumvent data delimiters
* Prepared Statements
  * The basic idea is you give MySQL a template for a query that you wnat to run and you indicates places that you want to fill in the blanks later.
  * Example: 
  ```
  INSERT INTO subjects
  (menu_name, position, visible)
  VALUES
  (?, ?, ?)   // The question marks indicates the blanks that you want to fill in later.
  ```
  * Why?
    * Prepare statement once and reuse it many times
    * Can be faster
    * Separate the query from the dynamic data
    * Prevent SQL injection
  * Example on how to use it:
  ```
  // The SQL query template:
  $sql  = "SELECT id, first_name, last_name ";
  $sql .= "FROM users ";
  $sql .= "WHERE username = ? AND password = ?";
  $stmt = mysql_prepare($connection, $sql);   // Prepare the statment that is ready to use
  
  // Usage on the template:
  mysqli_stmt_bind_param($stmt, 'ss', $username, $password);    // 'ss' indicates both parameters are string
  mysqli_stmt_execute($stmt);
  mysqli_stmt_bind_result($stmt, $id, $first_name, $last_name);
  mysqli_stmt_fetch($stmt);
  mysqli_stmt_close($stmt);
  ```


### Part 2
#### Cookies and Sessions
* What is cookie? It's a small string of data stored by the user's web browser
* How it works?
  * A user's web broswer sends a request to a web server. The request would include a header that indicates the website and the page
  * Then web server responses back to the browser and sends back a response with a header that can include a directive called set cookie. The data included in the set cookie is waht is going to be sent to the browser and that the web server is saying, "Hey browser, store this data for later"
  * Then whenever the browser makes another request back to that same web server in the future, it's going to include that cookie data in its request header
  * It's sent along on every single request, regardless of what page it's for as long as it's for that same website domain.
  * Summary:
    * Cookies are stored by website domain (or subdomain)
    * Cookies are sent with every request to the website
    * Cookies have a maximum size of 4K (~4000 characters)
    * Cookies can be viewed, edited, or deleted by the user
    * Cookies are set and sent in the headers
    * Headers are sent before **any** page data
    * Cookies must be set before any output, unless output buffering has been turned on
  * Cookies are stored in super globals (`$_COOKIE`) just like forms (`$_POST`) and URLs (`$_GET`). It will be key-value pairs that store in an associate array.
* Set and read cookie values
  * `setcookie($name, $value, $expire, $path, $domanin, $secure, $httponly);`
    * `$name`: the name of the cookie
    * `$value`: the value that you want to assign to that name
    * `$expire`: the expiration which allows us to control how long these cookies can stick around for. It's always going to be a Unix timestamp that is the numer of seconds since 1970. For example, if we want to set the time 2 weeks from now, we can do `$expires = time() + 60*60*24*14` where `time()` gives us the number of seconds of today from 1970
    * `$path`: the path to the server to which the cookies's available. For exmaple, we can have a cookie that is only available if our path is in the staff area.
    * `$domain`: typically it's the entire website, but we can also use it to subdomains
    * `$secure`: if set to "true", then the cookie will only be trasmitted if there's https secure connection. Otherwise, the cookie won't be transmitted
    * `$httponly`: it says the cookie should only be sent in the header. In other words, it should not be available to Javascript
* Unset Cookie Values:
  * Two ways to delete the cookie
    * Set value to false: `setcookie($name, false);`
    * Tell the browser that it expires 1 hour ago: `setcookie($name, $value, (time() - 3600));`
  * Avoid boolean values when setting cookies
  * Use "0" for false and "1" for true instead
  * Cookies can only be deleted with the same options as used for setting the cookies
* Work with Sessions
  * Store data in a session file
  * Store the reference identifier for the session file in a cookie
  * Retrieval of the session file handled by PHP
* Benefits of Seesions
  * More storage
  * Smaller request sizes
  * Conceals data values
  * More secure and less hackable
* Pitfalls of Sessions
  * Slower to access
  * Expire when browser is closed
  * Accumulate session files
* Common uses of sessions
  * Frequently referred to data
  * User authentication (the tutorial uses)
  * Storing data during a redirect
* Starting sessions
  * Configure sessions in `php.imi` file (go to the [website](http://php.net/manual/en/session.configuration.php))
  * Can configure session cookie options
  * Sessions are turned off by default
  * `session_start($options)`
* Set and Read session values
  * `$_SESSION['lang'] = 'English';`
  * To get it back: `$lang = $_SESSION['lang'];`. But it's a always a good idea to check if it exists or not: `$lang = $_SESSION['lang'] ?? '';`
  * To unset the session: `unset($_SESSION['lang']);`
* Application of session
  * Turn on session in `initailze.php` and add `session_start()`

#### User Authentication Overview
* User Authentication is Essential
  * Password-protected areas are common in most web apps
  * Learning best practices avoids costly mistakes
  * Development choices and security concerns are intertwined topics
  * A ticket analogy
    * Purchase tickets for a concert            => Admin creates a user
    * Present ID at the gate                    => User logs in via a login form
    * Get handstamp and enter concert           => User is authenticated and given access
    * Show handstamp, avoid line, and reenter   => User requests additional password-protected pages
    * Wash away handstamp                       => User logs out
  * Process
    * Admin creates a user in the database
    * Password MUST be encrypted before user is stored
    * User logs in via a login form
    * Application searches for the username in the database
    * If the username is found, it encrypts the form password and compares it with the **encrypted** stroed password (NOT decrypt the password!)
    * If the passwords match, then it sets a value in the session to the user ID and redirects to a post-login page
    * User requests additional password-protected pages
    * Application checks the session data for the user ID
      * If present, it returns the requested page
      * If absent, it redirects to the login form
    * User logs out
    * User ID stored in session is removed

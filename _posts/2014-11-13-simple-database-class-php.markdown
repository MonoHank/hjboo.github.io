---
layout: post
title:  "Simple PHP Database Utilities"
author: Ben Centra
date:   2014-11-13 13:00:00
categories: code
tags: projects php database utilities pdo mysql
---

Database operations are required in just about any project of mine. So, I wrote up a simple PHP class that performs all your basic CRUD operations using [PDO][pdo] and MySQL [prepared statements][prepared-statements]. Using it reduces my setup time, provices basic application security, and makes development a breeze. 

The constructor of the class creates your PDO object using the MySQL driver. I also set a few options to prevent strange "gotchas" and determine the behavior of queries. The rest of the class contains methods that perform your CRUD operations: SELECT, INSERT, UPDATE, and DELETE. Each method takes two params, a SQL query and an associative array of parameters to bind. 

SELECT queries will return the data as an associative array, which is great for quickly encoding as JSON to ouptut. INSERT and other queries will return the number of rows affected. Any errors or exceptions will be caught and the method will return `false`. To differentiate between an empty result and an error, just use `===` for strict type checking.

Here's an example of the class in action:

{% highlight php %}
<?php

// Create a new DatabaseUtils object
$db = new DatabaseUtils($dbName, $dbHost, $dbUser, $dbPass);

// Insert some stuff
$sql = "INSERT INTO things (name, color) VALUES (:name, :color)";
$params = array("name" => "Hat", "color" => "Purple");
$result = $db->insert($sql, $params);
if ($result !== false) {
  echo "<p>Stuff Inserted!</p>";
  echo "<pre>".print_r($result, true)."</pre>";
}
else {
  die("<p>Error Inserting Stuff</p>");
}

// Select that stuff
$sql = "SELECT * FROM things";
$result = $db->select($sql);
if ($result !== false) {
  echo "<p>Selected Stuff!</p>";
  echo "<pre>".print_r($result, true)."</pre>";
}
else {
  die("<p>Error Selecting Stuff</p>");
}

?>

{% endhighlight php %}

Check out the source code below, or view the [Gist on GitHub][gist]. 

{% highlight php %}

<?php

/*
* DatabaseUtils.php - A class of simple database utilities.
* 
* Performs CRUD operations using PDO (MySQL) prepared statements.
*/

class DatabaseUtils 
{

  // Database connection object
  private $pdo;

  // Create a PDO object and connect to the database
  public function __construct($dbName, $dbHost, $dbUser, $dbPass) {
    try {
      // Instantiate the PDO object
      $this->pdo = new PDO(
        // Use MySQL database driver
        "mysql:dbname=$dbName;host=$dbHost", 
        $dbUser, 
        $dbPass, 
        // Set some options
        array(
          // Return rows found, not changed, during inserts/updates
          PDO::MYSQL_ATTR_FOUND_ROWS => true, 
          // Emulate prepares, in case the database doesn't support it
          PDO::ATTR_EMULATE_PREPARES => true,
          // Have errors get reported as exceptions, easier to catch
          PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
          // Return associative arrays, good for JSON encoding
          PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC
        )
      );
    } 
    catch (PDOException $e) {
        die('Database Connection Failed: ' . $e->getMessage());
    }
  }

  // Perform a SELECT query
  public function select($sql, $data = array()) {
    try {
      // Prepare the SQL statement
      $stmt = $this->pdo->prepare($sql);
      // Execute the statement
      if ($stmt->execute($data)) {
        // Return the selected data as an assoc array
        return $stmt->fetchAll(PDO::FETCH_ASSOC);
      }
      else {
        return false;
      }
    }
    catch (Exception $e) {
      return false;
    }
  }

  // Perform an INSERT query
  public function insert($sql, $data = array()) {
    try {
      // Prepare the SQL statement
      $stmt = $this->pdo->prepare($sql);
      // Execute the statement
      if ($stmt->execute($data)) {
        // Return the number of rows affected
        return $stmt->rowCount();
      }
      else {
        return false;
      }
    }
    catch (Exception $e) {
      return false;
    }
  }

  // Perform an UPDATE query
  public function update($sql, $data = array()) {
    return $this->insert($sql, $data);
  }

  // Perform a REPLACE query
  public function replace($sql, $data = array()) {
    return $this->replace($sql, $data);
  }

  // Perform a DELETE query
  public function delete($sql, $data = array()) {
    return $this->insert($sql, $data);
  }

  // Get the ID of the last row inserted
  public function lastInsertId() {
    return $this->pdo->lastInsertId();
  }

}

?>
{% endhighlight php %}

[gist]: https://gist.github.com/bencentra/92228e1f4139436c4153
[pdo]: http://php.net/manual/en/book.pdo.php
[prepared-statements]: http://php.net/manual/en/pdo.prepared-statements.php

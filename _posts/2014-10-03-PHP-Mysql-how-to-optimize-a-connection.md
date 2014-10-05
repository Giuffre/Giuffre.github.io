---
layout: post
title: PHP - MySql - How to optimize a connection with Singleton and PDO
description: "Tiny tutorial about connection mysql php singleton"
modified: 2014-10-06
tags: [code, php, mysql, design pattern, singleton]
image:
  feature: desktop.jpg
  credit: chrischan
  creditlink: http://chrischan.piccsy.com/
comments: true
share: true  
---

In this article i'll show you about the implementation of a type of Design Pattern, the [Singleton](http://en.wikipedia.org/wiki/Singleton_pattern). 

The Singleton Pattern requires that a class can instantiate most one object of self, in fact, in the implementation in PHP we have the constructor as private method and we have another "getter" method like `getInstance()` that will return an instance of this class.

The implementation of this pattern allows, in this case, to save valuable resources in memory during the execution of the script because is given the same instance `connection`. I use PDO driver in this example, below:

{% highlight php %}
<?php

class Connection {

    protected static $instance;

    private static $dsn = 'mysql:host=localhost;dbname=DBNAME';

    private static $username = 'username';

    private static $password = 'password';

    private function __construct() {
        try {
            self::$instance = new PDO(self::$dsn, self::$username, self::$password);
        } catch (PDOException $e) {
            echo "MySql Connection Error: " . $e->getMessage();
        }
    }

    public static function getInstance() {
        if (!self::$instance) {
            new Connection();
        }

        return self::$instance
    }

}

?> 
{% endhighlight %}

Consequently, it is possible to obtain an instance of the connection and run a query in the following way:

{% highlight php %}
<?php
// some previous code...
$connection = Connection::getInstance();
$query = "SELECT * FROM user WHERE id = :user_id"

$statement = $connection->prepare($query);
$statement->bindValue('user_id', $user_id, \PDO::PARAM_INT);
$statement->execute();

$result = $statement->fetchColumn();

// some next code...
?>
{% endhighlight %}


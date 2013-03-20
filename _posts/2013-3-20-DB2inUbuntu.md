---
layout: post
category: memo
tagline: install db2 on ubuntu
tags : [db2, ubuntu]
---
{% include JB/setup %}

## Update the sources list
### Add partner repository
    check out if the file /etc/apt/sources.list contain two sources below:
    deb http://archive.canonical.com/ubuntu lucid partner
    deb-src http://archive.canonical.com/ubuntu lucid partner
### Update the repository
    sudo apt-get update
## Install Apache & php
### Install
    sudo apt-get install apache2
    sudo apt-get install php5
### restart apache
    sudo /etc/init.d/apache2 restart
## Install DB2 Express-C
### Install
    sudo apt-get install db2exc
### Check the db2
#### login via db2inst1
    sudo su db2inst1
#### create SAMPLE database
    db2sampl
#### activate the database
    db2 activate db sample
#### connect to database
    db2 connect to SAMPLE
    db2 "SELECT * FROM EMP"
### set db2inst1 pwd
#### switch to root
    sudo su
#### change pwd
    passwd db2inst1
#### exit
    exit
## Integrate php & db2
### install pear
    sudo apt-get install php-pear
### install php dev
    sudo apt-get install php5-dev
### install ibm_db2
    sudo pecl install ibm_db2
input the db2 server path (/home/db2inst1/sqllib)
### set apache2
create a file named ibm_db2.ini in /etc/php5/apache2/conf.d
    extension=ibm_db2.so
### restart the apache server
## Test
### create test file
    sudo vim /var/www/testdb2.php
### add content
    <?php
        $database = 'SAMPLE';
        $user = 'db2inst1';
        $password = '<pwd>';
        $conn = db2_connect($database, $user, $password);
        if ($conn) {
            echo "<h2>Employee Names</h2>\n";
            $sql = "SELECT * FROM DB2INST1.EMP";
            $stmt = db2_exec($conn, $sql, array('cursor'=> DB2SCROLLABLE));
            echo "<table>\n";
            echo "<tr><th>First</th><th>Middle</th><th>Last</th></tr>";
            while ($row = db2_fetch_array($stmt)) {
                echo "\t<tr><td>$row[1]</td><td>$row[2]</td><td>$row[3]</td><tr>\n";
            }
            echo "</table>/n";
            db2_close($conn);
        } else {
            echo "Connection failed.";
        }
    ?>
over.

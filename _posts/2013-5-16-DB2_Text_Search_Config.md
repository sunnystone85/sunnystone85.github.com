---
layout: post
category: DB2
tagline: DB2 Text Search Config
tags: [Text Search, DB2, config]
---
## 利用configTool配置db2的text search

#### 1.
    configTool configureParams 
    -configPath absolute-path-to-config-folder 
    -logPath path 
    -tempDirPath path 
    -installPath path
    -maxHeapSize value
    -----------------------------------------
    Where the logPath specifies the log path to be used by DB2 Text Search, the tempDirPath specifies the temporary directory path to be used by DB2 Text Search, the installPath specifies the path to DB2 Text Search install directory which is DB2PATH\db2tss on Windows and the DB2DIR/db2tss directory on Linux and UNIX, and the maxHeapSize specifies the size of the heap that will be used. DB2DIR is the location of the DB2 copy.
#### 2.
    configTool generateToken 
    -configPath absolute-path-to-config-folder 
    -seed myInstanceName
    -----------------------------------------
    DB2 Text Search authenticates text search index administration and text search requests by using an authentication token. You do not have to remember the authentication token or the authentication key because the text search instance services use the token internally.
#### 3.
    configTool configureHTTPListener  
    -configPath absolute-path-to-config-folder 
    -adminHTTPPort port-number 
    -----------------------------------------
    Note: The value of the port should be between 1024 and 65535.
    The administrative HTTP port allows communication between text search 
    processes using TCP/IP. During the installation of a DB2 database product 
    or during instance creation, you can specify a service name and port if you 
    have root authority. These are used for updating the services file.

#### ex.
    configTool printAll -configPath /db2/testdb/sqllib/db2tss/config
    configTool configureParams -configPath /db2/testdb/sqllib/db2tss/config 
    -installPath /db2/kxmddb/sqllib/db2tss -logPath //db2/testdb/sqllib/db2tss/log

    configTool generateToken -configPath /db2/testdb/sqllib/db2tss/config -seed testdb
    configTool configureHTTPListener -configPath /db2/testdb/sqllib/db2tss/config 
    -adminHTTPPort 46000

    configTool printAll -configPath /db2/testdb/sqllib/db2tss/config
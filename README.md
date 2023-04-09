# [code-search](https://codesearch.debian.net)

![](https://img.shields.io/github/last-commit/timb-machine/linux-malware?style=for-the-badge)

The following is my scribbled notes on some fun search strings that yield results when you feed them into [Debian Code Search](https://codesearch.debian.net).

## Queries

### PHP

* Includes user controlled files and directories:

```(include|require)\s+.*\$_(GET|POST)```

* Uses $_GET and $_POST verbatim within function calls:

```\([^)]*\$_(GET|POST)```

* SQL injection:

```[a-z]+_query\(.*["'][^a-z]+\$_(GET|POST) filetype:php```

* Command injection:

```(`|eval\(|system\(|exec\(|shell_exec\().*\$_(GET|POST) filetype:php```

* Mass assignment:

```::create\(\$ filetype:php```

* Deserialization:

```unserialize\(\$_(POST|GET|COOKIE|REQUEST)```

### C

* Calls printf() without a string as the first parameter:

```[^a-z_]printf\([a-z]```

* Calls crypt(), this time limited to just C code because well, sometimes you'll see functions implemented in multiple language but know they're not really the same:

```[^a-z]crypt\( filetype:c```

* Uses shared memory insecurely:

```shm[a-z_]+\(.*(...[67]|S_IWOTH)```

* Debug environment:

```getenv.*DEBUG```

* Function prototype uses a signed integer:

```(,|\()\s*int [a-z_]+\s*(,|\))```

* Changes privileges:

```sete*[ug]id\(```

* Seeds random with a static value or one derived from time:

```[^\w]srand\(([0-9]*|.*time\() filetype:c```

* Uses raw sockets:

```socket.*PACKET,\s*SOCK_RAW```

* Creates a file but sets no permissions:

```=\s+open\([^,]+,[^,]+O_CREAT[^,]+\); filetype:c```

* Supports variable arguments which may lead to information disclosure:

```\.\.\.\) filetype:c```

* Uses possibly weak ciphers:

    * Defaults: ```(SSL_CTX_set_ciphersuites\(.*\"\"\)|SSL_set_cipher_list\(.*\"\"\)|SSL_set_ciphersuites\(.*\"\"\))```

    * Low: ```(SSL_CTX_set_ciphersuites\(.*LOW.*\)|SSL_set_cipher_list\(.*LOW.*\)|SSL_set_ciphersuites\(.*LOW.*\))```

    * Null: ```(SSL_CTX_set_ciphersuites\(.*NULL.*\)|SSL_set_cipher_list\(.*NULL.*\)|SSL_set_ciphersuites\(.*NULL.*\))```
    
    * DES: ```(SSL_CTX_set_ciphersuites\(.*DES.*\)|SSL_set_cipher_list\(.*DES.*\)|SSL_set_ciphersuites\(.*DES.*\))```
    
    * CBC: ```(SSL_CTX_set_ciphersuites\(.*CBC.*\)|SSL_set_cipher_list\(.*CBC.*\)|SSL_set_ciphersuites\(.*CBC.*\))```
    
    * ECB: ```(SSL_CTX_set_ciphersuites\(.*ECB.*\)|SSL_set_cipher_list\(.*ECB.*\)|SSL_set_ciphersuites\(.*ECB.*\))```
    
    * MD5: ```(SSL_CTX_set_ciphersuites\(.*MD5.*\)|SSL_set_cipher_list\(.*MD5.*\)|SSL_set_ciphersuites\(.*MD5.*\))```
    
* Sets weak file permissions:

```chmod\(.*?,\s*((0[0-7]|0o[0-7])[0-7]{1,2}?[67]|.*S_IWOTH.*)```

* Sets weak memory protections:

```mprotect.*\(PROT_WRITE.*PROT_EXEC.*|PROT_EXEC.*PROT_WRITE.*\)```

### Shell

* Sets setUID/setGID perms or installing a file with setUID/setGID perms:

```(chmod\s|install\s-m\s)([ug]\+s|[42][75])```

* Sets world writable perms:

```( a\+[rx]*w[rx]* | o\+[rx]*w[rx]* )```

### Java

* Uses Log4J:

```log4j filetype:java```

### Ruby

* Performs an LDAP query:

```bind_as\(.*filter\s*=>\s*[a-zA-Z]```

### Other

* Includes private keys:

https://codesearch.debian.net/search?q=RSA+PRIVATE&literal=1

* Accesses /tmp:

```/tmp/[a-z][a-z][a-z]```

* Compiler outputs binary with DT_RPATH:

```-Wl,-rpath=\$.*```

* Hard coded passwords:

```\s+[\$\*]pas.*\s*=\s*\"[a-z0-9]```

## Tips

* If you hit the \/ next to "Filter by package" on the results page, you can download the full list of affected packages
* Alternatively, you can take advantage of the Code Search API

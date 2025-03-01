# [code-search](https://codesearch.debian.net)

![](https://img.shields.io/github/last-commit/timb-machine/code-search?style=for-the-badge)

[Rolling 7 day view of updates from this repo](https://github.com/timb-machine/code-search/compare/main@%7B7day%7D...main)

[Submissions?](https://github.com/timb-machine/code-search/blob/main/doc/HACKING)

The following is my scribbled notes on some fun search strings that yield results when you feed them into [Debian Code Search](https://codesearch.debian.net).

## Queries

### Perl

* Uses bcrypt() for hashing:

```use (Digest|Crypt)::Bcrypt filetype:perl```

### PHP

* Uses $_GET, $_POST, $_REQUEST, $_SERVER and/or $_COOKIE verbatim within function calls:

```\([^)]*\$_[GPRSC] filetype:php```

* Includes user controlled files and directories:

```(include|require)\s+.*\$_[GPRSC] filetype:php```

* SQL injection:

```[a-z]+_query\(.*["'][^a-z]+\$_[GPRSC] filetype:php```

* Command injection:

```(`|eval\(|system\(|exec\(|shell_exec\().*\$_[GPRSC] filetype:php```

* Mass assignment:

```::create\(\$ filetype:php```

* Deserialization:

```unserialize\(\$_[GPRSC] filetype:php```

* Performs an LDAP query:

```ldap_search(.*?\w*,\w*\$.*?\w*,.*); filetype:php```

### C

* Calls printf() without a string as the first parameter:

```[^a-z_]printf\([a-z] filetype:c```

* Calls crypt(), this time limited to just C code because well, sometimes you'll see functions implemented in multiple language but know they're not really the same:

```[^a-z]crypt\( filetype:c```

* Uses shared memory insecurely:

```shm[a-z_]+\(.*(...[67]|S_IWOTH) filetype:c```

* Debug environment:

```getenv.*DEBUG filetype:c```

* Function prototype uses a signed integer:

```(,|\()\s*int [a-z_]+\s*(,|\)) filetype:c```

* Changes privileges:

```sete*[ug]id\( filetype:c```

* Uses UNIX time

```filetype:c time\(```

* Seeds random with a static value:

```srand\([0-9]+ filetype:c```

* Seeds random with a value derived from UNIX time:

```srand\(.*time\( filetype:c```

* Seeds random with a value derived from current PID:

```srand\(.*getpid\( filetype:c```

* Uses raw sockets:

```socket.*PACKET,\s*SOCK_RAW filetype:c```

* Creates a file but sets no permissions:

```=\s+open\([^,]+,[^,]+O_CREAT[^,]+\); filetype:c```

* Accesses files from /tmp:

```open\(.*\/tmp filetype:c```

* Supports variable arguments which may lead to information disclosure:

```\.\.\.\) filetype:c```

* Uses possibly weak ciphers:

    * Defaults: ```(SSL_CTX_set_ciphersuites\(.*\"\"\)|SSL_set_cipher_list\(.*\"\"\)|SSL_set_ciphersuites\(.*\"\"\) filetype:c)```

    * Low: ```(SSL_CTX_set_ciphersuites\(.*LOW.*\)|SSL_set_cipher_list\(.*LOW.*\)|SSL_set_ciphersuites\(.*LOW.*\)) filetype:c```

    * Null: ```(SSL_CTX_set_ciphersuites\(.*NULL.*\)|SSL_set_cipher_list\(.*NULL.*\)|SSL_set_ciphersuites\(.*NULL.*\)) filetype:c```
    
    * DES: ```(SSL_CTX_set_ciphersuites\(.*DES.*\)|SSL_set_cipher_list\(.*DES.*\)|SSL_set_ciphersuites\(.*DES.*\)) filetype:c```
    
    * CBC: ```(SSL_CTX_set_ciphersuites\(.*CBC.*\)|SSL_set_cipher_list\(.*CBC.*\)|SSL_set_ciphersuites\(.*CBC.*\)) filetype:c```
    
    * ECB: ```(SSL_CTX_set_ciphersuites\(.*ECB.*\)|SSL_set_cipher_list\(.*ECB.*\)|SSL_set_ciphersuites\(.*ECB.*\)) filetype:c```
    
    * MD5: ```(SSL_CTX_set_ciphersuites\(.*MD5.*\)|SSL_set_cipher_list\(.*MD5.*\)|SSL_set_ciphersuites\(.*MD5.*\)) filetype:c```
    
* Sets weak file permissions:

```chmod\(.*?,\s*((0[0-7]|0o[0-7])[0-7]{1,2}?[67]|.*S_IWOTH.*) filetype:c```

* Sets weak memory protections:

```mprotect.*\(PROT_WRITE.*PROT_EXEC.*|PROT_EXEC.*PROT_WRITE.*\) filetype:c```

* Copies argv[] without checking the length

```strcpy\(.+, argv\[```

* Uses XOR-based cipher:

```crypt.*[ ]+\^[ ]+[0-9] filetype:c```

* Uses XOR-based hash:

```hash.*[ ]+\^[ ]+[0-9] filetype:c```

### C++

* Creates a DBUS service

```QDBusConnection%3A%3AsessionBus().registerService(```

### Shell

* Sets setUID/setGID perms or installing a file with setUID/setGID perms:

```(chmod\s|install\s-m\s)([ug]\+s|[42][75])```

* Sets world writable perms:

```( a\+[rx]*w[rx]* | o\+[rx]*w[rx]* )```

* Hard coded passwords:

```useradd.* -p```

### Java

* Uses Log4J:

```log4j filetype:java```

### Ruby

* Performs an LDAP query:

```bind_as\(.*filter\s*=>\s*[a-zA-Z] filetype:ruby```

### Python

* Command injection:

```subprocess\..*\(\".*{ filetype:py```

* Implements Flask route:

```@app.route\( filetype:py```

* Performs decompression

```shutil.copyfileobj\( filetype:python```

### Docker

* Runs as root:

```USER root path:Dockerfile```

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

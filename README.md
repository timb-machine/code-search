# code-search

## Queries

### PHP

* Including user controlled files and directories:

```(include|require)\s+.*\$_(GET|POST)```

* Using $_GET and $_POST verbatim within function calls:

```\([^)]*\$_(GET|POST)```

* SQL injection:

```[a-z]+_query\(.*["'][^a-z]+\$_(GET|POST) filetype:php```

* Command injection:

```(`|eval\(|system\(|exec\(|shell_exec\().*\$_(GET|POST) filetype:php```

### C

* Calling printf() without a string as the first parameter:

```[^a-z_]printf\([a-z]```

* Things that call crypt(), this time limited to just C code because well, sometimes you'll see functions implemented in multiple language but know they're not really the same:

```[^a-z]crypt\( filetype:c```

* Insecure shared memory usage:

```shm[a-z_]+\(.*(...[67]|S_IWOTH)```

* Debug environment:

```getenv.*DEBUG```

* Function prototypes that use a signed integer

```(,|\()\w*int [a-z_]+\w*(,|\)))```

### Shell

* Setting setUID/setGID perms, or which install a file with setUID/setGID perms:

```(chmod\s|install\s-m\s)([ug]\+s|[42][75])```

* Setting world writable perms:

```( a\+[rx]*w[rx]* | o\+[rx]*w[rx]* )```

### Java

* Log4J:

```log4j filetype:java```

### Other

* Private keys:

https://codesearch.debian.net/search?q=RSA+PRIVATE&literal=1

* Access to /tmp:

```/tmp/[a-z][a-z][a-z]```

* Compiler outputs binary with DT_RPATH:

```-Wl,-rpath=\$.*```

## Tips

* If you hit the \/ next to "Filter by package" on the results page, you can download the full list of affected packages
* Alternatively, you can take advantage of the Code Search API

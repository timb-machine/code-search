# code-search

## Queries

* Source files that set setUID/setGID perms, or which install a file with setUID/setGID perms:

```(chmod\s|install\s-m\s)([ug]\+s|[42][75])```

* Source files that set world writable perms:

```( a\+[rx]*w[rx]* | o\+[rx]*w[rx]* )```

* PHP files including user controlled files and directories:

```(include|require)\s+.*\$_(GET|POST)```

* Use of $_GET and $_POST verbatim within function calls:

```\([^)]*\$_(GET|POST)```

* Calling printf() without a string as the first parameter:

```[^a-z_]printf\([a-z]```

* Things that call crypt(), this time limited to just C code because well, sometimes you'll see functions implemented in multiple language but know they're not really the same:

```[^a-z]crypt\( filetype:c```

* SQL injection:

```[a-z]+_query\(.*["'][^a-z]+\$_(GET|POST) filetype:php```

* Private keys:

https://codesearch.debian.net/search?q=RSA+PRIVATE&literal=1

* Log4J:

```log4j filetype:java```

* Insecure shared memory usage:

```shm[a-z_]+\(.*(...[67]|S_IWOTH)```

* Debug environment:

```getenv.*DEBUG```

* Access to /tmp:

```/tmp/[a-z][a-z][a-z]```

* Compiler outputs binary with DT_RPATH:

```-Wl,-rpath=\$.*```

* Command injection:

```(`|eva\(|system\(|exec\(|shell_exec\().*\$_(GET|POST) filetype:php```

* Function prototypes that use a signed integer

```(,|\()\w*int [a-z_]+\w*(,|\)))```

## Tips

* If you hit the \/ next to "Filter by package" on the results page, you can download the full list of affected packages
* Alternatively, you can take advantage of the Code Search API

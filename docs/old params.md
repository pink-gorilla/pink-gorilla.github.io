

You'll get available command line options appending `--help`:
```
clojure -Sdeps '{:deps {org.pinkgorilla/gorilla-notebook {:mvn/version "0.4.9"}}}' -m pinkgorilla.core --help
```
so
```
clojure -Sdeps '{:deps {org.pinkgorilla/gorilla-notebook {:mvn/version "0.4.9"}}}' -m pinkgorilla.core -P 9111
```
will start up the HTTP server at port 9111.
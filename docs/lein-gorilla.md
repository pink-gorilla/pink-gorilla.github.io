
## as a leiningen plugin

To use Gorilla in one of your Leiningen projects,  add the following to the :plugins section of that project’s project.clj file:

```
[lein-pinkgorilla "0.0.8"]
```

Your completed project.clj file might look something like this:

```
(defproject your-demo "0.1.0-SNAPSHOT"
  :description "A demo project for PinkGorilla Notebook."
  :dependencies [[org.clojure/clojure "1.10.0"]]
  :main ^:skip-aot demo.core
  :target-path "target/%s"
  :plugins [[org.pinkgorilla/lein-pinkgorilla "0.0.8"]]
  :profiles {:uberjar {:aot :all}})
```

That’s it. You should now be able to run ```lein pinkgorilla``` from within the project directory and get started.

Alternatively, just add the following to your ~/.lein/profiles.clj

{:user {:plugins [[org.pinkgorilla/lein-pinkgorilla "0.0.8"]]}}

A demo project that uses lein-pinkgorilla is [ta](https://github.com/pink-gorilla/trateg)

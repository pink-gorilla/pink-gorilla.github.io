## ![Pink Gorilla](images/pink-gorilla-32.png)[![GitHub Actions status |pink-gorilla/gorilla-notebook](https://github.com/pink-gorilla/gorilla-notebook/workflows/CI/badge.svg)](https://github.com/pink-gorilla/gorilla-notebook/actions?workflow=CI)[![Clojars Project](https://img.shields.io/clojars/v/org.pinkgorilla/gorilla-notebook.svg)](https://clojars.org/org.pinkgorilla/gorilla-notebook)

![WTF Jupther](images/wtf-is-jupyter.png)

Pink Gorilla Notebook is a browser based notebook REPL for Clojure and ClojureScript. We aim at a small core application with a high level of runtime extensibility and great user experience. Extensibility primarily revolves around visualisation, widgets and data.

### Use cases
- Data science
- Persistent experiments and demos (Clojure/ClojureScript libraries)
- Courses and education on all matters related to clojure
- Pluggable JEE webapp instrumentation


### Web Interface

Whichever method you use to start application, you should reach it at [`http://localhost:9000/worksheet.html`](http://localhost:9000/worksheet.html).

This source comes with various [test notebooks](https://github.com/pink-gorilla/gorilla-notebook/tree/master/notebooks) to try and the explorer should have some more. 



# Running Gorilla Notebook

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

## inside your project.clj (as a clojars library)

Add a dependency in your project, and start gorilla-notebook, a good example is [python-gorilla](https://github.com/pink-gorilla/python-gorilla/blob/master/profiles/notebook/src/notebook/main.clj). 


## via clojure (as a clojars library)

The easiest way to run releases locally is leveraging the `clojure` cli
```
clojure -Sdeps '{:deps {org.pinkgorilla/gorilla-notebook {:mvn/version "0.4.9"}}}' -m pinkgorilla.core
```
You'll get available command line options appending `--help`:
```
clojure -Sdeps '{:deps {org.pinkgorilla/gorilla-notebook {:mvn/version "0.4.9"}}}' -m pinkgorilla.core --help
```
so
```
clojure -Sdeps '{:deps {org.pinkgorilla/gorilla-notebook {:mvn/version "0.4.9"}}}' -m pinkgorilla.core -P 9111
```
will start up the HTTP server at port 9111.

## Via Docker Image

### prebuild docker images

We provide uberjar Docker images which can be run as follows:
```
docker run --rm -p 9000:9000 pinkgorillawb/gorilla-notebook:jdk
```
If you want some samples to play with, you may want to clone and mount the samples repo into the container:

```
git clone https://github.com/pink-gorilla/sample-notebooks
docker run --rm -p 9000:9000 -v `pwd`/sample-notebooks/samples:/work/sample-notebooks:rw pinkgorillawb/gorilla-notebook:jdk
```

```
docker run -p 9000:9000 -v `pwd`/.m2:/root/.m2:rw -v `pwd`/notebooks:/tmp/notebooks:rw --rm clojure:tools-deps clojure -Sdeps '{:deps {org.pinkgorilla/gorilla-notebook {:mvn/version "0.4.9"}}}' -m pinkgorilla.core
```

### via ctr.run


If you aim at running a Docker image built on demand from git by [ctr.run](ctr.run) (which is awesome) you can
```
docker run -p 9000:9000 -v `pwd`/.m2:/root/.m2:rw ctr.run/github.com/pink-gorilla/gorilla-notebook:a-branch-name gorilla-notebook.sh -c /root/.m2/custom.edn
```

### custom build docker image

```
docker build --rm -t me/gorilla-notebook:builder .
```



## inside a servlet container

The uberjar may also work by just dropping it into another webapp (in `WEB-INF/lib`) . Whether you are lucky
 or not depends on the dependencies of your app. If all goes well, Pink Gorilla will appear at
`.../your-app-context/gorilla-repl/worksheet.html`.

```
./script/build-uberwar.sh
```
should give you the standalone war file. Drop it into your servlet container and visit the root url of the webapp.

## from source

```
npm install
./script/build-uberjar.sh
```

The uberjar is what the Docker image uses. It can be run by executing

```
java -jar target/gorilla-notebook-standalone.jar
```

## Development

```
npm install
lein build-tailwind-dev
./script/run-repls-with-jpda.sh
```

builds css and spins up the webserver and a Shadow CLJS build with JPDA debugging. NREPL should be serving you Clojure and ClojureScript at port `8703`.

There are a bunch of aliases in `project.clj` you might want to check. Try

```
lein help
```

### VS Code repl
- Run Botebook via /Development
- Jack In
- Server running in your project
- shadow-cljs
- Localhost:8703
- :app-with-cljs-kernel-dev
- you will get a clj repl AND a ljs repl
- the cljs repl will be available after opening a browser window


# FAQ

### Is Gorilla Notebook ready for day to day use?
The future is uncertain, but we are not aware of any technical issues which should be holding back users.

### What about migration from Gorilla REPL?
Being a decendant from [Gorilla REPL](http://gorilla-repl.org) we aim at a smooth migration path for the brave and also remain backwards
 compatible. However:

- We prefer dynamic dependencies (`add-dependencies` in notebooks) and rendering fully delegated to the browser. Getting the parts (`incanter`, `loom`, `expresso`) working dynamically should not be hard. 

- Given the nature of Reagent, this did not appear to make sense with regards to persisted html. We ended up introducing version 2
  persistence (transit based) while still supporting version 1 (shamelessly discarding output).
- URLs have slightly changed. The viewer is at `.../worksheet.html#/view` now. You may want to try
 [here](http://localhost:9000/worksheet.html#/view?source=github&user=JonyEpsilon&repo=gorilla-test&path=ws/graph-examples.clj)
 in case you have it running at port `9000`.
- We introduced compatibility namespaces `gorilla-plot.*`, `gorilla-renderable.*` and `gorilla-repl.*`. `gorilla-plot.*`


## Extensibility

We try to keep the code shipping with the bare notebook application minimal and aim at runtime customization where
 possible. The application (Jar/Uberjar/Docker Image) ships two flavors:

- `:advanced` optimization without ClojureScript kernel support
- `:none` optimization with ClojureScript kernel support and runtime extensibility

We support JVM library (`pomegranate`)-, ClojureScript- and JavaScript (`requirejs`) extensibility at runtime.



## Contributing

Contribution of pretty much any kind is welcome. Feel free to get in touch. We are on [Clojurians Slack](http://clojurians.net/) and on [Clojurians Zulip #PinkGorillaDev](https://clojurians.zulipchat.com/#narrow/stream/212578-pink-gorilla-dev).

## History

In 2016, Andreas was working on the [first iteration of Gorilla REPL modernisation](https://www.contentreich.de/pimping-gorilla-repl-with-react-clojurescript-and-beyond). Amongst other
  things, [Reagent](http://reagent-project.github.io/) was introduced at that time. Unfortunately, it went silent -
  for almost three years. [This issue](https://github.com/pink-gorilla/gorilla-notebook/issues/2) revived the project.

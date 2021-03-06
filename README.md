# tools.cursive-arcadia-repl
An [nREPL] bridge for [Arcadia Unity]. Heavily inspired by [arcadia.nrepl](https://github.com/spacepluk/arcadia.nrepl),
with the difference of redirecting eval operations to Arcadia's nREPL rather than its UDP REPL. Cursive specific operations
are not redirected but evaluated in the bridge as they require the JVM.

[nREPL]: https://github.com/clojure/tools.nrepl
[Arcadia Unity]: https://github.com/arcadia-unity/Arcadia

## Prerequisites

* Install [Clojure CLI](https://clojure.org/guides/getting_started) (`brew install clojure` on Mac)
* Install [Intellij](https://www.jetbrains.com/idea/download/) and [Cursive](https://cursive-ide.com)
* [Setup an Arcadia project](https://github.com/arcadia-unity/Arcadia/wiki/Getting-Started)

## How to use Cursive with Arcadia

Having installed the prerequisites, follow these steps to setup a Cursive->Arcadia REPL.

1. Open your Unity project and verify that Arcadia is running. You should see the message "Arcadia Started!" in Unity's console.

2. Create a new clojure project with a `deps.edn` file in your Unity project. For example:

    ```sh
    cd <your-unity-project>/Assets
    mkdir <project-name> && cd <project-name>
    mkdir src test target
    touch deps.edn
    ```

3. Add Arcadia sources and this library to the project's `deps.edn`:

    ```clj
    {:deps
     {org.clojure/clojure {:mvn/version "1.10.0"}}
     :aliases
     {:dev
      {:extra-paths ["../Arcadia/Source"]}
      :arcadia-repl
      {:extra-deps
       {eponai/tools.cursive-arcadia-repl {:git/url "https://github.com/eponai/tools.cursive-arcadia-repl"
                                           :sha     "c303076d7d725787e1524d59d010a45c47355890"}}
       :main-opts
       ["-m" "eponai.tools.cursive-arcadia-repl"]}}}
    ```

4. In Intellij/Cursive, open and import the `Assets/<project-name>` directory.

5. Start a clojure process either in the terminal or in Cursive, executing: `clj -A:arcadia-repl`. This starts the nREPL bridge on port `7888`.

6. Create a [remote REPL in Cursive](https://cursive-ide.com/userguide/repl.html#remote-repls) on port `7888`.

Forms typed into the remote REPL should be evaluated in Unity!

### Testing the setup

Here's some code you can use to test whether it's working correctly:

```clj
(ns <project>.core
  (:require
    [arcadia.core :as arc]
    [clojure.clr.io :as io]))

(comment
  ;; Send this form to the remote REPL (default key binding cmd+shift+p)
  (arc/log "Hi unity!")

  ;; You should see this message being printed in Unity's console!

  ;; Testing Clojure CLR code
  (io/as-file "some.file")
  ;; => #object[FileInfo 0xe64d6600 "some.file"]
  )
```

### As a standalone process

This Arcadia nREPL bridge can be run with `tools.deps` or `leiningen` as a standalone process too:

```sh
git clone git@github.com:eponai/tools.cursive-arcadia-repl.git
cd tools.cursive-arcadia-repl
clj -A:repl
# or with leiningen
lein run
```


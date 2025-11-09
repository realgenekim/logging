# genek/logging

Unified logging configuration for Clojure/Java projects using Timbre.

This was extracted from the Fulcro RAD project by Tony Kay, and then modified for use in other programs.

## What This Library Provides

This library provides a **batteries-included** unified logging setup that:

1. **Routes all Java logging frameworks to Timbre** via slf4j bridges:
   - `log4j` → slf4j → timbre
   - `java.util.logging` → slf4j → timbre
   - `java.commons.logging` → slf4j → timbre

2. **Pre-configured Timbre settings** with:
   - Custom output formatting with timestamps
   - Namespace filtering (deny noisy libraries like connection pools, Datomic, etc.)
   - Log level configuration per namespace
   - Pretty-printing support for data structures

3. **Helper utilities**:
   - `configure-logging!` - One-function setup
   - `pretty` - Mark data for pretty-printing in logs
   - `p` macro - Pretty-print with visual delimiters

## Usage

### 1. Add to deps.edn

**Recommended: Use git dependency**

```clojure
{:deps {genek/logging {:git/url "https://github.com/realgenekim/logging.git"
                       :git/sha "LATEST_COMMIT_SHA"}}}
```

Get the latest SHA from: https://github.com/realgenekim/logging/commits/main

Or for local development:

```clojure
{:deps {genek/logging {:local/root "../logging"}}}
```

**That's it!** All slf4j dependencies are included transitively.

### 2. Configure in your main namespace

```clojure
(ns my-app.core
  (:require [logging.main :as glog]
            [taoensso.timbre :as log]))

;; Configure at namespace level (runs once on load)
(glog/configure-logging! glog/config)

;; Now use timbre for logging
(log/info :app/starting "Application started")
```

## What You Don't Need

You **do not** need to add these to your deps.edn - they're already included:

```clojure
;; ❌ DON'T add these - already in genek/logging
org.slf4j/log4j-over-slf4j {:mvn/version "1.7.30"}
org.slf4j/jul-to-slf4j     {:mvn/version "1.7.30"}
org.slf4j/jcl-over-slf4j   {:mvn/version "1.7.30"}
com.fzakaria/slf4j-timbre  {:mvn/version "0.3.19"}
com.taoensso/timbre        {:mvn/version "5.2.1"}
```

## Customizing Configuration

You can override the default config by passing your own:

```clojure
(glog/configure-logging!
  {:taoensso.timbre/logging-config
   {:min-level :debug
    :ns-filter {:allow #{"my-app.*"}
                :deny #{"noisy.library.*"}}}})
```

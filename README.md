[![](https://img.shields.io/clojars/v/scicloj/tablecloth)](https://clojars.org/scicloj/tablecloth)
[![](https://api.travis-ci.org/scicloj/tablecloth.svg?branch=master)](https://travis-ci.org/github/scicloj/tablecloth)
[![](https://img.shields.io/badge/zulip-discussion-yellowgreen)](https://clojurians.zulipchat.com/#narrow/stream/236259-tech.2Eml.2Edataset.2Edev/topic/api)

## Versions

### tech.ml.dataset 6.x (master branch)

[![](https://img.shields.io/clojars/v/scicloj/tablecloth)](https://clojars.org/scicloj/tablecloth)

### tech.ml.dataset 4.x (4.0 branch)

`[scicloj/tablecloth "4.04"]`

## Introduction

[tech.ml.dataset](https://github.com/techascent/tech.ml.dataset) is a
great and fast library which brings columnar dataset to the Clojure.
Chris Nuernberger has been working on this library for last year as a
part of bigger `tech.ml` stack.

I’ve started to test the library and help to fix uncovered bugs. My main
goal was to compare functionalities with the other standards from other
platforms. I focused on R solutions:
[dplyr](https://dplyr.tidyverse.org/),
[tidyr](https://tidyr.tidyverse.org/) and
[data.table](https://rdatatable.gitlab.io/data.table/).

During conversions of the examples I’ve come up how to reorganized
existing `tech.ml.dataset` functions into simple to use API. The main
goals were:

  - Focus on dataset manipulation functionality, leaving other parts of
    `tech.ml` like pipelines, datatypes, readers, ML, etc.
  - Single entry point for common operations - one function dispatching
    on given arguments.
  - `group-by` results with special kind of dataset - a dataset
    containing subsets created after grouping as a column.
  - Most operations recognize regular dataset and grouped dataset and
    process data accordingly.
  - One function form to enable thread-first on dataset.

Important\! This library is not the replacement of `tech.ml.dataset` nor
a separate library. It should be considered as a addition on the top of
`tech.ml.dataset`.

If you want to know more about `tech.ml.dataset` and `dtype-next` please
refer their documentation:

  - [tech.ml.dataset
    walkthrough](https://techascent.github.io/tech.ml.dataset/walkthrough.html)
  - [dtype-next
    overview](https://cnuernber.github.io/dtype-next/overview.html)
  - [dtype-next
    cheatsheet](https://cnuernber.github.io/dtype-next/cheatsheet.html)

Join the discussion on
[Zulip](https://clojurians.zulipchat.com/#narrow/stream/236259-tech.2Eml.2Edataset.2Edev/topic/api)

## Documentation

Please refer [detailed documentation with
examples](https://scicloj.github.io/tablecloth/index.html)

## Usage example

``` clojure
(require '[tablecloth.api :as tc])
```

``` clojure
(-> "https://raw.githubusercontent.com/techascent/tech.ml.dataset/master/test/data/stocks.csv"
    (tc/dataset {:key-fn keyword})
    (tc/group-by (fn [row]
                    {:symbol (:symbol row)
                     :year (tech.v3.datatype.datetime/long-temporal-field :years (:date row))}))
    (tc/aggregate #(tech.v3.datatype.functional/mean (% :price)))
    (tc/order-by [:symbol :year])
    (tc/head 10))
```

\_unnamed \[10 3\]:

| :symbol | :year |      summary |
| ------- | ----: | -----------: |
| AAPL    |  2000 |  21.74833333 |
| AAPL    |  2001 |  10.17583333 |
| AAPL    |  2002 |   9.40833333 |
| AAPL    |  2003 |   9.34750000 |
| AAPL    |  2004 |  18.72333333 |
| AAPL    |  2005 |  48.17166667 |
| AAPL    |  2006 |  72.04333333 |
| AAPL    |  2007 | 133.35333333 |
| AAPL    |  2008 | 138.48083333 |
| AAPL    |  2009 | 150.39333333 |

## Contributing

`Tablecloth` is open for contribution. The best way to start is
discussion on
[Zulip](https://clojurians.zulipchat.com/#narrow/stream/236259-tech.2Eml.2Edataset.2Edev/topic/api).

### Development tools for documentation

Documentation is written in RMarkdown, that means that you need R to
create html/md/pdf files. Documentation contains around 600 code
snippets which are run during build. There are two files:

  - `README.Rmd`
  - `docs/index.Rmd`

Prepare following software:

1.  Install [R](https://www.r-project.org/)
2.  Install [rep](https://github.com/eraserhd/rep), nRepl client
3.  Install `pandoc`
4.  Run nRepl
5.  Run R and install R packages:
    `install.packages(c("rmarkdown","knitr"), dependencies=T)`
6.  Load rmarkdown: `library(rmarkdown)`
7.  Render readme: `render("README.Rmd","md_document")`
8.  Render documentation: `render("docs/index.Rmd","all")`

### API file generation

`tablecloth.api` namespace is generated out of `api-template`, please
run it before making documentation

``` clojure
(exporter/write-api! 'tablecloth.api.api-template
                     'tablecloth.api
                     "src/tablecloth/api.clj"
                     '[group-by drop concat rand-nth first last shuffle])
```

### Guideline

1.  Before commiting changes please perform tests. I ususally do: `lein
    do clean, check, midje` and build documentation as described above
    (which also tests whole library).
2.  Keep API as simple as possible:
      - first argument should be a dataset
      - if parametrizations is complex, last argument should accept a
        map with not obligatory function arguments
      - avoid variadic associative destructuring for function arguments
      - usually function should working on grouped dataset as well,
        accept `parallel?` argument then (if applied).
3.  Follow `potemkin` pattern and import functions to the API namespace
    using `tech.v3.datatype.export-symbols/export-symbols` function
4.  Functions which are composed out of API function to cover specific
    case(s) should go to `tablecloth.utils` namespace.
5.  Always update `README.Rmd`, `CHANGELOG.md`, `docs/index.Rmd`, tests
    and function docs are highly welcomed
6.  Always discuss changes and PRs first

## TODO

  - tests
  - tutorials

## Licence

Copyright (c) 2020 Scicloj

The MIT Licence

# PMD RDF data validations

A collection of RDF validation queries ready to be
used against RDF data using the [RDF Validator](https://github.com/Swirrl/rdf-validator).

- **pmd-qb** - a set of validations based on the RDF Data Cube IC validations, adjusted slightly to fit the way the RDF Cube has been implemented within Publish My Data
- **pmd4** - as set of validations to check data relating to the catalog structure and other data expectations for PMD4 to function as expected
- **qb** - a verbatim copy of the IC validations from the [RDF Data Cube specification](https://www.w3.org/TR/vocab-data-cube/#h3_wf-rules).

## Quick Start:

1. Install clojure cli tools using `brew install clojure/tools/clojure` (see [clojure cli docs](https://clojure.org/guides/getting_started#_clojure_installer_and_cli_tools) for other installations)
2. Create a new file named `deps.edn` (location is unimportant) with the following contents:

```clojure
{:deps {;; NOTE each dep here is a validation suite
        swirrl/validations.pmdqb {:git/url "git@github.com:Swirrl/pmd-rdf-validations.git"
                                  :sha "63479f200a7c3d1b0e63bc43b2617181644c846b"
                                  :deps/manifest :deps
                                  :deps/root "pmd-qb"}
        swirrl/validations.pmd4 {:git/url "git@github.com:Swirrl/pmd-rdf-validations.git"
                                  :sha "63479f200a7c3d1b0e63bc43b2617181644c846b"
                                  :deps/manifest :deps
                                  :deps/root "pmd4"}}
 :aliases {:rdf-validator {:extra-deps { swirrl/rdf-validator {:git/url "https://github.com/Swirrl/rdf-validator.git"
                                                          :sha "fd848fabc5718f876f99ee4ee5a3f89ea8529571"
                                                          }}
                      :main-opts ["-m" "rdf-validator.core"]}
           :local/validations {:classpath-overrides {swirrl/validations.pmdqb "/path/to/local/github/projects/pmd-rdf-validations/pmd-qb/src"
           																																										swirrl/validations.pmd4 "/path/to/local/github/projects/pmd-rdf-validations/pmd4/src"}}
                     }
 }
```

3. Navigate to the directory containing your newly created `deps.edn` file in the command line and run `clojure -A:rdf-validator --endpoint http://{server}:{port}/your-db-name/query` 

## How this works

The above `deps.edn` configuration file declaratively specifies dependencies on two suites of SPARQL query validations.  These two suites happen to located at different paths within the same git repository, though they could easily be in separate repositories.  It additionally then specifies a dependency on the [RDF Validator](https://github.com/Swirrl/rdf-validator) itself under the `:rdf-validator` alias.

Running the command `clojure -A:rdf-validator ,,,` will then automatically cause the dependencies to be fetched, cached for future use, put on the JVMs classpath before finally executing the [RDF Validator](https://github.com/Swirrl/rdf-validator) application.

As the [RDF Validator](https://github.com/Swirrl/rdf-validator) supports finding and loading validation suites from the java classpath the dependent suites will then be executed.  The validator defines a simple manifest for discovering and executing suites specified in this manner.

## Trouble shooting

- Make sure that the `:sha` values in your `deps.edn` match the latest commit SHAs for this [pmd-rdf-validations repo](https://github.com/Swirrl/pmd-rdf-validations/commits/master) and the [rdf-validator repo](https://github.com/Swirrl/rdf-validator/commits/master) respectively.

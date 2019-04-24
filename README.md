# clojure-test

This file is a raw dump of the Confluence page that is being retired.

WIP: To triage issues that people have with clojure.test with the goal of finding areas where it could potentially be improved.

Constraints: Retain backwards compatibility with existing callers.

Some slack conversations on the topic:

* https://clojurians-log.clojureverse.org/clojure/2018-12-12/1544643159.036200 turning into this thread:
* https://clojurians-log.clojureverse.org/clojure/2018-12-12/1544654428.083100

## Themes

## Open Issues

Just for easy lookup, here are all(?) the open issues: [JIRA open issues, labeled clojure.test](https://dev.clojure.org/jira/secure/IssueNavigator!executeAdvanced.jspa?jqlQuery=labels+%3D+%22clojure.test%22+and+status+%3D+%22open%22&runQuery=true&clear=true)

## Error reporting

* The thrown? test doesn't attempt to unwrap exceptions. It should probably test the main exception type and, in some situations, test the cause type as well (for CompilerException and ExceptionInfo – maybe others?). See https://dev.clojure.org/jira/browse/CLJ-2458

## Assertion error reporting

TBD

## Problems making assertions

* Extensibility?
* Problems with are.  are can be awkward at times, e.g. sometimes you want to put an (is ,,,, msg) inside it for more control, e.g. of the message — which leads to the reporting of the error twice because are also wraps an is over your form.
* How do you share a block of assertions across different tests, parameterize them, and retain good errors?
* Expressiveness: provides very little "language" for assertions: is and are provide basic equality/truthiness checks, and there are simple assertions around exceptions, but nothing to help with assertions about more complex data structures without very verbose code (see, for example, Expectations' more-of feature).

## Usability of fixtures

* Complects test namespace with fixtures/scope.  Choices are wrap each test in an ns separately in a fixture, or wrap all tests in this ns, but no way to opt out, or opt-in.
* No support for per-run fixture (allowing use cases like loading (and then dropping) fixture data for all tests in a suite.

## General Usability

* Although test-vars lets you run a single test, it provides no success/failure feedback and requires Vars rather than just symbols – https://dev.clojure.org/jira/browse/CLJ-1908 would address this.

## Organizational Issues

* Problems people have in structuring tests, test suites etc.  How to map ns's and functions to test ns's and functions? 1-1 mapping, 1-many mapping etc?

## Tooling Issues

* Composability: if tooling wants to override the reporting behavior (or the assertion behavior), it's often impossible to combine such tooling with other tooling. An example is that Leiningen monkey-patches some of the clojure.test machinery which can then break other tooling that tries to override behavior. I recently created expectations/clojure-test to provide an Expectations-like DSL over clojure.test and found that it didn't work well with Paul Stadig's Humane Test Output because both tools extend the assert-expr multimethod (I ended up making e/c-t conditionally aware of HTO and changing its behavior accordingly).

## Using clojure.spec with clojure.test

* Is this even a good idea?  If not do clojure.spec property based tests also need a test runner?  Perhaps other projects such as leiningen need to plug a gap here?

## clojure.test development speed is tied to clojure.core's releases

* Alex Miller mentioned there was a possibility of splitting clojure.test out as an external dependency from clojure.core so it can evolve at its own pace.

## Other test libraries

This (incomplete) list is provided purely for information, as possible sources of inspiration for changes that could be made or pain points that people have (that have already turned into a library):

* [eftest](https://github.com/weavejester/eftest)
* [Expectations](https://github.com/clojure-expectations/expectations) and [expectations.clojure.test](https://github.com/clojure-expectations/clojure-test)
* [Koacha (blog post)](https://lambdaisland.com/blog/2018-11-02-test-wars-new-hope) – there are several repos associated with Koacha and the blog post also links to a number of add-ons for clojure.test (match-combinators, humane-test-output, ultra, etc)

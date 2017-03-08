# walmartlabs/vizdeps

[![Clojars Project](http://clojars.org/walmartlabs/vizdeps/latest-version.svg)](http://clojars.org/walmartlabs/vizdeps)

An alternative to `lein deps :tree` that uses [Graphviz](http:://graphviz.org) to present
a dependency diagram of all the artifacts (Maven-speak for "libraries") in your project.

Here's an example of a relatively small project:

![active-status](images/active-status-deps.png)

A single artifact may be
a transitive dependency of multiple other artifacts.
`vizdeps` can show this (`lein deps :tree` doesn't), and will highlight in red any dependencies
with a version mismatch.
This can make it *much* easier to identify version conflicts and provide the best
exclusions and overrides.

These dependency graphs can get large; using the `--vertical` option may make large
trees more readable.

![rook](images/rook-deps.png)

To keep the graph from getting any more cluttered, the `org.clojure/clojure` artifact
is treated specially (just the dependency from the project root is shown).

The `--prune` is used when managing version conflicts; it removes uninteresting artifacts.
Those that remain either have a version conflict (such as `commons-codec`, below) or
transitively depend on an artifact with such a conflict:

![rook-purged](images/rook-pruned.png)

## Usage

Put `[walmartlabs/vizdeps "0.1.4"]` into the `:plugins` vector of your `:user`
profile.

### vizdeps task

```
Usage: lein vizdeps [options]

Options:
  -o, --output-file FILE    target/dependencies.pdf  Output file path. Extension chooses format: pdf or png.
  -s, --save-dot                                     Save the generated GraphViz DOT file well as the output file.
  -n, --no-view                                      If given, the image will not be opened after creation.
  -H, --highlight ARTIFACT                           Highlight the artifact, and any dependencies to it, in blue. Repeatable.
  -v, --vertical                                     Use a vertical, not horizontal, layout.
  -d, --dev                                          Include :dev dependencies in the graph.
  -p, --prune                                        Exclude artifacts and dependencies that do not involve version conflicts.
  -h, --help                                         This usage summary.
```

The `--highlight` option can be repeated; any artifact that contains any of the provided strings will be highlighted.

## vizconflicts task

```
Usage: lein vizconflicts [options]

Options:
  -o, --output-file FILE  target/conflicts.pdf  Output file path. Extension chooses format: pdf or png.
  -X, --exclude NAME                            Exclude any project whose name matches the value. Repeatable.
  -s, --save-dot                                Save the generated GraphViz DOT file well as the output file.
  -n, --no-view                                 If given, the image will not be opened after creation.
  -h, --help                                    This usage summary.
```

`vizconflicts` is used in concert with [lein-sub](https://github.com/kumarshantanu/lein-sub) to analyze
dependencies between and across a multi-module project.

When different versions of the same artifact are in use, the output chart will include a diagram of how that
artifact is used across the different module:

![conflicts](images/conflicts.png)

The lines in each chart identify dependencies; solid lines are explicit dependencies,
dotted lines are transitive dependencies.

When one version is the majority, it is highlighted in blue (and other versions are
drawn in red).

## License

Copyright © 2016-2017 Walmartlabs

Distributed under the Apache Software License 2.0.

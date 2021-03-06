# Rebel

Build system that conforms to the ideal [Reason](https://github.com/facebook/reason) build spec found [here](https://github.com/facebook/reason/wiki/Reason-Project:-Proposal-For-Unifying-Local-Development-And-Package-Manement) and [here](https://github.com/facebook/reason/wiki/The-Ideal-Package-Sandbox) (summary below). Implemented with [Jenga](https://github.com/janestreet/jenga), Jane Street's all-purpose build system.

## Features

- Lightning build speed: fast startup, incremental, parallel, etc.
- `rebel` to build everything. No need for sub-build commands since only changed things are rebuilt.
- Works with npm-style `node_modules` third-party dependencies that follows the spec (see next section).
- Works with OPAM/ocamlfind dependencies: put them in the `rebel.ocamlfindDependencies` field, [like so](https://github.com/chenglou/jengaboot/blob/e4a8860617b1c27f0faeeb40082476a22c5e07df/package.json#L28).
- Works with Reason and vanilla OCaml syntax, out of the box.
- Generates JavaScript output through [js_of_ocaml](http://ocsigen.org/js_of_ocaml/) or [bucklescript](https://github.com/bloomberg/bucklescript).
- Peace-of-mind "wipe the whole world" : just remove `_build/`.
- Share your work through the normal npm workflow: `npm publish`.

**Coming soon**:
- Integrated [BetterErrors](https://github.com/npm-ml/BetterErrors).
- Generates the correct .merlin files for [Merlin](https://github.com/the-lambda-church/merlin), for editor assistance.
- Generates utop/rtop bootstrap file.
- Generates documentation based on `mli`/`rei` interface files.
- Easier installation: bundle Jenga, etc.

## "Ideal Reason Project" Spec

- Needs a flat `src/` directory.
- Third-party libraries go into `node_modules/`, flat list of all transitive dependencies.
- Third-party libraries names of the format `foo-bar` are transformed into FooBar when used as module name (source file names aren't allowed to have kebab-case).
- Every library (including the current one) needs a package.json that lists its npm dependencies in `dependencies` (as usual). OPAM/ocamlfind dependencies go into `rebel.ocamlfindDependencies`, same format as `dependencies`.
- Refer to your own files e.g. `myFoo` as the module `MyFoo` in other files in the current project.
- Refer to third-party files e.g. `node_modules/bar/src/index.re` as `Bar.Index` in the current project.

## For Consumers

- `npm install --save-dev reasonml/rebel`
- Write some files in your `src/`, or install some spec compliant (see above) npm packages.
- `./node_modules/.bin/rebel`. :tada: :tada:!
- Binary in `_build/src/app.out`

## Rebel Config

- backend (string): One of "jsoo", "native", "bucklescript".
- targets ([string]): entry points for reason application. Currently only works with bucklescript.
- ocamlfindDependencies (array) : list of ocaml packages that work with ocamlfind

### More Info
**What to put in `gitignore` and `npmignore` when developing a jengaboot compliant project?**

Jenga generates a `.jenga` directory that caches the build info. `_build/` (where the artifacts live) cannot be easily shared yet (`ocamlc`'s artifacts are sometimes laptop-specific). `.merlin` is auto-generated by the build.

In short, put `.jenga`, `_build` and `.merlin` in your `.gitignore` and `.npmignore`.

## For Contributors
Ping me on IRC (chenglou), Twitter (@_chenglou), [Gitter](http://gitter.im/facebook/reason), etc. for specific details.

- `git clone` this repo
- `npm install`
- Modify logic in `src`
- To test examples run `npm run rebel:bs` or `npm run rebel:re`

### Things to test
- Support for source/interface interface files that are (uncapitalized, Capitalized) and (snake_cased, camelCased). Support third-party library names of the latter pair (npm modules can no longer be capitalized).
- Compiles dangling unused source files and unused dependencies (for merlin editor integration, e.g. add a new dependency, compile, then start autocompleting that previously unused module).
- Dependencies that contain the same source file names.
- Third-party sources with unique names.
- Compiling A and B that both require C.
- .rei interface files referencing third-party modules that source files don't (extra dependencies).
- `open`ing a third-party library to refer to its module that happens to have the same name as one of our first-party modules.
- Compile libraries with mixed `re`, `rei`, `ml` and `mli` files.
- Symlinks (relative and absolute).
- Third-party folders with kebab-case, snake_case, camelCase and CamelCase.
- Not compiling unused dependencies.

## Credits
The whole Jane Street team for making Jenga and helping me understanding it. Also to the whole Reason team of course.

# eslint-plugin-ts-immutable

[![npm version][version-image]][version-url]
[![travis build][travis-image]][travis-url]
[![Coverage Status][codecov-image]][codecov-url]
[![code style: prettier][prettier-image]][prettier-url]
[![MIT license][license-image]][license-url]

[ESLint](https://eslint.org/) rules to disable mutation in TypeScript.

## Background

In some applications it is important to not mutate any data, for example when using Redux to store state in a React application. Moreover immutable data structures has a lot of advantages in general so I want to use them everywhere in my applications.

I originally used [immutablejs](https://github.com/facebook/immutable-js/) for this purpose. It is a really nice library but I found it had some drawbacks. Specifically when debugging it was hard to see the structure, creating JSON was not straightforward, and passing parameters to other libraries required converting to regular mutable arrays and objects. The [seamless-immutable](https://github.com/rtfeldman/seamless-immutable) project seems to have the same conclusions and they use regular objects and arrays and check for immutability at run-time. This solves all the aformentioned drawbacks but introduces a new drawback of only being enforced at run-time. (Altough you loose the structural sharing feature of immutablejs with this solution so you would have to consider if that is something you need).

Then typescript 2.0 came along and introduced [readonly](https://github.com/Microsoft/TypeScript/wiki/What's-new-in-TypeScript#read-only-properties-and-index-signatures) options for properties, indexers and arrays. This enables us to use regular object and arrays and have the immutability enfored at compile time instead of run-time. Now the only drawback is that there is nothing enforcing the use of readonly in typescript.

This can be solved by using linting rules. So the aim of this project is to leverage the type system in typescript to enforce immutability at compile-time while still using regular objects and arrays.

## Installing

`npm install eslint-plugin-ts-immutable --save-dev`

See the [example](#sample-configuration-file) tslint.json file for configuration.

## Compability

- eslint-plugin-ts-immutable requires typescript >=2.8, node >=6, and tslint 5.x.x.

## ESLint Rules

In addition to immutable rules this project also contains a few rules for enforcing a functional style of programming. The following rules are available:

- [Immutability rules](#immutability-rules)
  - [readonly-keyword](#readonly-keyword)
  - [readonly-array](#readonly-array)
  - [no-let](#no-let)
  - [no-array-mutation](#no-array-mutation)
  - [no-object-mutation](#no-object-mutation)
  - [no-method-signature](#no-method-signature)
  - [no-delete](#no-delete)
- [Functional style rules](#functional-style-rules)
  - [no-this](#no-this-no-class)
  - [no-class](#no-this-no-class)
  - [no-mixed-interface](#no-mixed-interface)
  - [no-expression-statement](#no-expression-statement)
  - [no-if-statement](#no-if-statement)
  - [no-loop-statement](#no-loop-statement)
  - [no-throw](#no-throw)
  - [no-try](#no-try)
  - [no-reject](#no-reject)
- [Recommended built-in rules](#recommended-built-in-rules)

## Immutability rules

## Functional style rules

## Recommended built-in rules

### [no-var-keyword](https://palantir.github.io/tslint/rules/no-var-keyword/)

Without this rule, it is still possible to create `var` variables that are mutable.

### [no-parameter-reassignment](https://palantir.github.io/tslint/rules/no-parameter-reassignment/)

Without this rule, function parameters are mutable.

### [typedef](https://palantir.github.io/tslint/rules/typedef/) with call-signature option

For performance reasons, tslint-immutable does not check implicit return types. So for example this function will return an mutable array but will not be detected (see [#18](https://github.com/jonaskello/tslint-immutable/issues/18) for more info):

```javascript
function foo() {
  return [1, 2, 3];
}
```

To avoid this situation you can enable the built in typedef rule like this:

`"typedef": [true, "call-signature"]`

Now the above function is forced to declare the return type becomes this and will be detected.

## Sample Configuration File

Here's a sample TSLint configuration file (tslint.json) that activates all the rules:

```javascript
{
  "extends": ["tslint-immutable"],
  "rules": {

    // Recommended built-in rules
    "no-var-keyword": true,
    "no-parameter-reassignment": true,
    "typedef": [true, "call-signature"],

    // Immutability rules
    "readonly-keyword": true,
    "readonly-array": true,
    "no-let": true,
    "no-object-mutation": true,
    "no-delete": true,
    "no-method-signature": true,

    // Functional style rules
    "no-this": true,
    "no-class": true,
    "no-mixed-interface": true,
    "no-expression-statement": true,
    "no-if-statement": true

  }
}
```

It is also possible to enable all the rules in tslint-immutable by extending `tslint-immutable/all` like this:

```javascript
{
  "extends": ["tslint-immutable/all"]
}
```

## How to contribute

For new features file an issue. For bugs, file an issue and optionally file a PR with a failing test.

## How to develop

To execute the tests first run `yarn build` and then run `yarn test`.

While working on the code you can run `yarn test:work`. This script also builds before running the tests. To run a subset of the tests, change the path for `yarn test:work` in `package.json`.

Please review the [tslint performance tips](https://palantir.github.io/tslint/develop/custom-rules/performance-tips.html) in order to write rules that run efficiently at run-time. For example, note that using `SyntaxWalker` or any subclass thereof like `RuleWalker` is inefficient. Note that tslint requires the use of `class` as an entrypoint, but you can make a very small class that inherits from `AbstractRule` which directly calls `this.applyWithFunction` and from there you can switch to using a more functional programming style.

In order to know which AST nodes are created for a snippet of typescript code you can use [ast explorer](https://astexplorer.net/).

## How to publish

```
yarn version --patch
yarn version --minor
yarn version --major
```

## Prior work

This work was originally inspired by [eslint-plugin-immutable](https://github.com/jhusain/eslint-plugin-immutable).

[version-image]: https://img.shields.io/npm/v/eslint-plugin-ts-immutable.svg?style=flat
[version-url]: https://www.npmjs.com/packageeslint-plugin-ts-immutable
[travis-image]: https://travis-ci.com/jonaskello/eslint-plugin-ts-immutable.svg?branch=master&style=flat
[travis-url]: https://travis-ci.com/jonaskello/eslint-plugin-ts-immutable
[codecov-image]: https://codecov.io/gh/jonaskello/eslint-plugin-ts-immutable/branch/master/graph/badge.svg
[codecov-url]: https://codecov.io/gh/jonaskello/eslint-plugin-ts-immutable
[license-image]: https://img.shields.io/github/license/jonaskello/eslint-plugin-ts-immutable.svg?style=flat
[license-url]: https://opensource.org/licenses/MIT
[prettier-image]: https://img.shields.io/badge/code_style-prettier-ff69b4.svg?style=flat
[prettier-url]: https://github.com/prettier/prettier
[type-info-badge]: https://img.shields.io/badge/type_info-required-d51313.svg?style=flat
[type-info-url]: https://palantir.github.io/tslint/usage/type-checking

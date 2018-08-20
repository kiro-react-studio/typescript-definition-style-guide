# TypeScript Definition Style Guide

> Style guide for adding type definitions to my npm packages

*Open an issue if anything is unclear or if you have ideas for other checklist items.*


## Checklist

- Use tab-indentation and semicolons.
- The definition should target the latest TypeScript version.
- Exported properties/methods should be documented (see below).
- The definition should be tested (see below).
- Ensure you're not falling for any of the [common mistakes](https://github.com/DefinitelyTyped/DefinitelyTyped/#common-mistakes).
- For packages with a default export, use `export default foo;`, not `export = foo;`. You will have to add `module.exports.default = foo;` to the `index.js` file.
- If the entry file in the package is named `index.js`, name the type definition file `index.d.ts` and put it in root.<br>
	You don't need to add a `typings` field to package.json as TypeScript will infer it from the name.
- Add the type definition file to the `files` field in package.json.
- The pull request should have the title `Add TypeScript definition`. *(Copy-paste it so you don't get it wrong)*
- Ignore any Travis linting failures. I will fix them after merging.
- Help review [other pull requests](https://github.com/search?q=user%3Asindresorhus+is%3Apr+is%3Aopen+%22Add+TypeScript+definition%22&type=Issues) that adds a type definition.

Check out [this](https://github.com/sindresorhus/write-json-file/blob/master/index.d.ts) and [this](https://github.com/sindresorhus/delay/blob/master/index.d.ts) example for how it should be done.

### Types

- All types used in the public interface should be `export`'ed.
- Types should not have namespaced names; `interface Options {}`, not `interface FooOptions {}`, unless there are multiple `Options` interfaces.
- Use the array shorthand type notation; `number[]`, not `Array<number>`.
- Prefer returning the [`unknown` type](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html#new-unknown-top-type) instead of `any` whenever possible.
- Don't use abbreviation for type/variable/function names; `function foo(options: Options)`, not `function foo(opts: Opts)`.
- [Generic type variables](https://www.typescriptlang.org/docs/handbook/generics.html#working-with-generic-type-variables) should have descriptive names; `type Mapper<Element, NewElement> = …`, not `type Mapper<T, U> = …`.
- Don't prefix the name of interfaces with `I`; `Options`, not `IOptions`.
- Imports, destructuring, and object literals should not have spaces around the identifier; `{foo}`, not `{ foo }`.

### Documentation

Exported definitions should be documented with [TSDoc](https://github.com/Microsoft/tsdoc). You can borrow text from the readme.

Example:

```typescript
export interface Options {
	/**
	 * Allow negative numbers.
	 *
	 * @default true
	 */
	allowNegative?: boolean;

	/**
	 * Has the ultimate foo.
	 *
	 * @default false
	 */
	hasFoo?: boolean;
}

/**
 * Add two numbers together.
 *
 * @param x - The first number to add.
 * @param y - The second number to add.
 * @returns The sum of `x` and `y`.
 */
export default function add(x: number, y: number, options?: Options): number;
```

Note:

- Don't [hard-wrap](https://stackoverflow.com/questions/319925/difference-between-hard-wrap-and-soft-wrap).
- Put an empty line between interface entries.
- Sentences should start with an uppercase character and end in a dot.
- There's an empty line after the function description.
- Parameters and the return value should be documented.
- There's a dash after the parameter name.
- `@param` should not include the parameter type.
- If the parameter description just repeats the parameter name, leave it out.
- If the parameter is `options` it doesn't need a description.
- If the function returns `void` or a wrapped `void` like `Promise<void>`, leave out `@returns`.
- If you include an `@example`, there should be a newline above it. There also needs be a newline after the `@example` tag because of [this TypeScript bug](https://github.com/Microsoft/TypeScript/issues/15749).
- If the API accepts an options-object, define an `Options` interface as seen above. Document default option values using the [`@default` tag](http://usejsdoc.org/tags-default.html) (since interfaces cannot have default values).
- Use `@returns`, not `@return`.

### Testing

The type definition should be tested with [`tsd-check`](https://github.com/SamVerschueren/tsd-check). [Example of how to integrate it.](https://github.com/sindresorhus/delay/commit/594c42fa0f9f5f2997715d7e83dbd9e2e018e9aa)

Example:

```typescript
import {expectType} from 'tsd-check';
import delay from '.';

expectType<void>(await delay(200));

expectType<string>(await delay(200, {value: '🦄'}));
expectType<number>(await delay(200, {value: 0}));

expectType<never>(await delay.reject(200, {value: '🦄'}));
expectType<never>(await delay.reject(200, {value: 0}));
```

Note:

- The test file should be named `index.test-d.ts`.
- `tsd-check` supports top-level `await`.

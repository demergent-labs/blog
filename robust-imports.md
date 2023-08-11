<style type="text/css">
    ol ol { list-style-type: lower-alpha; }
    ol ol ol { list-style-type: lower-roman; }
</style>
# Robust Imports
We recently released an update that has made importing from the Azle npm package
more meaningful and robust. Previously, importing Azle into your TypeScript
canister was merely a formality. It helped the static type checker, but it
didn't directly influence how Azle interpreted your canister. Consequently, this
could lead to numerous unexpected behaviors, especially when importing
identifiers with names like "Record" or "Variant" from non-Azle modules.
However, with the introduction of Azle 0.17, we've implemented a significantly
more robust approach to handling the Azle module, effectively resolving these
issues.

I want to pull back the curtain on this update and explain what went into it. To
provide context, I will first provide a brief overview of how Azle operates,
then I will discuss our initial, less sophisticated approach and its
shortcomings, delve into our improved second approach, and its enhanced
functionality, and finally, explore our future plans for the importing system.

## How Azle Works (A Very Brief Overview)
To run a canister on the Internet Computer (IC), it must compile to WebAssembly
(wasm). To enable TypeScript/JavaScript to run on the IC, we required a
JavaScript engine that could be compiled to wasm. This allowed us to load a
canister written in TypeScript into that JavaScript engine, then we can analyze
the TypeScript canister and create a Rust wrapper to interface with it. The IC
converts candid values to Rust values, which our wrapper then converts to
JavaScript values. These values are then passed to the corresponding JavaScript
functions from your canister which now live within the engine. After execution,
the value is returned to the wrapper, which converts it back to a Rust value,
allowing the final conversion to candid.

The Azle npm package serves as a framework, facilitating these operations by
providing:

1. Decorators, allowing developers to specify which functions and custom types
   should be exposed in the wrapper. Examples include:
    1. `Record`
    1. `Variant`
    1. `$query`
    1. `$update`
1. Type aliases, ensuring that the parameter, return types, and object
   properties align with valid candid types. Examples include:
    1. `nat`
    1. `blob`
    1. `int64`
    1. `text`

## The Previous Approach

In Azle 0.16 and earlier versions, importing Azle was unnecessary for parsing
your canister. One could simply use the keywords directly.  Azle merely scanned
files for the literal words "Record", "$query", "nat32", etc. It did not
validate whether these keywords were from Azle or whether they represented valid
Azle/candid types. This leniency led to problems.

Issues arose when importing items with the same name as Azle keywords from
non-Azle packages, necessitating renaming in order to import them. Furthermore,
renaming Azle keywords during importation or making aliases to them was not
supported. It was mandatory to use the exact characters in precise order for the
process to work.

But not any more!

## The Current Approach

We have harnessed the TypeScript compiler's capabilities to determine which
keywords are imported keywords from Azle and which have no relation to Azle.

In theory this task wasn't difficult to understand. We just needed to do some
simple analysis of the imports, trace them back to their origin and see it they
come from Azle. However, as we tried finding tools to help us do this we found a
startling lack of good documentation or pre-made tooling. But with a lot of
experimenting with the TypeScript package, studying its source code, and making
many progressively more refined queries to Chat GPT we finally made our
breakthrough. We are eager to share our findings to spare others the challenges
we faced.

We started by looking at the TypeScript npm package, reasoning that it must
possess import resolution capabilities for successful JavaScript transpilation.

We eventually discovered that the TS Type Checker offered the functionalities we
required. There is a lot to the type checker and we will only focus on a small
subset of its features here.

Given the Type Checker's relevance, we decided to implement this part of the
compiler in TypeScript to fully leverage the TypeScript module.

The TS Type Checker comes from a TS Program, which comes from compiling the
entry file:

```typescript
import { createProgram } from 'typescript';
const program = createProgram(['index.ts'], {});
const typeChecker = program.getTypeChecker();
```

With the type checker at our disposal, we can access the symbol table for each
file in our program:

```typescript
const sourceFile = program.getSourceFile('myFile.ts');
const sourceFileSymbol = typeChecker.getSymbolAtLocation(sourceFile);
const symbolTable = sourceFileSymbol.valueDeclaration.locals;
```

> [!NOTE]
>
> What is a Symbol Table?
>
> A symbol table is a data structure used by compilers, interpreters, and other
> programming language tools to manage information about symbols (identifiers)
> used in a program. Symbols can include variable names, function names, class
> names, labels, and other user-defined or language-specific identifiers.
>
> During the various phases of a programming language processing, like lexical
> analysis, parsing, semantic analysis, and code generation, the compiler or
> interpreter uses the symbol table to track and resolve identifiers. This helps
> ensure that the program is correctly analyzed and translated into machine code
> or executed properly.
>
> The symbol table aids in various tasks, such as detecting undeclared or
> multiply declared variables, checking type compatibility, managing namespaces
> and scope resolution, and generating appropriate code or performing
> optimizations.
>
> Different programming languages and language tools may implement symbol tables
> in various ways, often optimizing them for efficiency and effective management
> of symbol information.

Now that we have the symbol table for each file we can determine how the Azle
keywords are represented in that file. This involves looping through each symbol
in the table and analyzing it to discern its origin:

- For type alias or variable declarations, we recursively preform the same
analysis on the assigned value.

- If a symbol is imported from another file, then it will have a module specifier
indicating its source. We can look up the Symbol Table for that source and
continue tracing until we determine if it is related to Azle.

This approach yields a table for each file, which shows how each Azle keyword
is represented in that file:

```typescript
'myFile.ts': {
    record: [azle.Record, Record, MyRecord],
    variant: [Variant],
    etc.
}
```

Now, while parsing, if we encounter the Type Reference 'azle.Record', we can
determine which, if any, azle keyword it refers to by consulting this table. And
as in the example table above we see that it should be parsed as a Record.

As a result, your Azle projects can import Azle in any EMCAscript standard way
(please report any non-functional methods).

## Future Developments

Our next objective is extending this robustness to user-defined type aliases.

Consider the following example:

myFile.ts:
```typescript
import { Record } from 'azle';
export type MyRecord = azle.Record<{}>;
```

index.ts:
```typescript
import * as aliases from './myFile';
import { MyRecord } from './myFile';
import { $query } from 'azle';

type MyBrokenAlias = aliases.MyRecord;

$query;
export function myBrokenQuery(alias: BrokenRenamedAlias): void {
    console.log(alias)
}

type MyAlias = MyRecord;
export function myQuery(alias: RenamedAlias): void {
    console.log(alias)
}
```
Simple aliases to user defined types like `MyAlias` as an alias to `MyRecord`
work just fine. But if more complex tracing is required like resolving the
qualified name `aliases.MyRecord` for the alias `MyBrokenAlias`, then our
process breaks down.

Another (though more minor) issue pertains to the current treatment of entries
in our alias table. They are treated as literal Azle types. This mostly affects
our Rust wrapper which most developers should only rarely need to look into, but
it also make reporting compile and runtime errors more ambiguous. For example
the error for this code snippet:

```typescript
type MyNat = azle.nat;

$query;
export function getMyNat(): MyNat {
    return false;
}
```

currently would be read:

```
TypeError: false is not of type 'nat'
```
It would be more informative if we tracked the alias name, allowing us to say:

```
TypeError: false is not of type 'MyNat'
```
or

```
TypeError: false is not of type 'MyNat', which aliases 'nat'
```
Finally, for Azle, there remains much to learn about the TypeScript type checker.
Even while writing this article, I've identified a couple of things that I could
take advantage of to improve our process.

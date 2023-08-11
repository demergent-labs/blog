<style type="text/css">
    ol ol { list-style-type: lower-alpha; }
    ol ol ol { list-style-type: lower-roman; }
</style>
# Robust Imports

Recently we released an update that made importing from the Azle npm package
more meaningful and robust. In the before times importing Azle into your
TypeScript canister was just a formality. It helped aid your static type
checker, but until now it didn't actually affect how Azle interpreted your
canister. As a result there could be a lot of errant behavior, especially if you
imported something called "Record" or "Variant" from another non-Azle module. As
of Azle 0.17 we have introduced a much more robust method for handling the Azle
module, which resolves these issues.

I'm going to pull back the curtain a little, I'll give a brief overview of how
Azle works to provide context, then talk about our naive first approach and why
it didn't work, then I'll cover our less naive second approach and how it works
better and then I'll explore some of the future work we have for our importing
system.

## How Azle Works (A Very Brief Overview)

In order to run a canister on the Internet Computer (IC) the canister needs to
be able to compile to wasm. To make TypeScript/JavaScript run on the IC we
needed a JavaScript engine that can be compiled to wasm. Then we can create a
Rust wrapper to provide an interface to the JavaScript engine. The IC converts
the candid values to a Rust value so our wrapper can convert it to a JavaScript
value. We feed those values into the corresponding JavaScript functions that are
living in the engine. The function runs and returns the value out to the
wrapper which converts it back to a Rust valve so that those values be converted
to candid and voila you have a TypeScript/JavaScript canister!

The Azle npm package provides two things as a framework to make this all
possible.

1. Decorators so the developer can tell Azle which functions and custom type
   need to be exposed in the wrapper, for example
    1. `Record`
    1. `Variant`
    1. `$query`
    1. `$update`
1. Type aliases to use to make sure the parameter, return types, and object
   properties you are using will match up to valid candid types, for example
    1. `nat`
    1. `blob`
    1. `int64`
    1. `text`

## The Before Times

In Azle 0.16 and earlier, you didn't need even need import Azle into a single
file for it to be able to parse your canister. If you knew the keywords you
could just use them. If you were working in vanilla vim without any of that
nasty bulk of an IDE or any the overhead from expensive programming tools, you
know like a real programmer would, you wouldn't even notice you were missing
anything.

Thats' because in the before times when Azle parsed your files it just looked
for the word "Record" or "$query" or "nat32". It wouldn't check to see if those
keyword actually came from Azle or if they would evaluate to valid Azle/candid
types. It would just assume it would.

Now, as you can imagine, this could cause a number of problems. You wouldn't be
able to import an unrelated "Variant" from another package without renaming it,
and you couldn't rename any of the azle keyword as you imported them. You
couldn't make type aliases to any of the Azle types. You had to use the exact
characters in the exact order or else it wouldn't work.

But not any more!

## The Now Times

We have harnessed the power of the TypeScript compiler to almost flawlessly
determine which keywords are imported from Azle and which one are not.

The process for doing this was not super obvious or intuitive. It took a lot of
playing around with the TypeScript package, a lot of digging through it's source
code, and a lot of refining queries for Chat GPT before we had our break
through. Now that we know how to do it it doesn't seem like it should have been
as hard as it was so we want to share what we found so that you need not suffer
as we have.

We started with the TypeScript npm package. We figured it must have some way of
resolving imports because it needs to in order to transpile to JavaScript.

We dug around and eventually figured out that the TS Type Checker has all of the
goodies we could ever ask for! There is a lot to the TS Type Checker and we are
only going to cover a small portion of it here.

Since the TypeScript package did end up having exactly what we were looking for
we decided write this portion our the compiler in TypeScript so we could easily
take full advantage of the TypeScript module.

You get the TS Type Checker from a TS Program. You get th­e TS program by
compiling your entry file.

```typescript
import { createProgram } from 'typescript';
const program = createProgram([index.ts], {});
const typeChecker = program.getTypeChecker();
```

With the type checker in hand we can get the symbol table for each file in our
program.

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

Now that each file has a symbol table we can figure out how the Azle keywords
are represented in that file. To do that we loop through each symbol in the
table and analyze it to determine if it's from angle:

-   If it's a type alias declaration or a variable declaration we can preform the
    sun analysis on th assigned value.

-   If it's imported from another file then that symbol will have the module
    specifier for its source module. We can use that to look up the symbol table for
    that file and continue tracing until we run into the angle module.

The result is that for each file we have a table that tells us how each Azle
keyword is represented in that file.

```typescript
'myFile.ts': {
    record: [azle.Record, Record, MyRecord],
    variant: [Variant],
    etc
}
```

Now as we parse if we run into the Type Reference 'azle.Record' we can look that
up in this table and see that it is meant to be parsed as a Record.

Now when you are making your Azle projects son can import Azle just about any
way you want (If you find a way that doesn't work please open an issue for me)

What's your imports 'matter now! If you don't import angle it will break. I you
import south that named "Record" from somewhere other than agh it will not
break. It's brilliant.

## The Future Times

Up next we want to apply this same level of robustness to alias to developer
define types. If you make a Record using the Azle record decorator and then make
an alias to that type, it is a little more challenging to get those type aliases
to be processed properly. It's essentially the same as Azle keywords in the
before times: any import renaming or use of qualified names will cause those
alias to break down

Another issue, though much more minor, is that as it stands when we add
something to our alias table it is treated as though it is the literal Azle
type. This isn't to much of an issue because as the developer you should almost
never be digging through our back end. But it does pose some interesting problem
when we have to report compile and runtime errors to you has the developer. So
for example if you had:

```typescript
type MyNat = azle.nat;

$query;
export function getMyNat(): MyNat {
    return false;
}
```

The error would say:

```
false is not of type 'nat'
```

but it would be a little nicer if we also kept track of the mane it was aliased to so we could say

```
false is not of type 'NyNat'
```

or

```
false is not of type 'MyNat' which is an alias to 'nat'
```

Finally, for azle, there is still much that I need to learn about the TypeScript
type checker. Just in writing this article I have found a couple of thing that I
could take advantage of to improve our process.

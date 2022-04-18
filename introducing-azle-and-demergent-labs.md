# Introducing Azle and Demergent Labs

April 16, 2022

## TLDR

Write canisters in TypeScript with [Azle](https://github.com/demergent-labs/azle) and keep tabs on [Demergent Labs](https://github.com/demergent-labs) for GraphQL, Python, and more for the Internet Computer.

## Introduction

Hi,

I would like to introduce you to two new things.

## Azle

The first is [Azle](https://github.com/demergent-labs/azle), a [TypeScript](https://www.typescriptlang.org/) Canister Development Kit (CDK) for the [Internet Computer](https://internetcomputer.org/) (IC). If you're new to the IC, that last sentence was probably a mouthful. First things first, if you are to understand Azle you must first understand the IC.

The IC is a new decentralized cloud platform. As opposed to most cloud platforms that exist today, the IC aims to be owned and controlled by multiple parties (instead of one centralized legal entity). It's a decentralized logical computer built from an open protocol that will be owned and operated by independent entities. This has many benefits, and those benefits are broadly discussed in the Web3 and blockchain communities (seek out those communities for more information if you're interested).

The fundamental application unit of the IC is called a canister. Canisters contain the code and state of your application, and users interact with them through simple RPC calls over the internet. Basically canisters expose functions that can be called through network requests like HTTP.

For the past couple years most developers have written their canisters in [Motoko](https://smartcontracts.org/docs/language-guide/motoko.html) or [Rust](https://www.rust-lang.org/). [DFINITY](https://dfinity.org/) (the foundation that created Motoko and the Internet Computer) has put a lot of effort into making Motoko and Rust first-class languages on the IC. They've done a great job, and the burgeoning community has been [very productive](https://n7ib3-4qaaa-aaaai-qagnq-cai.raw.ic0.app/#/).

But there are a few problems with using Motoko and Rust on the IC. Let's start with Rust.

Rust is a very difficult language to learn. It's a systems or low-level language. It gives you extreme control over many aspects of the environment it runs in, and thus is more unwieldly. It's a beautiful language that I love to use, but many developers (especially newcomers to programming in general) will have an extremely difficult time learning Rust. There's a reason that high-level languages exist.

Motoko is a high-level language. It abstracts away many of the difficulties of low-level languages like Rust. That's why developers love high-level languages, they are simply easier to use. But Motoko is a very new language. It lacks a large community, many libraries, mature documentation, YouTube videos, blog posts, podcasts, and generally years of expertise. Put simply it is a new language, and there hasn't been sufficient time nor interest for these resources to arise.

Creating and fostering the adoption of a new language is extremely difficult. It's a huge point of friction for developers to learn a new language with all of its nuance, idioms, libraries, package managers, etc.

Enter [Azle](https://github.com/demergent-labs/azle). Azle is a TypeScript CDK. It allows developers to write their canisters in TypeScript (and technically JavaScript as well). This is a big deal. TypeScript and JavaScript are some of the most popular programming languages ever to exist. They have years' worth (for JavaScript decades) of community, libraries, documentation, YouTube videos, blog posts, podcasts, and expertise. There are millions of TypeScript and JavaScript developers that already exist and already love TypeScript and JavaScript. Why take that away from them?

Here's the source code for a simple canister written in TypeScript with Azle. It's just a read-only query function that returns "Hello World!":

```typescript
import { Query } from 'azle';

export function helloWorld(): Query<string> {
  return 'Hello World!';
}
```

Deploying that to the IC with a simple `dfx deploy --network ic` will instantly allow anyone in the world to call this `helloWorld` function and see that beautiful phrase.

Here's the source code for a simple canister that is a key-value store:

```typescript
import {
    Query,
    Update,
    Opt
} from 'azle';

type Store = {
    [key: string]: string;
};

let store: Store = {};

export function get(key: string): Query<Opt<string>> {
    return store[key] ?? null;
}

export function set(key: string, value: string): Update<void> {
    store[key] = value;
}
```

That's a database folks. One `dfx deploy --network ic` and you have exposed a simple database to the world.

And there you have it, just a taste of what it's like to write canisters with TypeScript and JavaScript on the IC. Let's end our discussion of Azle with some wise words:

* [Brendan Eich](https://en.wikipedia.org/wiki/Brendan_Eich) once said ["always bet on JS"](http://brendaneich.github.io/ModernWeb.tw-2015/#74)
* [Jeff Atwood](https://en.wikipedia.org/wiki/Jeff_Atwood) once said ["any application that can be written in JavaScript, will eventually be written in JavaScript"](https://blog.codinghorror.com/the-principle-of-least-power/)

Watch JavaScript eat the Internet Computer.

## Demergent Labs

And now on to the second thing I would like to introduce you to, a new company called Demergent Labs. Our goal is to accelerate the adoption of Web3 and the Internet Computer. We're starting off with three flagship projects: [Azle (TypeScript CDK)](https://github.com/demergent-labs/azle), [Sudograph (GraphQL Database)](https://github.com/sudograph/sudograph), and [Kybra (Python CDK)](https://github.com/demergent-labs/kybra).

Our thesis is simple: By focusing on the technologies that are really working well in the Web2 world, we believe we can accelerate the adoption of Web3. We can also greatly improve some of the technologies that made Web2 great ([decentralized npm registry](https://github.com/demergent-labs/deregistry) anyone?).

We are removing barriers to entry, making it extremely simple for developers to come over to the IC and write applications that take advantage of its unique characeteristics and benefits over the centralized cloud providers. We're also experimenting with new intellectual property, governance, and funding mechanisms that could solve some of the sustainability issues that have plagued open source software since its inception.

There's a lot of work to do in Web3 and for the Internet Computer. The dream of an infinitely scalable and unhackable decentralized computer is beautiful. We aren't there yet, but we're working hard to get there.

Please, in your own way and with your skills and talents, help us build this amazing new future.

Yours truly,

@lastmjs

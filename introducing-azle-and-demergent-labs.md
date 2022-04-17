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

Motoko is a high-level language. It abstracts away many of the difficulties of low-level languages like Rust. That's why developers love high-level languages, they are simply easier to use. But Motoko is a very new language. It lacks a large community, documentation, videos, blog posts, podcasts, and expertise. It is simply a new language, and there hasn't been sufficient time nor interest for these things to arise.

Creating and expecting adoption of a new language is something very difficult. Many languages are created but most fail.

Enter [Azle](https://github.com/demergent-labs/azle). Azle is a TypeScript CDK. It allows developers to write their canisters in TypeScript (and technically JavaScript as well). This is a big deal. TypeScript is one of the most popular programming languages ever to exist. It has years of documentation, blog posts, YouTube videos, podcasts, and expertise. There are millions of TypeScript developers that already exist.

Here's the source code for a simple canister written in TypeScript with Azle. It's just a read-only query function that returns "Hello World!":

```typescript
import { Query } from 'azle';

export function helloWorld(): Query<string> {
  return 'Hello World!';
}
```

Deploying that to the IC with a simple `dfx deploy --network ic` will instantly allow anyone in the world to call your `helloWorld` function and see that beautiful phrase. What an amazing developer experience. Hats off to DFINITY for creating such an amazing platform.

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

That's a database folks. One `dfx deploy --network ic` and you have exposed a simple database to the world. The vision of the IC is to make web applications this easy to write. Eventually we will have unbounded scalability, near unhackability, and everything you want and need from a cloud platform, while focusing on the logic of your code.

Asking developers to learn an entirely new language that itself is new (Motoko) is a very hard ask. It's a burden, it's a barrier, it's a hindrance to adoption of the IC. Asking developers to learn a difficult low-level language (Rust) is also a very hard ask. Not that some developers will not choose to learn Motoko or Rust, many will and have. But there is now an alternative, which is to reuse the languages and skills that many developers already cherish.

Like Brendan Eich says, "Always Bet on JS". 

Like Jeff Atwood has said, "Anything that can be written in JavaScript eventually will be written in JavaScript".

Watch JavaScript eat the Internet Computer.

## Demergent Labs

And now on to the second thing I would like to introduce you to, my new company Demergent Labs. We aim to accelerate the adoption of Web3 and the Internet Computer. We're starging off on our mission with three flagship projects: Azle (TypeScript CDK), [Sudograph (GraphQL Database)](https://github.com/sudograph/sudograph), and Kybra (Python CDK).

By focusing on the technologies that are really working well in the Web2 world, we believe we can accelerate the adoption of Web3 (obviously). We are removing barriers to entry, making it extremely simple for developers to come over to the IC and adopt the platform, taking advantage of its unique characeteristics and benefits over the centralized cloud providers.

Azle and Sudograph are currently in beta, with 1.0 releases slated for this year (Kybra 1.0 as a stretch goal). The dream of an infinitely scalable and unhackable decentralized computer is beautiful. We aren't there yet, but we're working hard to get there.

Come join us.

Yours truly,

@lastmjs

# Introducing Azle and Demergent Labs

April 16, 2022

TLDR check out [Azle](https://github.com/demergent-labs/azle) and Demergent Labs.

Hi,

I would like to introduce you to two new things.

## Azle

The first is [Azle](https://github.com/demergent-labs/azle), a [TypeScript](https://www.typescriptlang.org/) CDK for the [Internet Computer](https://internetcomputer.org/) (IC). But if you are to understand Azle, you must first understand the IC.

The IC is a new decentralized cloud platform. As opposed to most cloud platforms that exist today, the IC aims to be owned and controlled by multiple parties (instead of one centralized legal entity). It's a decentralized logical computer built from an open protocol that will be owned and operated by independent entities. This has many benefits, and those benefits are broadly discussed in the Web3 and blockchain communities (seek out those communities for more information if you're interested).

The fundamental application unit of the IC is called a canister. Canisters contain the code and state of your application, and users interact with them through simple RPC calls over the internet. Basically canisters expose functions that can be called through network requests like HTTP.

For the past couple years most developers have written their canisters in [Motoko](https://smartcontracts.org/docs/language-guide/motoko.html) or [Rust](https://www.rust-lang.org/). [DFINITY](https://dfinity.org/) (the foundation that created Motoko and the Internet Computer) has put a lot of effort into making Motoko and Rust first-class languages on the IC. They've done a great job, and the burgeoning community has been [very productive](https://n7ib3-4qaaa-aaaai-qagnq-cai.raw.ic0.app/#/).

But there are a few problems with using Motoko and Rust on the IC. Let's start with Rust.

Rust is a very difficult language to learn. It's a systems or low-level language. It gives you extreme control over many aspects of the environment it runs in, and thus is more unwieldly. It's a beautiful language that I love to use, but many developers (especially newcomers to programming in general) will have an extremely difficult time learning Rust. Even though it is a joy to use once you get over the learning curve and has many ergonomic features that many other low-level languages don't have, it is still a low-level language. There's a reason that high-level languages exist.

## Demergent Labs

Demergent Labs aims to accelerate the adoption of Web3 and the Internet Computer.

Yours truly,

@lastmjs

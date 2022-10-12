# Demergent Labs Master Plan Part 1

October 12, 2022

Our self-imposed vision at Demergent Labs is to accelerate the adoption of [Web3](https://en.wikipedia.org/wiki/Web3), the [Internet Computer](https://internetcomputer.org/) (IC), and sustainable open source.

We believe that a decentralized world computer has the potential to bring various material benefits to mankind. Though we are interested in the success of any decentralized world computer, currently our focus is almost exclusively on the IC.

We are very interested in the success of the IC. Due to some personal efforts and thanks to a grant from [DFINITY](https://dfinity.org/), we have already put signifiant engineering efforts towards creating fundamental open source developer tools that we hope will enable many developers to come to the IC. We would like to lay out here our plans for the next 1-2 years.

## Languages

Master Plan Part 1 is all about languages, both general-purpose programming languages and database languages.

### General-purpose Programming Languages

General-purpose programming languages are arguably the most fundamental tools that developers need to build applications on any computer. The IC in particular has had good support for [Motoko](https://internetcomputer.org/docs/current/developer-docs/build/cdks/motoko-dfinity/motoko/) and [Rust](https://internetcomputer.org/docs/current/developer-docs/build/cdks/cdk-rs-dfinity/), but almost no other languages. Our belief is that requiring developers to learn a new language presents significant barriers to adoption of a new platform like the IC. Due to the robust communities of millions of developers in both the TypeScript/JavaScript and Python communities, starting with CDKs for these communities has the potential to provide enormous benefits to the IC ecosystem.

Thus we are beginning by enabling `TypeScript`, `JavaScript`, and `Python` through two [Canister Development Kits](https://internetcomputer.org/docs/current/developer-docs/build/cdks/) (CDKs). `TypeScript` and `JavaScript` are enabled by [Azle](https://github.com/demergent-labs/azle), which is in a healthy beta stage. `Python` is enabled by [Kybra](https://github.com/demergent-labs/kybra), which is in a very early alpha stage.

In addition to the individual CDKs, we are working on a CDK framework that we hope will allow for more rapid development of CDKs by sharing common functionality between them. The initial version of the framework is nearing completion, and we will soon plug it into both Azle and Kybra, which will hopefully speed up the development of Kybra significantly.

The next low-hanging fruit for the CDK framework will probably be compile-to-JS languages like `Kotlin`, `Dart`, `Elm`, `PureScript`, `ClojureScript`, and various others. Beyond those languages, the framework is intended to work well with higher-level languages that have a runtime that can be compiled into Wasm, and most especially runtimes written in, or that can interoperate well with, Rust.

Demergent Labs doesn't have any concrete plans to pursue languages beyond `TypeScript`, `JavaScript`, and `Python`, but we hope that our efforts will provide a solid foundation for other teams to implement languages of their choosing.

### Database Languages

Beyond choosing and proficiently utilizing a general-purpose programming language, one of the next most signficant challenges to building applications is the data storage and retrieval system. Applications must be able to efficiently persist data in various formats, often with complex interrelationships, and be able to efficiently retrieve specific data based on often complex ad-hoc requirements. And once such a system is created, providing developers simple yet flexible access to that system can be a major challenge.

The IC has yet to find a data storage and retrieval model that is scalable and has an excellent developer experience (DX). We are attempting to solve this problem with [Sudograph](https://github.com/sudograph/sudograph), a GraphQL database for the IC.

Sudograph is in a solid beta stage. It is currently focused on creating an excellent DX within a single canister. Though the capacity of individual canisters is relatively small (~4-8GiB), that capacity will continue to grow over time. We wish to push that capacity as far as possible, thus allowing us to maintain simplicity in Sudograph's design and in its DX. Once we reach the scalability limits of a single canister, we may consider how to maintain Sudograph's DX across multiple canisters.

We foresee various database languages and underlying database architectures will be necessary to service the many different application use cases on the IC. We have chosen to start with GraphQL as the database language, and to implement a theoretically simple relational-style database under-the-hood known as [Sudodb](https://github.com/sudograph/sudograph/tree/main/sudodb).

Similar to the CDK framework, we plan to look into creating a framework that will allow for multiple database languages to be built on top of our underlying database. We also plan to look into doing the reverse, allowing database languages to choose their underlying database. The idea of this framework is much less mature than the CDK framework, but if it makes sense to build it then we hope it provides similar benefits, allowing many teams to build many modular database languages and database implementations to their liking.

## Business Models and Decentralization

It is likely that multiple parts of our Master Plan will incorporate experimenting with business models that are highly compatible with the open source ethos. We call these business models open source native.

With that being said, during Master Plan Part 1 a secondary goal to delivering fundamental open source developer tools is finding sustainable open source native business models and decentralizing our projects. We imagine our most popular projects eventually becoming DAOs that are directly owned and controlled by various interested parties. Sources of revenue could be from various token and/or cycle sharing models.

It is very early days for these unproven business models, but we are deeply committed to finding a sustainable way for open source developers to make a living doing what they do best and love most, without having to look for subsidies from larger companies.

## Beyond Part 1

We have some ideas for Master Plan Part 2 (e.g. decentralized package registries for npm, pip, and cargo) but we're not ready to decide just yet. We are excited to make significant progress in the next 1-2 years and then reasess based on the state of the world then.

Thanks for reading,

- Jordan Last, Chief Executive/Engineering Officer @ Demergent Labs

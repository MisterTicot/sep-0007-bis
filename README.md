[Introduction](README.md)
 • [Generic Parameters](level0A.md) • [Request Authentication](level0B.md)
 • [Passing Transaction by txHash](level1A.md)
 • [Passing Transaction by XDR](level1B.md)
 • [Payment Request](level2A.md)
 • [Donation Request](level2B.md)

# Introduction

## Preamble

```
SEP: <to be assigned>
Title: HTTP Transaction Passing
Author: Antoine Bodin <mister.ticot@cosmic.plus>
Status: Draft
Created: 2018-11-24
```

## Simple Summary

This SEP specifies how transactions must be encoded prior to be passed over HTTP
requests.

## Abstract

When it comes to asymmetric cryptography, the golden rule is *"Never share your
private keys!"*. To better enforce this rule, we need the appropriate
infrastructure to pass unsigned transactions around, in particular to user
wallet.

More generally speaking, we also need a transaction passing protocol for
services such as multi-signatures coordinators, smart-contract coordinators,
blockchain explorers, and other things to come.

This SEP goes for the "greater abstraction" by introducing a generic transaction
passing mechanism. It works for both GET and POST requests and is modular enough
to handle the afford-mentioned use cases.

## Motivation

The existing transaction request mechanism (SEP-0007) suffers from the following
drawbacks:

* It is over-specialized: it can only pass transactions to user wallet over
  custom URI scheme. The temptation to use it raw inside POST requests in the
  *"Bootstrapping Multisig Coordination SEP"* demonstrates the need for a more
  generic solution.
* It does it wrong: when it comes to web-wallets, it relies on a non-standard &
  deprecated web feature that only 35% of the browsers can handle. Because most
  people use web-wallets, this means that SEP-0007 will only works for less than
  half of the users.
* It is missing compactness: Compactness is key to passing transactions over
  URLs. The maximum length of an URL is 2,083 characters. A typical operation
  takes around 50-200 characters once encoded into an XDR. So we can squeeze in
  about 10-40 operations into the request. Ideally, we should be able to put 100
  operations in there.
* It got too much requirements: it imposes to implement at once both an
  arbitrary transaction request (operation `tx`) and a payment request
  (operation `pay`). In practice, those are two very different things to
  implement, and a service may only need one or the other.
* It got too much requirements (2): it introduces a mandatory authentication
  feature that nobody needs yet. It should be made optional.
* It's outdated: while a number of flaws have been discovered before the
  protocol even got accepted, the SEP was never updated to address them. In
  particular, the `&message` parameter introduces security issues, and the
  `&pubkey` parameter is mandatory but useless (it's easy to know which account
  should sign a transaction).
* It doesn't have a reference implementation: It's 6 months old so it looks like
  it will never have one.

The present SEP is a new iteration of SEP-0007 that aims at fixing those issues
while retaining any meaningful feature from its predecessor.

## Specification

### Introduction

This SEP is made of several modules. Depending on your needs, you may want to
implement them all or only a subset of them. Each module have a mandatory part
(the base) and an optional part (the extensions).

Extensions are features that may be needed by some services and useless for
others. If you choose not to support a module or an extension, you must
explicitly reject requests that makes use of them.

### SEP Organization

* Level 0: General Specifications
  * Level 0A: [Base Parameters](level0A.md)
  * Level 0B: [Request Authentication](level0B.md) (extension)
* Level 1: Arbitrary Transaction Passing
  * Level 1A: [Passing Transaction by txHash](level1A.md) (mode: **txhash**)
  * Level 1B: [Passing Transaction by XDR](level1B.md) (mode: **xdr**)
* Level 2: Pseudo Operations
  * Level 2A: [Payment Request](level2A.md) (mode: **pay**)
  * Level 2B: [Donation Request](level2B.md) (mode: **donate**)

Level0 entries are for general specifications. Level 0A defines base parameters
that are valid for most modes and must be complied with. Other Level 0 entries
are optional extensions.

Each Level1 & Level2 entry describes a particular mode of passing a transaction.
Those entries are independent and optionals.

Level1 entries are about passing arbitrary transactions. It targets a wide set
of use cases, including wallets that wish to accept arbitrary transaction
request.

Level2 entries introduce pseudo-operations that need additional computation or
user input before building a transaction. It mostly targets wallets.


## Usual Ways to Pass Transactions

### GET Request, POST Request

The parameters specified in this SEP can be passed either over a GET or a POST
request. Typically, a POST request would simply pass the query string into its
body as a `application/x-www-form-urlencoded` content.

To show the equivalence between the different ways of writing requests, examples
will always include the request encoded both as a query string and as a JSON
object (a common way to read/pass requests):

```
// Query String
parameter1=value1&parameter2=value2&...&parameterN=valueN

// JSON Object
{ parameter1: "value1", parameter2: "value2", ... parameterN: "valueN" }
```

### Universal Link

Todays standards allow to reach applications from normal URL. This
indiscriminately supports endpoints, websites, web-applications and softwares;
Hence the name of universal link. It is nothing more that a GET request:

```
https://any-wallet.org/tx?parameter1=value1&parameter2=value2&...&parameterN=valueN
```

### Custom URI Scheme

Custom URI Scheme was the standard way to pass request to software. It is being
deprecated but is still in use. They can be used to pass a transaction query
string:

```
web+stellar:parameter1=value1&parameter2=value2&...&parameterN=valueN
```

**Question to reviewers:** Should we still define a custom URI scheme for
Stellar? Do we keep web+stellar or do we switch to stellar:?


## Rationale

Generally speaking, a protocol is a set of rules about how to do something,
while a standard is a widely accepted consensus. The difference between the two
is the reality test: for a standard to emerge, a protocol must fill developers
needs rather than protocol writer wishes.

In particular, it must not try to force developers to implement something they
don't actually think they need, but present them with good quality solutions
that they may want to embrace.

The present SEP have been written with that concern in mind. Its modularity
allows services to easily implement transaction passing in their work-flow
without having to introduce a new set of features beforehand. This is critical,
because not all service need or wish to implement each of the presented
features.

For instance, Level2 entries are mostly meaningful in the context of wallets.
Level1 entries are more generic, but here again the set of feature you'll
implement depends on the what your service is able to do.

This approach allowed to retain & improve all the meaningful SEP-0007 features
while keeping the implementation difficulty reasonable.

## Backwards Compatibility

This new iteration of SEP-0007 is not backward compatible because of the
semantic changes it introduces; However, all meaningful feature have been
retained, so upgrading from SEP-0007 is mostly a matter of re-naming a few
parameters.

[Introduction](README.md)
 • [Generic Parameters](level0A.md) • [Request Authentication](level0B.md)
 • [Passing Transaction by txHash](level1A.md)
 • [Passing Transaction by XDR](level1B.md)
 • [Payment Request](level2A.md)
 • [Donation Request](level2B.md)

# Level 0A: Base Parameters

Those are generic parameters that are valid for most (or all) of the Level1 &
Level2 modules.

* **Base parameters:** mode, network
* **Extension parameters:** horizon, callback

## Base Parameters (mandatory)

**mode: {String}**

The mode parameter indicates whic Level1 or Level2 module is being used to pass
the transaction.

```
// Query String
mode=xdr&...

// JSON Object
{ mode: "xdr", ... }
```

* Request must be rejected if this parameter is missing.
* Request must be rejected if this parameter value is invalid.

**network: "public"|"test"|${network_passphrase}|undefined**

The network parameter indicates the network on which the passed transaction is
valid. When no network is given, the transaction is said "network-independent".

```
// Query String
mode=...&network=public

// JSON Object
{ mode: ..., network: "public" }
```

You are free to support only part of the networks. If you do so, you must
explicitely reject any transaction that use a network you don't support.

Network-independent transactions are a feature that makes sense for wallets.
When receiving a network-independent transaction request (such as `set
homeDomain to: 'stellar.org'`), wallets can either choose a network on the
behalf of the user (for example when it only support public network), or to let
the user choose any account of any network for signing.

Services for which network-indepent transaction passing doesn't makes sense, or
that doesn't support them, must reject them explicitely.

## Extension Parameters (optional)

**horizon: {URL}**

The horizon parameter indicate the URL of a fallback Horizon node for a custom
network.

```
// Query String
mode=...&network=custom&horizon=horizon-custom.example.org

// JSON Object
{ mode: ..., network: "custom", horizon: "horizon-custom.example.org" }
```

Implementators must pay attention to the fact that **wrongly implementing this
parameter could lead to serious security flaws**. Indeed, a transaction emitter
could leverage the power of passing a malicious horizon node to abuse the user
by various means.

This parameter must be a _fallback_ URL that conveniently enable services to
support unknown custom networks. You must ignore it when your service already
know about an Horizon node for the selected network. In particular, **this
parameter must never be used for public & test networks**.

* In case the URL doesn't include a protocol, `https://` must be used.
* This parameter must be ignored when an Horizon node is already known for the
  requested custom network.
* Request must be rejected if this parameter is used with public or test
  network.
* Request must be rejected if this parameter is used for a network-independent
  transaction.

**callback: {URL}**

The callback parameter indicates when the signed transaction must be sent.

```js
// Query String
?mode=...&callback=tx.example.org

// JSON Object
{ mode: ..., callback: "tx.example.org"}
```

TODO: more precisions about how transaction must be posted. Probably the same
way we're posting it to Horizon endpoint?

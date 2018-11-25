[Introduction](README.md)
 • [Generic Parameters](level0A.md) • [Request Authentication](level0B.md)
 • [Passing Transaction by txHash](level1A.md)
 • [Passing Transaction by XDR](level1B.md)
 • [Payment Request](level2A.md)
 • [Donation Request](level2B.md)

# Level 1A: Passing Transaction by txHash

txHash is an unique identifier for a transaction. There are two situations in
which you may want to pass transaction by txHash:

1. The transaction already got validated on the network.
2. The contacted service already knows about the transaction.

**Note:** Services which doesn't support this mode must explicitely say so when
rejecting the request.

## Parameters

* **Generic base parameters:** mode, network
* **Generic extension parameters (optional):** horizon, callback, signature,
  signer
* **TxHash base parameter:** txhash

### Generic Base Parameters (mandatory)

* **mode** must be set to "txhash"
* **network** must be set to a value: txhash mode is not compatible with
  network-independent transactions.

### Generic Extension Parameters (optional)

* When dealing with validated transactions, **callback** parameter may be
  meaningless. Implementators are free to reject it when relevant.

### TxHash Base Parameters (mandatory)

**txhash: ${txhash}**

The txhash parameter must be set to the txhash of the transaction.

```js
// Query String
mode=txhash&...&txhash=a2z0...7i28

// JSON Object
{ mode: "txhash", ..., txhash: "a2z0...7i28" }
```

* The request must be rejected if this parameter is missing.

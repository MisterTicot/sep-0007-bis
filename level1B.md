[Introduction](README.md)
 • [Generic Parameters](level0A.md) • [Request Authentication](level0B.md)
 • [Passing Transaction by txHash](level1A.md)
 • [Passing Transaction by XDR](level1B.md)
 • [Payment Request](level2A.md)
 • [Donation Request](level2B.md)

# Level 1B: Passing Transaction by XDR

XDR is the standard way of encoding Stellar transactions. It can be passed over
HTTP requests as a query string.

**Note:** Services which doesn't support this mode must explicitly say so when
rejecting the request.


## Parameters

* *Generic base parameters:* mode, network
* *Generic extension parameters (optional):* horizon, callback, signature,
  signer
* *XDR base parameter:* xdr

### Generic Base Parameters (mandatory)

* **mode** must be set to "xdr"

### XDR Base Parameters (mandatory)

**xdr:** ${xdr}

The xdr parameter must be set to the XDR of the transaction.

```js
// Query String
?mode=xdr&...&xdr=AAAA...AA==

// JSON Object
{ mode: "xdr", ..., xdr: "AAAA...AA==" }
```

* The request must be rejected if this parameter is missing.


## Extensions (optional)

Those extensions makes sense in the context of passing transaction to wallets
for signing purpose.

### Sequence-independent transaction

A sequence-independent transaction is a transaction request for which the
sequence number must be set by the wallet prior to signing. Such transaction is
built by setting the sequence number to 0.

Services for which this feature doesn't makes sense, or that doesn't wish to
support it, must fail explicitly when receiving a transaction with sequence
number 0.

Services that support that feature must replace the sequence number by its
current value plus one prior to signing.

### Source-independent transaction

A source-independent transaction is a transaction request that is valid for any
account, such as `join inflation pool X` or `trust asset A`. Such transaction is
built by setting the transaction source to the account 0 (`GAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAWHF`)

Services for which this feature doesn't makes sense, or that doesn't wish to
support it, must fail explicitly when receiving a transaction with this
transaction source.

Services that support that feature must replace the transaction source by the public key of the signer prior to signing.

## Rationale

While the use of "magic numbers" will not be consensual among reviewers, this is
a nice & compact design.

In CosmicLink, I used an arbitrary sequence/account number along with the
injunction that wallet must replace it with user values (`&stripSource`,
`&stripSequence`). However, this is weird and bloated.

You'll have to pass account & sequence numbers anyway and, if it have to be
replaced, it is more meaningful to pass neutral values than arbitrary ones. It
also save about 20-30 characters which leave space for another operation into a
GET request.

On other blockchains, it is an usual thing that the account 0 is the place were
asset are sent to get burned. Stellar doesn't really needs that, but that shows
that giving special meaning to a public key is not an heresy.

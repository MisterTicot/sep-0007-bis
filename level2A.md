[Introduction](README.md)
 • [Generic Parameters](level0A.md) • [Request Authentication](level0B.md)
 • [Passing Transaction by txHash](level1A.md)
 • [Passing Transaction by XDR](level1B.md)
 • [Payment Request](level2A.md)
 • [Donation Request](level2B.md)

# Level 2A: Payment Request

This mode encodes a payment request that translate in either a `payment` or a
`pathPayment` operation.

**Note:** Services which doesn't support this mode must explicitly say so when
rejecting the request.

## Work-flow

1. The user is presented with a payment request.
2. The user select the asset she wants to pay with.
3. If user select a different asset than what **destination** wants, the payment
   is sent using a `pathPayment` operation. Else, a `payment` operation is used.

**Note:** Implementors are free to partially implement this functionality by
using only `payment` operations. A partially implemented donation feature is
better than nothing at all.

## Parameters

* *Generic base parameters:* mode, network
* *Generic extension parameters (optional):* horizon, callback, signature,
  signer
* *Payment request mandatory parameters:* destination, amount
* *Payment request optional parameters:* asset, source

### Generic Base Parameters (mandatory)

* **mode** must be set to "pay"

### Payment Request Mandatory Parameters (mandatory)

**destination:** ${publicKey|federatedAddress}

This is the destination of the payment.

**amount:** {Number}

This is the amount of the payment.

### Payment Request Optional Parameters (mandatory)

**asset:** ${code}:${issuer}

If asset is not provided, we use network native asset (*XLM* on public/test
network). Issuer may be either a publicKey or a federated address.

**source:** ${publicKey|federatedAddress}

Optionally, the request can enforce a source account to be used for the payment.

## Example

```
// Query String
mode=pay&network=public&callback=payment.example.org&destination=shop*example.org&amount=120&asset=USD:anchor*example.org

// JSON Object
{
  mode: "pay",
  network: "public",
  callback: "payment.example.org"
  destination: "shop*example.org"
  amount: "120",
  asset: "USD:anchor*example.org",
}
```


## Rationale

### Accepting federated address

Federated address are a first-level feature of Stellar network, hence, it must
be supported here. Implementors who doesn't wish to do so must explicitly
reject the request.

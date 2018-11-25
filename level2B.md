[Introduction](README.md)
 • [Generic Parameters](level0A.md) • [Request Authentication](level0B.md)
 • [Passing Transaction by txHash](level1A.md)
 • [Passing Transaction by XDR](level1B.md)
 • [Payment Request](level2A.md)
 • [Donation Request](level2B.md)

# Level 2B: Donation Request

This mode encodes a donation request that can translate either as a `payment` or
as a `pathPayment` operation. The donation may be sent in any asset accepted by
the destination.

**Note:** Services which doesn't support this mode must explicitly say so when
rejecting the request.

## Work-flow

1. The user is presented with a donation request.
2. The user set an amount and an asset to be sent to **destination**.
3. If **destination** doesn't trust this asset, the wallet send it using a
   `pathPayment` operation. Else, it can use a `payment` operation.

**Note:** Implementors are free to partially implement this functionality by
using only `payment` operations or by restricting asset to allow only Stellar
Lumens for donations. A partially implemented donation feature is better than
nothing at all.

## Parameters

* **Generic base parameters:** mode, network
* **Generic extension parameters (optional):** horizon, callback, signature,
  signer
* **Donation request mandatory parameters:** destination

### Generic Base Parameters (mandatory)

* **mode** must be set to "donate"

### Payment Request Mandatory Parameters (mandatory)

**destination: ${publicKey|federatedAddress}**

This is the destination of the donation.

## Example

```
// Query String
mode=donate&network=public&destination=ngo*example.org

// JSON Object
{ mode: "donate", network: "public", destination: "ngo*example.org" }
```

## Rationale

### Accepting federated address

Federated address are a first-level feature of Stellar network, hence, it must
be supported here. Implementors who doesn't wish to do so must explicitly
reject the request.

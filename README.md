# Test Cases for a Tezos Integration

This document includes a boiler plate suite of tests for a Tezos integration. Code which passes these test cases currently runs in production in the Trust Wallet app.

These tests are implemented as C++ and you can check out / run them here: https://github.com/trustwallet/wallet-core/tree/master/tests/Tezos

Tests are ordered in simplicity, it is recommended you work through these test cases from top to bottom. 

## Address Validity
Valid Addresses:
```
tz1Yju7jmmsaUiG9qQLoYv35v5pHgnWoLWbt
tz2PdGc7U5tiyqPgTSgqCDct94qd6ovQwP6u
tz3VEZ4k6a4Wx42iyev6i2aVAptTRLEAivNN
KT1VsSxSXUkgw6zkBGgUuDXXuJs9ToPqkrCg
```
Invalid Addresses:
```
NmH7tmeJUmHcncBDvpr7aJNEBk7rp5zYsB1qt // Invalid prefix, valid checksum
tz1eZwq8b5cvE2bPKokatLkVMzkxz24z3AAAA // Valid prefix, invalid checksum
1tzeZwq8b5cvE2bPKokatLkVMzkxz24zAAAAA // Invalid prefix, invalid checksum
```

## Address Derivation
Generally, clients of Tezos should prefer `tz1` addresses. `tz2` or `tz3` addresses could also be derived from the given public key, but any integration should only really need to generate one address type from keypairs.

### Public Key To Address
```
Input Public Key Bytes in Hex: 01fe157cc8011727936c592f856c9071d39cf4acdadfa6d76435e4619c9dc56f63
Output: tz1cG2jx3W4bZFeVGBjsTxUAG8tdpTXtE8PT
```

### Private Key to Address

```
Input Private Key Bytes in Hex: 4646464646464646464646464646464646464646464646464646464646464646
Output: tz1gcEWswVU6dxfNQWbhTgaZrUrNUFwrsT4z
```

### Originated Address Derivation
Given an operation group hash and an operation index, the expected value of an address produced by an `origination` operation is deterministic:

```
Inputs:
  Operation Hash: oo7VeTEPjEusPKnsHtKcGYbYa7i4RWpcEhUVo3Suugbbs6K62Ro
  Opertation Index: 0
  
Output: KT1WrtjtAYQSrUVvSNJPTZTebiUWoopQL5hw
```

## Forging Tests
Expected input and outputs for forging. 
### Booleans
true:
```
Input: true
Output: ff
```
false:
```
Input: false
Output: 00
```
### Unsigned Integers
```
Input: 0
Output: 00
```

```
Input: 10
Output: 0a
```

```
Input: 14
Output: 14
```

```
Input: 150
Output: 9601
```

```
Input: 1113602
Output: 82fc43
```

### Signed Integers
Signed integers are forged differently than unsigned integers. However, unsigned integers are not used in most exchange wallet integrations as Transactions, Delegation, Origination and Reveal operations only use unsigned integer data types. 

### Addresses
tz1:
```
Input: tz1eZwq8b5cvE2bPKokatLkVMzkxz24z3Don
Output: 0000cfa4aae60f5d9389752d41e320da224d43287fe2
```

tz2:
```
Input: tz2Rh3NYeLxrqTuvaZJmaMiVMqCajeXMWtYo
Ouput: 0001be99dd914e38388ec80432818b517759e3524f16
```

tz3:
```
Input: tz3RDC3Jdn4j15J7bBHZd29EUee9gVB1CxD9
Ouptut: 0002358cbffa97149631cfb999fa47f0035fb1ea8636
```

KT1:
```
Input: KT1HiGcq47Yek7dcre7So2yfWTLU83FTBaf1
Output: 0164244bbdc7790d7896b6a52ac49802cfe4eafc4b00
```

### Public Keys
```
Input: edpkuAfEJCEatRgFpRGg3gn3FdWniLXBoubARreRwuVZPWufkgDBvR
Output: 00451bde832454ba73e6e0de313fcf5d1565ec51080edc73bb19287b8e0ab2122b
```

### Branches

```
Input: BMNY6Jkas7BzKb7wDLCFoQ4YxfYoieU7Xmo1ED3Y9Lo3ZvVGdgW
Output: da8eb4f57f98a647588b47d29483d1edfdbec1428c11609cee0da6e0f27cfc38
```

### Full Transactions

These tests fully forge a given JSON object representation to their hex representation.

#### Reveal

```
Input:
{
  source: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW
  fee: 1272,
  counter: 30738,
  gas_limit: 10100,
  storage_limit: 257
  kind: reveal
  public_key: edpku9ZF6UUAEo1AL3NWy1oxHLL6AfQcGYwA5hFKrEKVHMT3Xx889A  
}

Output: 07000081faa75f741ef614b0e35fcc8c90dfa3b0b95721f80992f001f44e810200429a986c8072a40a1f3a3e2ab5a5819bb1b2fb69993c5004837815b9dc55923e
```

#### Transaction
```
Input:
{
  source: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW,
  fee: 1272,
  counter: 30738,
  gas_limit: 10100,
  storage_limit: 257,
  kind: transaction,
  amount: 1,
  destination: tz1Yju7jmmsaUiG9qQLoYv35v5pHgnWoLWbt
 }
 
 Output: 08000081faa75f741ef614b0e35fcc8c90dfa3b0b95721f80992f001f44e81020100008fb5cea62d147c696afd9a93dbce962f4c8a9c9100
```
 
### Operation Lists
End to end tests for forging an operation list.

#### Single Operation - Transaction
```
Inputs:
  Branch: BL8euoCWqNCny9AR3AKjnpi38haYMxjei1ZqNHuXMn19JSQnoWp
  Operations: [ {
    amount: 1
    destination: tz1Yju7jmmsaUiG9qQLoYv35v5pHgnWoLWbt
    source: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW
    fee: 1272
    counter: 30738
    gas_limit: 10100
    storage_limit: 257
    kind: transaction
  }]
  
Output: 3756ef37b1be849e3114643f0aa5847cabf9a896d3bfe4dd51448de68e91da0108000081faa75f741ef614b0e35fcc8c90dfa3b0b95721f80992f001f44e81020100008fb5cea62d147c696afd9a93dbce962f4c8a9c9100
```

#### Single Operation - Reveal

```
Inputs:
  Branch: BL8euoCWqNCny9AR3AKjnpi38haYMxjei1ZqNHuXMn19JSQnoWp
  Operations: [ {
    public_key: edpku9ZF6UUAEo1AL3NWy1oxHLL6AfQcGYwA5hFKrEKVHMT3Xx889A"
    source: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW
    fee: 1272
    counter: 30738
    gas_limit: 10100
    storage_limit: 257
    kind: reveal
  }]
  
Output: 3756ef37b1be849e3114643f0aa5847cabf9a896d3bfe4dd51448de68e91da0107000081faa75f741ef614b0e35fcc8c90dfa3b0b95721f80992f001f44e810200429a986c8072a40a1f3a3e2ab5a5819bb1b2fb69993c5004837815b9dc55923e
```

#### Single Operation - Origination Only

```
Inputs:
  Branch: BLoCyPwBk3XoS5jLbKNzvYPe4RwFbnDdMSLqvsbhMmxopcPfAhP
  Operations: [ {
    manager_pub_key: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW
    balance: 0
    source: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW
    fee: 1285
    counter: 30871
    gas_limit: 10000
    storage_limit: 257
    kind: origination
  }]
  
Output: 8ee0e04e6e66717f5b580bd494f1c00b73c171f5ebd85e0ef4c1dbc4def1f6f109000081faa75f741ef614b0e35fcc8c90dfa3b0b95721850a97f101904e81020081faa75f741ef614b0e35fcc8c90dfa3b0b9572100ffff0000
```

#### Single Operation - Set Delegate

```
Input: 
  Branch: BLa4GrVQTxUgQWbHv6cF7RXWSGzHGPbgecpQ795R3cLzw4cGfpD
  Operations: [ {
    delegate: tz1dYUCcrorfCoaQCtZaxi1ynGrP3prTZcxS
    source: KT1D5jmrBD7bDa3jCpgzo32FMYmRDdK2ihka
    fee: 1257
    counter: 68
    gas_limit: 10000
    storage_limit: 0
    kind: delegation
  }]

Output: 7105102c032807994dd9b5edf219261896a559876ca16cbf9d31dbe3612b89f20a01315b1206ec00b1b1e64cc3b8b93059f58fa2fc3900e90944904e00ff00c4650fd609f88c67356e5fe01e37cd3ff654b18c
```

#### Single Operation - Clear Delegate

```
Input:
  Branch: BLGJfQDFEYZBRLj5GSHskj8NPaRYhk7Kx5WAfdcDucD3q98WdeW
  Operations: [{
    delegate: ""   // Empty string
    source: KT1D5jmrBD7bDa3jCpgzo32FMYmRDdK2ihka"
    fee: 1257
    counter: 67
    gas_limit: 10000
    storage_limit: 0
    kind: delegation
  }]

Output: 48b63d801fa824013a195f7885ba522503c59e0580f7663e15c52f03ccc935e60a01315b1206ec00b1b1e64cc3b8b93059f58fa2fc3900e90943904e0000
```

#### Multiple Operations - Reveal and Transaction

```
Input: 
  Branch: BL8euoCWqNCny9AR3AKjnpi38haYMxjei1ZqNHuXMn19JSQnoWp
  Operations: [ {
    public_key: edpku9ZF6UUAEo1AL3NWy1oxHLL6AfQcGYwA5hFKrEKVHMT3Xx889A
    source: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW
    fee: 1272
    counter: 30738
    gas_limit: 10100
    storage_limit: 257
    kind: reveal
  },
  {
    amount: 1
    destination: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW
    source: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW
    fee: 1272
    counter: 30739
    gas_limit: 10100
    storage_limit: 257
    kind: transaction
  }]
  
Output: "3756ef37b1be849e3114643f0aa5847cabf9a896d3bfe4dd51448de68e91da0107000081faa75f741ef614b0e35fcc8c90dfa3b0b95721f80992f001f44e810200429a986c8072a40a1f3a3e2ab5a5819bb1b2fb69993c5004837815b9dc55923e08000081faa75f741ef614b0e35fcc8c90dfa3b0b95721f80993f001f44e810201000081faa75f741ef614b0e35fcc8c90dfa3b0b9572100
```

## Signing
Tests for signing data

### Simple Bytes
```
Inputs:
  Private Key: 2e8905819b8723fe2c1d161860e5ee1830318dbf49a83bd451cfb8440c28bd6f
  Bytes to Sign: ffaa
 
Output:
  Signature: eaab7f4066217b072b79609a9f76cdfadd93f8dde41763887e131c02324f18c8e41b1009e334baf87f9d2e917bf4c0e73165622e5522409a0c5817234a48cc02
```

### OperationList

Sign an operation list that has a reveal and transaction operation. 

```
Inputs: 
  Private Key: 2e8905819b8723fe2c1d161860e5ee1830318dbf49a83bd451cfb8440c28bd6f
  Operation List: {
    branch: BL8euoCWqNCny9AR3AKjnpi38haYMxjei1ZqNHuXMn19JSQnoWp
    operations: [  {
      public_key: edpku9ZF6UUAEo1AL3NWy1oxHLL6AfQcGYwA5hFKrEKVHMT3Xx889A
      source: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW
      fee: 1272
      counter: 30738
      gas_limit: 10100
      storage_limit: 257
      kind: reveal
     }, 
     {
      amount: 1
      destination: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW
      source: tz1XVJ8bZUXs7r5NV8dHvuiBhzECvLRLR3jW
      fee: 1272
      counter: 30739
      gas_limit: 10100
      storage_limit: 257
      kind: transaction
    }
  }]

Output Signature: d924cb3e56c4b9f55e50735e461899a2f616a26bfb0aa05d0b356b66f517b023df330ad3621f0bf39d518131a1becd6a7b2e226ed291483af3682535d1f4530f
```

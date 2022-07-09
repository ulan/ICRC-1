# ICRC-1 Token Standard

The ICRC-1 is a standard for Fungible Tokens on the [Internet Computer](https://internetcomputer.org).

## Data

### account

A `principal` can have multiple accounts. Each account of a `principal` is identified by a 32-byte string called `subaccount`. Therefore an account corresponds to a pair `(principal, subaccount)`.

The account identified by the subaccount with all bytes set to 0 is the _default account_ of the `principal`.

## Methods

### name

Returns the name of the token (e.g., `MyToken`).

```
name : () -> (text) query;
```

### symbol

Returns the symbol of the token (e.g., `ICP`).

```
symbol : () -> (text) query;
```

### decimals

Returns the number of decimals the token uses (e.g., `8` means to divide the token amount by `100000000` to get its user representation).

```
decimals : () -> (nat32) query;
```

### metadata

Returns the list of metadata entries for this ledger.
See the "Metadata" section below.

```
type Value = variant { Nat : nat; Int : int; Text : text; Blob : blob };

metadata : () -> (vec { record { text; Value } }) query;
```

### totalSupply

Returns the total token supply.

```
totalSupply: () -> (nat32) query;
```

### balanceOf

Returns the balance of the account given as argument.

```
balanceOf: (record { of: principal; subaccount: opt SubAccount; }) -> (nat64) query;
```

### transfer

Transfers `amount` of tokens from the account `(caller, from_subaccount)` to the account `(to_principal, to_subaccount)`.

```
type TransferArgs = record {
    from_subaccount: opt SubAccount;
    to_principal: Principal;
    to_subaccount: opt SubAccount;
    amount: nat64;
};

transfer: (TransferArgs) -> (variant { Ok: nat64; Err: TransferError; });
```

The result is either the block index of the transfer or an error. The list of errors is:

```
type TransferError = variant {
    // TODO
    GenericError: text,
};
```

## Metadata

A ledger can expose metadata to simplify integration with wallets and improve user experience.
The client can use the `metadata` method to fetch the metadata entries. 
All the metadata entries are optional.

### Key format

The metadata keys are arbitrary unicode strings and must follow the pattern `<namespace>:<key>`, where `<namespace>` is a string not containing colons.
Namespace `icrc1` is reserved for keys defined in this standard.

### Standard metadata entries

| Key | Example value | Semantics |
| --- | ------------- | --------- |
| `icrc1:symbol` | `variant { Text = "XTKN" }` | The token currency code (see [ISO-4217](https://en.wikipedia.org/wiki/ISO_4217)). When present, should be the same as the result of the `symbol` query call. |
| `icrc1:name` | `variant { Text = "Test Token" }` | The name of the token. When present, should be the same as the result of the `name` query call. |
| `icrc1:decimals` | `variant { Nat = 8 }` | The number of decimals the token uses. For example, 8 means to divide the token amount by 10<sup>8</sup> to get its user representation. When present, should be the same as the result of the `decimals` query call. |


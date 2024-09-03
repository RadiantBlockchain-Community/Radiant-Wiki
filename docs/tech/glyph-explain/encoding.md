# Encoding

A glyph's token data is encoded in the reveal unlocking script, identified by the string "gly" followed by a CBOR encoded token payload:

```
OP_PUSH "gly"
OP_PUSH <CBOR Payload>
```

## Payload structure

The Glyphs protocol defines standard top level fields shown below in CDDL:

```
payload = {
  p: [* (number / string)]       ; Array of protocol identifiers
  name: tstr,                    ; Name of the token
  ? type: tstr,                  ; Type of the token (e.g. container, user or other custom types)
  ? in: [*bstr],                 ; Array of container token refs the token is part of
  ? by: [*bstr],                 ; Array of author token refs the token is created by
  ? main: {                      ; Main file
    t: tstr,                     ; File mime type
    b: bstr                      ; File bytes
  },
  ? attrs: { * tstr => any }     ; Attributes as key value pairs (e.g. color, rarity, etc)
  ? loc: number / bytes / string ; Location for link tokens
}

```

Example CBOR payload:

```
{
    p: [1, 4],
    name: "My token",
    ticker: "XYZ",
    main: {
        t: "image/jpeg",
        b: <bytes>
    },
    in: [
        <containerRefBytes>
    ],
    by: [
        <authorRefBytes>
    ],
}
```

## Protocols

The `p` property contains an array of protocols used by the token. Current protocol identifiers are as follows:

| ID  | Protocol           |
| --- | ------------------ |
| 1   | Fungible token     |
| 2   | Non-fungible token |
| 3   | Data storage       |
| 4   | Decentralized mint |
| 5   | Mutable            |

Some protocols may depend on other protocols also being implemented. For example for a mineable token, a `p` value of `[1, 4]` must be used, indicating the token implements the FT and dmint contracts.

## File encoding

In addition to the `main` property other files can be added to the payload. These can be either embedded or remote.

Embed:

```
  embed = {
    t: tstr, ; File mime type
    b: bstr  ; File bytes
  }
```

Remote:

```
  remote = {
    t: tstr,   ; File mime type
    u: tstr    ; URL
    ? h: bstr  ; File hash
    ? hs: bstr ; HashStamp image (low resolution copy of image)
  }
```

Example:

```
{
    main: {
        t: "image/jpeg",
        b: <bytes> // File bytes embedded in transaction
    },
    alt: {
        t: "image/jpeg",
        u: "https://url/to/image.jpg", // Link to remote file
        h: <hashBytes>,
        hs: <hashStampBytes>
    }
}
```

If a link to a remote file is used, it is recommended to include a hash so the downloaded file can be verified.

### HashStamp

For remote images a HashStamp may be provided in the `hs` property. This is a low resolution, highly compressed copy of the image in WebP format. This may be desirable for people who want to store data off-chain but still have some representation of the file on-chain.  It is recommended to keep HashStamps within 64x64 pixels and quality 20 to keep size to a minimum.

HashStamps can be used by wallets to display a version of the image if downloading the file is deemed insecure, or used as a loading placeholder similar to a BlurHash.

## Types

The Glyphs protocol uses types `container` and `user`, for allowing the creation of collections and associating tokens to a creator. Wallets should implement these types and render the tokens accordingly.

Custom types are also permitted.

## Custom fields

Any custom fields may be added to the payload.

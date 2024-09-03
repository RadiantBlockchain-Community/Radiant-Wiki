# Links

Tokens may be linked to any resource, either tokens or off-chain resources. This has many uses such as creating a record of a token mint or even building a decentralized redirect service.

Links are created using the `loc` field which is used by wallets in a similar way to the HTTP location header.

To create a link the `loc` field is set to either a number, bytes or string:

```
location = number / bytes / string
```

- `number` - Link to a token with given output index and transaction ID matching the link ref
- `bytes` - Link to a token given as ref bytes
- `string` - Link to any URI

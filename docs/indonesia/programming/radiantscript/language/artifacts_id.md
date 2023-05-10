---
title: Artefak
---

Kontrak yang disusun dapat diwakili oleh apa yang disebut artefak. Artefak ini berisi semua informasi yang diperlukan untuk berinteraksi dengan smart contract on-chain. Artefak disimpan dalam file `.json` sehingga dapat dibagikan dan disimpan untuk penggunaan selanjutnya tanpa harus mengkompilasi ulang kontrak.

:::tip Tahukah Anda?
Artefak memungkinkan pengembangan SDK pihak ketiga mana pun, karena SDK ini hanya perlu mengimpor dan menggunakan file artefak, sedangkan kompilasi kontrak diserahkan kepada kompiler resmi `cashc`.
:::

## Artifact specification
```typescript
interface Artifact {
  contractName: string // Contract name
  constructorInputs: AbiInput[] // Arguments required to instantiate a contract
  abi: AbiFunction[] // functions that can be called
  bytecode: string // Compiled Script without constructor parameters added (in ASM format)
  source: string // Source code of the CashScript contract
  compiler: {
    name: string // Compiler used to compile this contract
    version: string // Compiler version used to compile this contract
  }
  updatedAt: string // Last datetime this artifact was updated (in ISO format)
}

interface AbiInput {
  name: string // Input name
  type: string // Input type (see language documentation)
}

interface AbiFunction {
  name: string // Function name
  inputs: AbiInput[] // Function inputs / parameters
}
```

# LuxFHE SDK

**JavaScript/TypeScript SDK for FHE on Lux Network**

[![npm](https://img.shields.io/npm/v/@luxfhe/sdk.svg)](https://www.npmjs.com/package/@luxfhe/sdk)
[![License](https://img.shields.io/badge/license-Lux%20Research-blue.svg)](LICENSE)

---

## ⚠️ Important IP Notice

**This SDK uses Lux's independent FHE implementation:**

- ✅ Client-side encryption using WASM bindings to `github.com/luxfi/tfhe`
- ✅ Communicates with Lux Network FHE precompiles
- ✅ NO dependency on Zama or any third-party FHE code
- ✅ Protected by Lux Industries patent portfolio

---

## Overview

The LuxFHE SDK enables JavaScript/TypeScript applications to:
- Encrypt data client-side for FHE smart contracts
- Decrypt results from confidential computations
- Interact with FHE-enabled smart contracts on Lux Network

## Installation

```bash
npm install @luxfhe/sdk
# or
pnpm add @luxfhe/sdk
# or
yarn add @luxfhe/sdk
```

## Quick Start

```typescript
import { LuxFHE, FheUint32 } from '@luxfhe/sdk';
import { ethers } from 'ethers';

// Initialize
const fhe = await LuxFHE.create({
  network: 'mainnet', // or 'testnet'
  provider: window.ethereum,
});

// Encrypt a value
const encrypted = await fhe.encrypt.uint32(42);

// Send to contract
const contract = new ethers.Contract(address, abi, signer);
await contract.setSecretValue(encrypted.data, encrypted.proof);

// Decrypt a result (requires permit)
const permit = await fhe.generatePermit(contract.address);
const decrypted = await fhe.decrypt.uint32(encryptedResult, permit);
console.log('Decrypted value:', decrypted); // 42
```

## Encrypted Types

```typescript
import { 
  FheBool, 
  FheUint8, 
  FheUint32, 
  FheUint64,
  FheUint128,
  FheUint160,  // Ethereum addresses
  FheUint256,  // EVM word size
  FheAddress
} from '@luxfhe/sdk';
```

## Encryption

```typescript
// Encrypt different types
const encBool = await fhe.encrypt.bool(true);
const encU8 = await fhe.encrypt.uint8(255);
const encU32 = await fhe.encrypt.uint32(1000000);
const encU64 = await fhe.encrypt.uint64(BigInt('1000000000000'));
const encAddr = await fhe.encrypt.address('0x...');

// With input proof for contracts
const input = await fhe.encrypt.uint32(42);
await contract.processSecret(input.data, input.proof);
```

## Decryption (with Permits)

```typescript
// Generate permit (user signs message)
const permit = await fhe.generatePermit(contractAddress);

// Decrypt values from contract
const value = await fhe.decrypt.uint32(encryptedHandle, permit);
```

## React Integration

```tsx
import { LuxFHEProvider, useFHE } from '@luxfhe/sdk/react';

function App() {
  return (
    <LuxFHEProvider network="mainnet">
      <MyComponent />
    </LuxFHEProvider>
  );
}

function MyComponent() {
  const { fhe, isLoading, encrypt, decrypt } = useFHE();
  
  const handleEncrypt = async () => {
    const encrypted = await encrypt.uint32(secretValue);
    // Use encrypted data...
  };
  
  return <button onClick={handleEncrypt}>Encrypt</button>;
}
```

## Supported Networks

| Network | Chain ID | RPC |
|---------|----------|-----|
| Lux Mainnet | 7777 | https://api.lux.network |
| Lux Testnet | 8888 | https://api.testnet.lux.network |
| Local | 9999 | http://localhost:9650 |

## Architecture

```
@luxfhe/sdk (this package)
    ├── WASM Module (compiled from github.com/luxfi/tfhe)
    ├── ethers.js / viem integration
    └── React hooks

        ↓ RPC calls ↓

Lux Network
    ├── FHE Precompiles (lux/evm/precompile/contracts/fhe/)
    └── Go TFHE Library (github.com/luxfi/tfhe)
```

## API Reference

### LuxFHE.create(options)

```typescript
interface LuxFHEOptions {
  network: 'mainnet' | 'testnet' | 'local';
  provider?: EIP1193Provider;
  rpcUrl?: string;
}
```

### fhe.encrypt

| Method | Input | Output |
|--------|-------|--------|
| `encrypt.bool(value)` | `boolean` | `EncryptedInput` |
| `encrypt.uint8(value)` | `number` | `EncryptedInput` |
| `encrypt.uint32(value)` | `number` | `EncryptedInput` |
| `encrypt.uint64(value)` | `bigint` | `EncryptedInput` |
| `encrypt.address(value)` | `string` | `EncryptedInput` |

### fhe.decrypt

| Method | Input | Output |
|--------|-------|--------|
| `decrypt.bool(handle, permit)` | `bytes32` | `boolean` |
| `decrypt.uint32(handle, permit)` | `bytes32` | `number` |
| `decrypt.uint64(handle, permit)` | `bytes32` | `bigint` |

## License

**Lux Research License with Patent Reservation**

- ✅ Free for research and academic use
- ✅ Free on Lux Network (mainnet/testnet)
- 📧 Commercial license required for other networks

Contact: oss@lux.network

---

© 2020-2025 Lux Industries Inc. All rights reserved.

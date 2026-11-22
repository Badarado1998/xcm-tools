2. Code Patch / Fix

**File:** `packages/sdk-core/src/transfer/utils/validationUtils.ts`

```typescript
// ... existing code ...

export function validateTransact(options: TransactOptions, api: ApiPromise) {
  const { call, sender } = options
  const address = options.destination

  if (!call) {
    throw new ValidationError('Call is required for transact')
  }

  if (!isHex(call)) {
    throw new ValidationError('Call must be a hex string')
  }

  // FIX: Changed 'return' to 'throw' to properly reject invalid sender/destination mismatches
  if (typeof address === 'string' && sender && !compareAddresses(api, address, sender)) {
    throw new ValidationError(
      'Sender address must match the destination address for transact to work.'
    )
  }
}

// ... existing code ...
```
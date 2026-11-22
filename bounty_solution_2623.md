2. Code Patch / Fix

**File:** `packages/sdk-core/src/transfer/transfer-info/buildOriginInfo.ts`

```typescript
// ... existing imports and context

export function buildOriginInfo(
  // ... params
): OriginInfo {
  // ... existing logic

  const originBalanceFee = originBalance - originFee
  const originBalanceFeeAfter = originBalanceFee - (transferredFeeAsset ? amount : 0n)
  
  // FIX: Remove the incorrect addition of 'ed' to 'remaining'.
  // The sufficiency check should be performed directly on the balance after the fee deduction.
  const isSufficient = transferredFeeAsset
    ? originBalanceFeeAfter === 0n || originBalanceFeeAfter >= ed
    : originBalanceFee >= originFee

  return {
    // ... other fields
    xcmFee: {
      // ... other fields
      sufficient: isSufficient,
      // ...
    },
    // ...
  }
}
```

*Note: The exact variable names might vary slightly depending on the current state of the file, but the core change is replacing the `remaining` calculation and check with a direct check on `originBalanceFeeAfter` (or equivalent variable representing balance after fee).*

**Refined Patch (assuming standard variable names in the repo):**

```typescript
// In buildOriginInfo.ts

// Replace:
// const remaining = originBalanceFeeAfter + (isNativeFeeAsset ? ed : 0n)
// sufficient: transferredFeeAsset
//   ? remaining === 0n || remaining >= ed
//   : originBalanceFee >= originFee

// With:
sufficient: transferredFeeAsset
  ? originBalanceFeeAfter === 0n || originBalanceFeeAfter >= ed
  : originBalanceFee >= originFee
```
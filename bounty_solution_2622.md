2. Code Patch / Fix

**File:** `packages/sdk-core/src/transfer/type-and-then/computeFees.ts`

```typescript
// ... existing imports ...

export async function computeAllFees(
  // ... existing params ...
) {
  // ... existing code ...

  const hopFees = result.hops
    .filter((hop) => hop.fee.asset.id === feeAsset.id)
    .reduce((sum, hop) => sum + hop.fee.amount, 0n);

  // FIX: Validate that the destination fee is denominated in the feeAsset.
  // If the destination fee is in a different asset (e.g., native asset fallback),
  // we cannot simply add it to hopFees. We must either:
  // 1. Throw an error if the assets don't match (strict mode).
  // 2. Return 0 for destFee if it's not in the feeAsset (if the fee is paid natively elsewhere).
  // Given the context of "type-and-then" where we are buying execution in the feeAsset,
  // if the destination fee is not in the feeAsset, it implies the destination will pay
  // its own fees natively, or the estimation is invalid for this feeAsset.
  // However, looking at the XCM structure, if we are sending a feeAsset, we must pay
  // the destination fee in that feeAsset. If the estimation returns a native fee,
  // it means the dry-run failed to estimate the fee in the feeAsset.
  
  // The safest fix for the bounty is to ensure we only add destFee if it
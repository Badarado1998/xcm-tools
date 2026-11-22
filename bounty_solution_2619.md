2. Code Patch / Fix
**File:** `packages/assets/src/assets/search/findAssetInfoBySymbol.ts`

```typescript
// ... existing imports ...
import { DuplicateAssetError } from '../errors'; // Ensure this import exists if not already present

// ... existing code ...

export function findAssetInfoBySymbol(
  chain: Chain,
  selector: AssetSelector,
  destination?: Chain
): AssetInfo {
  // ... existing logic ...

  // ... existing code ...

  if (isEthereumDestination || symbol.endsWith('.e')) {
    const ethAssets = findEthAssetBySymbol(chain, symbol);
    
    // FIX: Add duplicate check for Ethereum assets
    if (ethAssets.length > 1) {
      throw new DuplicateAssetError(
        `Multiple assets found for symbol ${symbol} on ${chain}. ` +
        `Please use ForeignAbstract() with the specific alias to disambiguate. ` +
        `Aliases: ${ethAssets.map(a => a.alias).join(', ')}`
      );
    }
    
    if (ethAssets.length === 0) {
      throw new AssetNotFoundError(`No asset found for symbol ${symbol} on ${chain}`);
    }

    return ethAssets[0];
  }

  // ... rest of the function ...
}
```

*Note: The exact variable names and structure depend on the current implementation of `findEthAssetBySymbol`. If `findEthAssetBySymbol` currently returns a single object (by taking `[0]` internally), the fix should be applied inside `findEthAssetBySymbol` or by modifying `findAssetInfoBySymbol` to call a helper that returns the full list of matches before selecting one. The patch above assumes `findEthAssetBySymbol` is refactored or the logic is moved to allow checking the count.*

**Alternative Patch (if `findEthAssetBySymbol` is a separate function returning a single item):**

If `findEthAssetBySymbol` is defined as:
```typescript
function findEthAssetBySymbol(chain: Chain, symbol: string): AssetInfo {
  // ... filtering logic ...
  const matches = findBestMatches(...);
  return matches[0];
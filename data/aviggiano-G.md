# Loop only once through `_amount` on `NFTSale.buy`

```diff
diff --git a/contracts/NFTSale.sol b/contracts/NFTSale.sol
index f5d0f84..db48b23 100644
--- a/contracts/NFTSale.sol
+++ b/contracts/NFTSale.sol
@@ -44,8 +44,6 @@ contract NFTSale is Ownable {
         uint[] memory _sold = new uint[](_amount);
         for (uint i=0; i<_amount; i++) {
             _sold[i] = availableIds[(availableIds.length-i) - 1];
-        }
-        for (uint i=0; i<_amount; i++) {
             availableIds.pop();
         }
         nft.safeTransferMany(msg.sender, _sold);

```
# hw1

## Task 1

```diff
diff --git a/MultiSigWallet.sol b/MultiSigWallet.sol
index 5a3717d..e29292b 100644
--- a/MultiSigWallet.sol
+++ b/MultiSigWallet.sol
@@ -22,6 +22,7 @@ contract MultiSigWallet {
      *  Constants
      */
     uint constant public MAX_OWNER_COUNT = 50;
+    uint constant public MAX_ETHER_PER_TX = 66 ether;
 
     /*
      *  Storage
@@ -91,6 +92,11 @@ contract MultiSigWallet {
         _;
     }
 
+    modifier validTransaction(uint value) {
+        require(value <= MAX_ETHER_PER_TX);
+        _;
+    }
+
     /// @dev Fallback function allows to deposit ether.
     function()
         payable
@@ -289,6 +295,7 @@ contract MultiSigWallet {
```

## Task 2

```diff
diff --git a/ERC20.sol b/ERC20.sol
index c6718e6..96e7f28 100644
--- a/ERC20.sol
+++ b/ERC20.sol
@@ -39,6 +39,12 @@ contract ERC20 is Context, IERC20 {
     string private _name;
     string private _symbol;
 
+    modifier validateDate {
+        uint day = (block.timestamp / 1 days + 3) % 7 + 1;
+        require(day != 6, "ERC20: You can't transfer on Saturday");
+        _;
+    }
+
     /**
      * @dev Sets the values for {name} and {symbol}.
      *
@@ -205,7 +211,7 @@ contract ERC20 is Context, IERC20 {
      * - `recipient` cannot be the zero address.
      * - `sender` must have a balance of at least `amount`.
      */
-    function _transfer(address sender, address recipient, uint256 amount) internal virtual {
+    function _transfer(address sender, address recipient, uint256 amount) internal validateDate virtual {
         require(sender != address(0), "ERC20: transfer from the zero address");
         require(recipient != address(0), "ERC20: transfer to the zero address");
```

## Task 3

```diff
diff --git a/DividentToken.sol b/DividentToken.sol
index ad8578c..d3a0169 100644
--- a/DividentToken.sol
+++ b/DividentToken.sol
@@ -18,7 +18,7 @@ contract DividendToken is StandardToken, Ownable {
     event PayDividend(address indexed to, uint256 amount);
     event HangingDividend(address indexed to, uint256 amount) ;
     event PayHangingDividend(uint256 amount) ;
-    event Deposit(address indexed sender, uint256 value);
+    event Deposit(address indexed sender, uint256 value, bytes32 comment);
 
     /// @dev parameters of an extra token emission
     struct EmissionInfo {
@@ -37,9 +37,9 @@ contract DividendToken is StandardToken, Ownable {
         }));
     }
 
-    function() external payable {
+    function deposit(bytes32 message) external payable {
         if (msg.value > 0) {
-            emit Deposit(msg.sender, msg.value);
+            emit Deposit(msg.sender, msg.value, message);
             m_totalDividends = m_totalDividends.add(msg.value);
         }
     }
```
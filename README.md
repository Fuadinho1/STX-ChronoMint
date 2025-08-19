#  STX-ChronoMint

A **programmable vesting NFT contract** for the Stacks blockchain that integrates **STX payments**, **vesting logic**, **staking info**, and **utility-based level upgrades**.

This smart contract enables minting NFTs that unlock value over time, enforcing vesting periods while allowing upgrades, staking, and rewards. It includes **enhanced safety checks**, **fund withdrawal controls**, and **metadata support**.

---

## 🚀 Features

* **NFT Minting with Vesting**

  * Users can mint NFTs by paying STX.
  * Each NFT is tied to a vesting period (measured in blocks).
  * Mint price is configurable by the contract owner.

* **Token Levels**

  * NFT levels increase as vesting periods pass.
  * Users can pay STX to update their NFT level when eligible.
  * Levels unlock utilities defined by the contract owner.

* **Programmable Utilities**

  * Contract owner can assign **utility strings** to specific token levels.
  * Enables dynamic features tied to NFT progression.

* **Staking Integration (data model only, future expansion)**

  * NFTs can store staking-related data (`staked-at`, `last-reward-claim`).
  * Provides the foundation for yield or staking reward mechanisms.

* **Metadata Support**

  * Each token can store metadata:

    * `name`
    * `description`
    * `image-uri`
  * Useful for wallets, explorers, or NFT marketplaces.

* **STX Payments & Withdrawal**

  * Minting and upgrading NFTs require STX payments.
  * Contract owner can withdraw accumulated STX safely.
  * All transfers include **refund safety checks**.

* **Security Checks**

  * Ownership validation before level updates.
  * Non-zero parameter checks for prices, amounts, and vesting.
  * Overflow and invalid state prevention.
  * Refunds if NFT minting fails.

---

## 📂 Contract Structure

### **Constants**

* `contract-owner` → Deployer of the contract.
* Error codes (`err-owner-only`, `err-invalid-token`, etc.).
* Default economic parameters:

  * `mint-price` = **200 STX**
  * `level-up-price` = **50 STX**
  * `reward-rate` = **0.01 STX per block**

### **Data Variables**

* `token-id-nonce` → Tracks incremental NFT IDs.
* `mint-price`, `level-up-price`, `reward-rate`.

### **Data Maps**

* `tokens` → NFT ownership, creation block, vesting period, level.
* `token-levels` → Maps token levels to utilities.
* `token-metadata` → Metadata for each NFT.
* `staking-info` → Tracks staking timestamps & claims.

---

## ⚙️ Public Functions

### **Minting**

```clarity
(mint (vesting-period uint))
```

* Mints a new NFT for `tx-sender`.
* Requires payment of `mint-price` in STX.
* Assigns `vesting-period` in blocks.

### **Level Management**

```clarity
(update-token-level (token-id uint))
```

* Updates NFT level if vesting period conditions are met.
* Requires STX payment (`level-up-price`) for each upgrade.

### **Utility Assignment**

```clarity
(set-level-utility (token-id uint) (level uint) (utility (string-ascii 256)))
```

* Contract owner sets a utility string for a given token level.

### **Price Controls (Owner only)**

```clarity
(set-mint-price (new-price uint))
(set-level-up-price (new-price uint))
```

### **STX Withdrawal (Owner only)**

```clarity
(withdraw-stx (amount uint))
```

* Withdraws STX from the contract balance.

---

## 🔒 Error Codes

| Code   | Meaning                  |
| ------ | ------------------------ |
| `u100` | Owner-only action        |
| `u101` | Not token owner          |
| `u102` | Invalid token            |
| `u103` | Insufficient STX balance |
| `u104` | Invalid parameters       |
| `u105` | Zero amount not allowed  |
| `u106` | Unauthorized action      |
| `u107` | Already staked           |
| `u108` | Not staked               |
| `u109` | Staking error            |
| `u110` | Fusion failed            |
| `u111` | Same token not allowed   |

---

## 📊 Example Workflow

1. **Mint an NFT**

   ```clarity
   (contract-call? .stx-vesting-nft mint u500) ;; vesting over 500 blocks
   ```

2. **Update NFT Level**

   ```clarity
   (contract-call? .stx-vesting-nft update-token-level u1) ;; upgrade token #1
   ```

3. **Assign Utility (Owner only)**

   ```clarity
   (contract-call? .stx-vesting-nft set-level-utility u1 u2 "Access premium dashboard")
   ```

4. **Withdraw Earnings (Owner only)**

   ```clarity
   (contract-call? .stx-vesting-nft withdraw-stx u100000000) ;; withdraw 100 STX
   ```

---

## 🔮 Future Enhancements

* **Full staking implementation** (rewards accrual, claiming, penalties).
* **Fusion mechanics** (merge NFTs into stronger tokens).
* **NFT transfer restrictions** during vesting.
* **Dynamic reward-rate adjustments** by level.
* **Integration with NFT marketplaces** via metadata.

---

## 🛡️ Security Considerations

* Refund mechanism ensures users don’t lose STX if minting fails.
* Vesting periods prevent premature upgrades.
* All prices validated to prevent zero-cost abuse.
* Owner-only restrictions safeguard economic parameters.

---

## 📜 License

MIT License. Free to use, modify, and distribute.


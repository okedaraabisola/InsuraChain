
# InsuraChain -  Insurance Protocol — README

## Overview

This project implements a **decentralized insurance protocol** written in **Clarity** for the Stacks blockchain. It enables insurers to create policies, accept premiums, process claims, issue payouts, assess risk, and manage cancellations — all on-chain with full transparency.

The contract uses maps, public functions, and event logging to manage the full lifecycle of an insurance product.

---

## ✨ Features

### **1. Policy Creation**

* Insurers can initialize a policy for an insured party.
* Validates:

  * Principals are not the transaction sender.
  * Premium and coverage amounts are positive.
  * Coverage does not exceed the max allowed.
  * Only one active policy per insured party.

### **2. Premium Submission & Policy Renewal**

* Insured parties can submit premiums to activate or renew policies.
* Automatically updates:

  * Policy status (`policy-active`)
  * Expiration block (≈ 1 year)
* Transfers premium from insured → insurer.

### **3. Claim Submission**

* Insured users may submit claims if:

  * Their policy is active.
  * Claim amount does not exceed remaining coverage.
* Stores claim request data awaiting approval.

### **4. Claim Approval**

* Claims can be approved (e.g., by insurer).
* Marks claim as approved for later payout.

### **5. Payout Release**

* Insurer releases payout after claim approval.
* Ensures total claims do not exceed policy coverage.
* Updates total claims and transfers payout from insurer → insured.

### **6. Policy Cancellation**

* May be cancelled by either:

  * The insured, or
  * The insurer.
* Calculates proportional premium refund based on remaining policy time.
* Refunds insured directly from the insurer.

### **7. Risk Assessment & Premium Adjustment**

* Insurers can update a user’s risk score.
* Risk score must be within 1–100.
* Premium automatically recalculates using:

  ```
  adjusted-premium = current-premium * (risk-score / 50)
  ```

---

## 📚 Data Structures

### **insurance-policies**

Stores policy details per insured party:

```
insurer
policy-premium
policy-coverage
total-claims
policy-expiration
policy-active
```

### **insurance-claims**

Stores last submitted claim:

```
claim-requested
claim-approved
```

### **policy-history**

Tracks premiums and claim history (optional future enhancements).

### **risk-scores**

Tracks assigned risk score and timestamp.

---

## 🔧 Key Constants

* `grace-period = u1000`
* `max-coverage = u1000000`
* 1-year duration: `u52595` blocks (≈ 365 days)

---

## 📡 Public Functions Summary

| Function                 | Description                               |
| ------------------------ | ----------------------------------------- |
| `initiate-policy`        | Create a new insurance policy             |
| `submit-premium`         | Pay premium & renew/activate policy       |
| `submit-claim`           | File a new claim                          |
| `approve-claim`          | Approve a submitted claim                 |
| `release-payout`         | Transfer funds for an approved claim      |
| `cancel-policy`          | Cancel an active policy (refund included) |
| `update-risk-assessment` | Update risk score & auto-adjust premium   |

---

## 🧪 Suggested Testing Scenarios

1. Create a policy and attempt to create another — should fail.
2. Submit premium and confirm policy becomes active.
3. Submit a claim within coverage limits.
4. Approve the claim and release the payout.
5. Cancel policy midway and verify refund logic.
6. Update risk score and check premium adjustments.

---

## 🔐 Security Notes

* Uses `asserts!` and `unwrap!` for defensive programming.
* Minimizes re-entrancy via linear state transitions.
* Ensures only valid principals and authorized roles can call sensitive functions.

---

## 📄 License

MIT or your preferred open-source license.

---


# Chapter 10 — Capstone: E-Commerce Checkout Testing

> **Software Testing Foundation · Module: Test Design & Strategy**
> **Chapter 10 of 10** · ⏱️ **Time: 35 minutes** (hands-on case study)
> **Previous:** [← Chapter 9 — Test Estimation](09-test-estimation.md) · **Back to start:** [Chapter 1 — Testing Types](01-testing-types.md)

---

## Purpose

This capstone uses **one realistic feature** and takes you through the whole module. You'll do the same work a junior QA engineer does when a new story arrives in the sprint.

| Deliverable | Uses chapter | Suggested time |
|---|---|---|
| 1. Testing Types | [Ch. 1](01-testing-types.md) | 3 min |
| 2. Test Strategy | [Ch. 2](02-test-strategies.md) | 5 min |
| 3. Test Scenarios | [Ch. 3](03-use-cases.md) – [Ch. 6](06-test-scenarios.md) | 6 min |
| 4. Test Cases | [Ch. 7](07-test-case-design.md) | 8 min |
| 5. Defects | [Ch. 8](08-defect-life-cycle.md) | 7 min |
| 6. Estimation | [Ch. 9](09-test-estimation.md) | 6 min |

> **How to use this chapter:** try each deliverable **yourself first** (on paper, or in a spreadsheet using the template provided), *then* open the model answer and compare. Model answers are one good solution, not the only one. The time is tight on purpose. If you only partly finish a deliverable, skim its model answer before moving on.

---

## The Case: ShopEasy Checkout

**ShopEasy** is an online store (the same application you've seen throughout this course). The team is building a new credit-card checkout this sprint.

### User Story

> **US-301:** As a customer, I want to purchase products using my credit card so that I can complete my order online.

### Acceptance Criteria

| ID | Acceptance criterion |
|---|---|
| AC1 | Customer must have at least one item in the cart. |
| AC2 | Customer must provide valid shipping information. |
| AC3 | Customer must provide valid payment information. |
| AC4 | Payment must be authorized. |
| AC5 | Order should be created after successful payment. |
| AC6 | Customer should receive an order confirmation. |
| AC7 | Failed payment should not create an order. |

### Additional Context (from refinement)

- **Shipping info:** full name, address line, city, state, 6-digit PIN code, 10-digit mobile number. All mandatory.
- **Payment info:** 16-digit card number (Visa/Mastercard), expiry MM/YY (must not be in the past), 3-digit CVV, name on card.
- **Payment gateway:** third-party (sandbox available in QA). It responds with *Authorized*, *Declined*, *Insufficient funds*, or times out after 30 seconds.
- **Order confirmation:** on-screen confirmation with order number, plus an email sent via an external email service.
- **Platforms:** Chrome, Firefox, Safari (desktop); Chrome on Android; Safari on iOS.
- **Order states:** Created → Paid → Shipped → Delivered (see the state diagram in Ch. 7).

### Sandbox Test Cards (QA environment)

| Card number | Gateway behaviour |
|---|---|
| `4111 1111 1111 1111` | Authorized |
| `4000 0000 0000 0002` | Declined |
| `4000 0000 0000 9995` | Insufficient funds |
| `4000 0000 0000 0119` | Gateway timeout (no response for 35 s) |
| `1234 5678 9012 3456` | Invalid number (fails Luhn check) |

---

## Deliverable 1 — Testing Types

**Task:** for the checkout feature, explain **why and how** each of these testing types applies: Functional, Integration, System, Security, Performance, Regression. Add any other types you think apply.

<details>
<summary>✅ Model answer</summary>

| Type | Why it applies to checkout | What specifically to test |
|---|---|---|
| **Functional** | Verifies each AC behaves as specified | Cart validation, shipping validation, card validation, order creation, confirmation |
| **Integration** | Checkout talks to the **payment gateway**, **inventory**, **order service**, and **email service** | Correct amount/currency sent to the gateway; handling of Declined/Timeout responses; stock reduced after order; email triggered with correct data |
| **System** | The full journey must work end to end in a production-like environment | Login → add to cart → checkout → pay → order in My Orders → email received |
| **Security** | Card data and money are involved | Card number masked (`**** 1111`), CVV never stored or displayed, HTTPS only, no order manipulation via URL/API (changing price), session timeout |
| **Performance** | Checkout must work at sale peaks | Payment response under load (e.g., 500 concurrent checkouts), no duplicate orders under load |
| **Regression** | Checkout is a critical revenue flow touched by many changes | Automated checkout regression on every build; full regression before release |
| *Also:* **Compatibility** | Customers use many devices | 5 listed platforms, especially Safari iOS |
| *Also:* **Usability** | Abandoned checkouts lose revenue | Clear error messages, minimal steps, mobile-friendly forms |
| *Also:* **Smoke** | Every new build | Can a valid card purchase complete? |

**Remember the key idea from Ch. 1:** *"System + Functional + Regression"* testing of checkout is one activity with several labels.

</details>

---

## Deliverable 2 — Test Strategy

**Task:** (a) identify the main **risks**, (b) define **testing priorities**, (c) identify **automation candidates**, and (d) define **entry and exit criteria**.

<details>
<summary>✅ Model answer</summary>

**(a) Risks** (Likelihood × Impact)

| # | Risk | L | I | Level |
|---|---|---|---|---|
| R1 | Customer charged but no order created (or order created without payment) | M | H | **Critical** |
| R2 | Duplicate charge / duplicate order (double-click, retry, refresh) | M | H | **Critical** |
| R3 | Card data exposed or stored insecurely | L–M | H | **High** |
| R4 | Gateway timeout handled badly (stuck, unclear state) | M | H | **High** |
| R5 | Invalid shipping data accepted → undeliverable orders | M | M | Medium |
| R6 | Confirmation email not sent | M | M | Medium |
| R7 | Layout issues on mobile Safari block checkout | M | M | Medium |
| R8 | Slow checkout during sales | L–M | M | Medium |

**(b) Priorities**
- **P1:** successful payment E2E, failed payment → no order (AC7), duplicate-payment prevention, gateway timeout, card-data security.
- **P2:** shipping and card field validations (EP/BVA), confirmation email, out-of-stock during checkout, cross-platform checks.
- **P3:** cosmetic and usability details, rare edge cases.

**Approach:** requirement-based coverage of all 7 AC (RTM) + risk-based depth on R1–R4 + a **60-minute exploratory session** with the charter *"Explore payment interruptions (refresh, back, double-click, network loss) to discover duplicate-charge and orphan-order issues."*

**(c) Automation candidates**
- ✅ Successful checkout happy path (smoke + regression, every build)
- ✅ Declined / insufficient-funds / invalid-card flows (data-driven with sandbox cards)
- ✅ Shipping-field validations (data-driven BVA: PIN code 5/6/7 digits, mobile 9/10/11 digits)
- ✅ API-level tests: order not created when the gateway returns Declined
- ✅ Load test: concurrent checkouts
- ❌ Not automated (for now): exploratory interruption testing, usability, visual checks on Safari iOS, email content review

**(d) Entry / exit criteria**
- **Entry:** US-301 AC agreed; build passes smoke; QA environment and payment sandbox available; test cards and test users ready; test cases reviewed.
- **Exit:** 100% of P1 and ≥ 95% of all test cases executed; 0 open Critical/High defects; all 7 AC traced to passing tests; Medium/Low defects triaged (fixed or deferred with PO sign-off); regression passed; test summary report shared.

</details>

---

## Deliverable 3 — Test Scenarios

**Task:** list test scenarios for the checkout. Include at least: successful payment, failed payment, invalid card, expired card, insufficient funds, network interruption, and duplicate payment attempt. Then add your own (aim for 15+). Classify each and map it to an AC.

> 💡 *Tip:* mentally write the use case first (Ch. 3): main flow = add item → checkout → shipping → payment → authorize → order → confirmation. Then find alternate and exception flows at each step.

<details>
<summary>✅ Model answer</summary>

| ID | Scenario | Type | AC |
|---|---|---|---|
| TS_CHK_01 | Verify successful payment with a valid card creates an order and shows confirmation | Positive / E2E | AC3–AC6 |
| TS_CHK_02 | Verify checkout is blocked when the cart is empty | Negative | AC1 |
| TS_CHK_03 | Verify checkout with exactly one item succeeds | Boundary | AC1 |
| TS_CHK_04 | Verify an item that goes out of stock during checkout is handled | Exception | AC1 |
| TS_CHK_05 | Verify missing mandatory shipping fields are rejected | Negative | AC2 |
| TS_CHK_06 | Verify PIN code accepts exactly 6 digits only | Boundary | AC2 |
| TS_CHK_07 | Verify mobile number accepts exactly 10 digits only | Boundary | AC2 |
| TS_CHK_08 | Verify invalid card number is rejected | Negative | AC3 |
| TS_CHK_09 | Verify expired card is rejected | Negative / Boundary | AC3 |
| TS_CHK_10 | Verify card expiring in the current month is accepted | Boundary | AC3 |
| TS_CHK_11 | Verify invalid CVV (2 or 4 digits, letters) is rejected | Negative | AC3 |
| TS_CHK_12 | Verify declined payment does not create an order | Negative | AC4, AC7 |
| TS_CHK_13 | Verify insufficient-funds response does not create an order | Negative | AC4, AC7 |
| TS_CHK_14 | Verify network interruption / gateway timeout leaves no order and no charge, with a clear message | Exception | AC4, AC7 |
| TS_CHK_15 | Verify double-clicking "Place Order" creates only one order and one charge | Exception / Error guessing | AC5 |
| TS_CHK_16 | Verify browser Back/Refresh after success does not re-submit payment | Exception | AC5 |
| TS_CHK_17 | Verify order details (items, qty, prices, address, total) are correct | Positive | AC5 |
| TS_CHK_18 | Verify confirmation page shows order number and summary | Positive | AC6 |
| TS_CHK_19 | Verify confirmation email is received with correct details | Positive / Integration | AC6 |
| TS_CHK_20 | Verify card number is masked and CVV is never stored/displayed | Security | AC3 |
| TS_CHK_21 | Verify session timeout during payment is handled safely | Exception / Security | AC4, AC7 |
| TS_CHK_22 | Verify the cart is retained after a failed payment so the customer can retry | Alternate | AC7 |
| TS_CHK_23 | Verify retry with a valid card after a decline succeeds (one order only) | Alternate | AC4, AC5 |
| TS_CHK_24 | Verify checkout works on all 5 supported platforms | Compatibility | All |
| TS_CHK_25 | Verify order status transitions Created → Paid after authorization | State transition | AC4, AC5 |

</details>

---

## Deliverable 4 — Test Cases

**Task:** convert **two** scenarios into detailed, executable test cases (suggested: TS_CHK_12 → TC_CHK_005 and TS_CHK_15 → TC_CHK_010). Use this template:

| Field | Value |
|---|---|
| Test Case ID | TC_CHK_0XX |
| Title | |
| Requirement / Scenario | |
| Priority | |
| Preconditions | |
| Test Data | |
| Steps | |
| Expected Result | |
| Actual Result | |
| Status | Not Executed |

Also apply at least **one test design technique** (EP/BVA, decision table, or state transition) to part of the checkout.

<details>
<summary>✅ Model answer: test case index</summary>

The team's full test case list (IDs used in Deliverable 5):

| TC ID | Title | Scenario |
|---|---|---|
| TC_CHK_001 | Successful purchase with valid Visa card | TS_CHK_01 |
| TC_CHK_002 | Checkout blocked with empty cart | TS_CHK_02 |
| TC_CHK_003 | Mandatory shipping field missing | TS_CHK_05 |
| TC_CHK_004 | Invalid card number (Luhn fail) | TS_CHK_08 |
| TC_CHK_005 | Declined card, no order created | TS_CHK_12 |
| TC_CHK_006 | Insufficient funds, no order created | TS_CHK_13 |
| TC_CHK_007 | Expired card rejected | TS_CHK_09 |
| TC_CHK_008 | Invalid CVV rejected | TS_CHK_11 |
| TC_CHK_009 | Gateway timeout, no order, clear message | TS_CHK_14 |
| TC_CHK_010 | Double-click Place Order creates one order | TS_CHK_15 |
| TC_CHK_011 | Back button after success doesn't re-charge | TS_CHK_16 |
| TC_CHK_012 | PIN code boundary: 5 digits rejected | TS_CHK_06 |
| TC_CHK_013 | Item out of stock during checkout | TS_CHK_04 |
| TC_CHK_014 | Order details correct in My Orders | TS_CHK_17 |
| TC_CHK_015 | Confirmation email received | TS_CHK_19 |
| TC_CHK_016 | Card number masked, CVV not stored | TS_CHK_20 |
| TC_CHK_017 | Session timeout during payment | TS_CHK_21 |
| TC_CHK_018 | Checkout on Safari iOS | TS_CHK_24 |

</details>

<details>
<summary>✅ Model answer: detailed test cases</summary>

**TC_CHK_001: Successful purchase with valid Visa card**

| Field | Value |
|---|---|
| Requirement / Scenario | US-301 AC3, AC4, AC5, AC6 / TS_CHK_01 |
| Priority | P1 |
| Preconditions | User `asha@example.com` logged in; cart contains 1 × "Wireless Mouse" (₹1,200) and 1 × "USB Cable" (₹250); stock ≥ 5 for both |
| Test Data | Shipping: Asha Rao, 12 MG Road, Bengaluru, Karnataka, PIN `560001`, Mobile `9876543210` · Card: `4111 1111 1111 1111`, Exp `12/28`, CVV `123`, Name `ASHA RAO` |
| Steps | 1. Open Cart, click **Checkout** 2. Enter the shipping data, click **Continue** 3. Enter the card data 4. Click **Place Order** |
| Expected Result | 1. Confirmation page shows order number (format `ORD-#####`) and total ₹1,450. 2. Order appears in My Orders with status **Paid**, correct items, quantities, address. 3. Gateway sandbox shows **one** authorization of ₹1,450. 4. Confirmation email received at `asha@example.com` within 2 minutes. 5. Cart is empty. 6. Stock of each item reduced by 1. |
| Status | Not Executed |

**TC_CHK_005: Declined card, no order created**

| Field | Value |
|---|---|
| Requirement / Scenario | US-301 AC4, AC7 / TS_CHK_12 |
| Priority | P1 |
| Preconditions | User logged in; cart contains 1 × "Wireless Mouse" (₹1,200); valid shipping info saved |
| Test Data | Card `4000 0000 0000 0002`, Exp `12/28`, CVV `123` |
| Steps | 1. Checkout, select saved address 2. Enter the card data 3. Click **Place Order** |
| Expected Result | 1. Message "Your payment was declined. Please try another card." 2. **No** new order in My Orders. 3. Cart still contains the item. 4. No confirmation email. 5. Stock unchanged. |
| Status | Not Executed |

**TC_CHK_010: Double-click Place Order creates only one order**

| Field | Value |
|---|---|
| Requirement / Scenario | US-301 AC5 / TS_CHK_15 |
| Priority | P1 |
| Preconditions | User logged in; cart contains items totalling ₹2,450; valid shipping saved |
| Test Data | Card `4111 1111 1111 1111`, Exp `12/28`, CVV `123` |
| Steps | 1. Checkout, select saved address 2. Enter the card data 3. Rapidly double-click **Place Order** |
| Expected Result | 1. Button disables after the first click. 2. Exactly **one** order is created. 3. Gateway sandbox shows exactly **one** authorization of ₹2,450. 4. One confirmation email. |
| Status | Not Executed |

**Test design techniques applied**

*BVA: PIN code (exactly 6 digits):*

| Input | Expected |
|---|---|
| `56000` (5 digits) | Rejected |
| `560001` (6 digits) | Accepted |
| `5600011` (7 digits) | Rejected (or input blocked) |
| `56000A`, empty | Rejected |

*BVA: card expiry (today = Oct 2026):* `09/26` → rejected · `10/26` → accepted (current month) · `11/26` → accepted.

*Decision table: payment outcome:*

| | R1 | R2 | R3 | R4 |
|---|---|---|---|---|
| Card details valid? | Y | Y | Y | N |
| Gateway response | Authorized | Declined / Insufficient | Timeout | – (not sent) |
| **Create order?** | ✔ | ✘ | ✘ | ✘ |
| **Charge customer?** | ✔ | ✘ | ✘ (or auto-reversed) | ✘ |
| **Send confirmation?** | ✔ | ✘ | ✘ | ✘ |
| **Message** | Confirmation page | "Payment declined" | "Payment could not be completed, you have not been charged" | Field-level validation error |

</details>

---

## Deliverable 5 — Defects

**Task:** below are **execution results** from QA build `3.2.0-rc5` (Chrome 129, Windows 11, unless stated). For the **failed** test cases:
1. Write a **full** defect report (title, environment, steps, expected vs. actual, severity, priority) for **TC_CHK_010** and **TC_CHK_007**.
2. For the other three failures, write just a **title + severity + priority**.
3. Then use the **triage and fix outcomes** to move **all five** defects through the **defect life cycle**.

### Execution Results (given)

| TC ID | Status | Actual result observed |
|---|---|---|
| TC_CHK_001 | ✅ Pass | As expected |
| TC_CHK_005 | ✅ Pass | As expected |
| TC_CHK_007 | ❌ **Fail** | Card with expiry `09/26` (past month) was **accepted**; payment authorized by sandbox and order `ORD-10311` created |
| TC_CHK_010 | ❌ **Fail** | Double-click on **Place Order** created **two** orders (`ORD-10322`, `ORD-10323`) and **two** authorizations of ₹2,450 in the sandbox. Reproduced 4/5 times |
| TC_CHK_012 | ❌ **Fail** | PIN code `56000` (5 digits) accepted; order created with invalid PIN |
| TC_CHK_015 | ❌ **Fail** | Confirmation email **not received** for 3 of 5 orders paid with a **saved card**; on-screen confirmation was correct. Emails received for all new-card orders |
| TC_CHK_018 | ❌ **Fail** | Safari on iPhone 14 (iOS 18): **Place Order** button is partly hidden behind the sticky "Need help?" chat bubble; it can be tapped only after scrolling to the very bottom |

### Triage and Fix Outcomes (given)

| Failed TC | What happened next |
|---|---|
| TC_CHK_010 | Accepted in triage. Developer fixed it in `rc6`. Retest passed; regression on checkout passed. |
| TC_CHK_007 | Accepted. Developer fixed it in `rc6`. Retest **failed**: `09/26` is now rejected, but `10/26` (current month) is **also** rejected. Fixed again in `rc7`; retest and regression passed. |
| TC_CHK_012 | Accepted. Fixed in `rc6`. Retest passed. |
| TC_CHK_015 | In triage, the lead found that **BUG-298** (raised last week) already reports "Emails not sent for saved-card payments". |
| TC_CHK_018 | Valid, but the PO decided the chat bubble redesign is scheduled for next release, and the workaround (scroll) is acceptable for now. |

<details>
<summary>✅ Model answer: sample defect reports</summary>

**BUG-311: Checkout: double-clicking "Place Order" creates duplicate orders and double-charges the customer**

| Field | Value |
|---|---|
| Module | Checkout → Payment |
| Environment | QA, build 3.2.0-rc5, Chrome 129, Windows 11 |
| Preconditions | User `asha@example.com` logged in; cart total ₹2,450; saved address |
| Test Data | Card `4111 1111 1111 1111`, Exp `12/28`, CVV `123` |
| Steps | 1. Cart → Checkout 2. Select saved address → Continue 3. Enter the card data 4. Double-click **Place Order** quickly |
| Expected | One order, one authorization of ₹2,450, button disabled after first click (US-301 AC5) |
| Actual | Two orders (`ORD-10322`, `ORD-10323`) and two authorizations of ₹2,450 in sandbox |
| Reproducibility | 4/5 attempts |
| Severity / Priority | **Critical / P1** (financial loss, customer trust) |
| Attachments | Screen recording, sandbox transaction log, My Orders screenshot |
| Linked | TC_CHK_010, US-301 |

**BUG-312: Payment: expired card (past expiry month) is accepted and an order is created**

| Field | Value |
|---|---|
| Environment | QA, build 3.2.0-rc5, Chrome 129 |
| Steps | Checkout with card `4111 1111 1111 1111`, Exp `09/26`, CVV `123` (today: 06-Oct-2026) → Place Order |
| Expected | Validation error "Card has expired"; no gateway call; no order (US-301 AC3) |
| Actual | Payment authorized, order `ORD-10311` created |
| Severity / Priority | **High / P1** (invalid payment data accepted; real gateways may decline later → orphan orders, chargebacks) |
| Linked | TC_CHK_007 |

**BUG-313: Shipping: 5-digit PIN code is accepted**
Severity / Priority: **Medium / P2** (undeliverable orders; workaround = customer support correction). Linked TC_CHK_012.

**BUG-314: Email: order confirmation not sent for saved-card payments (intermittent)**
Severity / Priority: **High / P2** (AC6 not met; on-screen confirmation exists as partial mitigation). Linked TC_CHK_015.

**BUG-315: Safari iOS: "Place Order" button partly hidden behind chat bubble**
Environment: iPhone 14, iOS 18, Safari. Severity / Priority: **Medium / P3** (workaround exists, but it may hurt conversion on mobile). Linked TC_CHK_018.

</details>

<details>
<summary>✅ Model answer: life cycle paths</summary>

| Defect | Life cycle path |
|---|---|
| BUG-311 (double charge) | New → Assigned → Open → Fixed → Retest → Verified → **Closed** |
| BUG-312 (expired card) | New → Assigned → Open → Fixed → Retest → **Reopened** (current month now wrongly rejected; a *boundary* defect introduced by the fix) → Assigned → Open → Fixed → Retest → Verified → **Closed** |
| BUG-313 (PIN code) | New → Assigned → Open → Fixed → Retest → Verified → **Closed** *(after regression on the shipping form)* |
| BUG-314 (email) | New → **Duplicate** (of BUG-298) → **Closed**. *Add your evidence to BUG-298 as a comment.* |
| BUG-315 (Safari layout) | New → **Deferred** (to next release, with PO sign-off) |

**Lessons:**
- BUG-312's reopen shows why **BVA** matters during **retesting**: always retest the boundaries around a fix (`09/26`, `10/26`, `11/26`), not just the original failing value.
- After BUG-311's fix, **regression** must cover successful payment, declined payment, and retry flows, because the fix touched the submit logic.
- **Exit criteria check:** after these outcomes there are 0 open Critical/High defects (BUG-314 is tracked under BUG-298, which must be resolved or explicitly accepted), and BUG-315 is deferred with sign-off. Release can proceed **if** BUG-298 is fixed.

</details>

---

## Deliverable 6 — Estimation

**Task:** estimate the effort to test the checkout feature. Use WBS + test-case-based estimation, then apply a three-point estimate and convert it to a schedule.

**Inputs (given):**
- 25 scenarios → **50 test cases**: 20 simple, 20 medium, 10 complex
- Average design + execution time: simple 10 + 5 min · medium 20 + 10 min · complex 40 + 20 min
- 5 platforms; a cross-platform subset of **15 test cases** runs on the 4 non-primary platforms at ~8 min each
- **12 test cases** to be automated at ~2 h each
- **2 regression cycles** of ~5 h each
- Basic performance check (~6 h) and basic security checks (~4 h)
- Team: **2 testers**, **6 productive hours/day** each

<details>
<summary>✅ Model answer</summary>

**WBS + test-case-based**

| Task | Calculation | Effort (h) |
|---|---|---|
| Requirement analysis, strategy, scenarios | — | 3 |
| Test design + 1st execution (primary platform) | Simple 20 × 15 min = 300 min · Medium 20 × 30 min = 600 min · Complex 10 × 60 min = 600 min → 1,500 min | 25 |
| Test case review | — | 3 |
| Test data & environment setup (sandbox cards, users, products, devices) | — | 6 |
| Cross-platform execution | 15 × 4 × 8 min = 480 min | 8 |
| Defect reporting & retesting | 25% of execution (primary execution: 20×5 + 20×10 + 10×20 = 500 min ≈ 8.3 h; + 8 h cross-platform ≈ 16.3 h) → 25% ≈ 4.1 | 4 |
| Automation | 12 × 2 h | 24 |
| Regression | 2 × 5 h | 10 |
| Performance check | — | 6 |
| Security checks | — | 4 |
| **Subtotal** | 3+25+3+6+8+4+24+10+6+4 | **93** |
| Management, meetings, reporting (10%) | 10% × 93 ≈ 9.3 | 9 |
| **Most likely (M)** | | **102 h** |

**Three-point estimate**
- O = 85 h · M = 102 h · P = 145 h (unstable gateway sandbox, many defects, reopened fixes like BUG-312)
- **E = (85 + 4×102 + 145) / 6 = (85 + 408 + 145) / 6 = 638 / 6 ≈ 106.3 h**
- SD = (145 − 85) / 6 = **10 h** → likely range ≈ 96–116 h

**Schedule**
- Capacity: 2 testers × 6 h = 12 h/day
- Duration: 106.3 ÷ 12 ≈ 8.9 → **~9 working days**

**Key assumptions**
- Payment sandbox and email service available and stable in QA.
- Builds pass smoke; Critical/High defects fixed within 1 day.
- Requirements (US-301 AC) stay stable; changes are re-estimated.
- Automation framework already exists (only test scripts are built).
- Full penetration testing and large-scale load testing are **out of scope** (separate teams).

**Risk to communicate:** if the sprint has only 5 testing days left, the team can't complete everything. Propose a **risk-based cut**: all P1 tests + automation of the 5 most critical flows now; defer the remaining automation and P3 tests to the next sprint.

</details>

---

## ✅ Self-Assessment Checklist

Before you finish, check your work:

- [ ] I identified **multiple testing types** and explained *why* each applies.
- [ ] My strategy ranks **risks** by likelihood × impact and sets **priorities**.
- [ ] I chose **automation candidates** using clear criteria (repetitive, stable, critical).
- [ ] My scenarios cover **positive, negative, boundary, alternate, exception, and E2E** cases and map to **every AC**.
- [ ] My test cases have **specific test data** and **expected results that include what must *not* happen**.
- [ ] I applied at least one **test design technique** (EP/BVA, decision table, state transition).
- [ ] My defect reports are **reproducible** with correct **severity vs. priority**.
- [ ] I traced each defect through the correct **life cycle path** (including Reopened, Duplicate, Deferred).
- [ ] My estimate includes **all task types**, a **three-point** calculation, a **schedule**, and **assumptions**.

---

## 🎓 Course Wrap-Up

You've completed the **Test Design & Strategy** module. The journey you just took is the same one you'll follow on real projects:

```text
Testing Types → Strategy → Use Cases / User Stories → Acceptance Criteria
      → Test Scenarios → Test Cases (with design techniques) → Defects → Estimation
```

### Where to go next
- **Practice:** pick any app you use daily (food delivery, banking, ride-hailing) and write scenarios and test cases for one feature.
- **Tools:** learn a test management tool (Jira + Xray/Zephyr, TestRail) and a bug tracker (Jira, Azure DevOps).
- **Next skills:** SQL for data validation, API testing with Postman, and an introduction to automation (Selenium / Playwright).
- **Certification (optional):** the **ISTQB Certified Tester Foundation Level (CTFL)** syllabus covers these concepts in depth.

> **Remember:** good testers don't just find bugs. They provide **evidence about quality** so their team can make **confident decisions**.

---

**Back to start:** [← Chapter 1 — Testing Types](01-testing-types.md)

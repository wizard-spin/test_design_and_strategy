# Chapter 6 — Test Scenarios

> **Software Testing Foundation · Module: Test Design & Strategy**
> **Chapter 6 of 10** · ⏱️ **Time: 20 minutes** (12 min reading + 8 min practice)
> **Previous:** [← Chapter 5 — Acceptance Criteria](05-acceptance-criteria.md) · **Next:** [Chapter 7 — Test Case Design →](07-test-case-design.md)

---

## Learning Objectives

By the end of this chapter you will be able to:

1. Define a test scenario and distinguish it from a requirement and from a test case.
2. Identify **positive, negative, boundary, alternate-flow, exception, and end-to-end** scenarios.
3. Apply systematic **scenario identification techniques**.
4. Produce a scenario list for a feature and prioritise it.

---

## Where This Chapter Fits

You now have requirements as **use cases** (Ch. 3) or **user stories** (Ch. 4) with **acceptance criteria** (Ch. 5). Before writing detailed test cases (Ch. 7), you need to decide **what situations to test**. That list of situations is your set of **test scenarios**. It is the "map" of your testing.

---

## 1. What Is a Test Scenario?

A **test scenario** is a **one-line description of a situation or functionality that needs to be tested**. It states *what* to verify, without detailed steps or data.

> **Test scenario = What should we test?**
> **Test case = How exactly will we test it?**

Example scenario: *"Verify that a user cannot transfer more money than the available balance."*

### Why Write Scenarios First?
- **Fast coverage thinking:** you can list 30 scenarios in the time it takes to write 3 detailed test cases.
- **Easy to review:** a product owner can read a scenario list and spot gaps.
- **Prioritisation:** you can rank scenarios by risk before investing in detail.
- **Agile-friendly:** in fast-moving teams, a scenario list plus exploratory testing may be enough for low-risk features.

---

## 2. Scenario vs. Requirement vs. Test Case

| | **Requirement** | **Test Scenario** | **Test Case** |
|---|---|---|---|
| **Describes** | What the system *should* do | A *situation* to verify | *Exact steps* and expected result |
| **Owner** | Business / product owner | Tester | Tester |
| **Detail** | Business-level | One line | Preconditions, data, steps, expected result |
| **Example** | "User can transfer money between accounts." | "Transfer amount exceeding balance." | "Login as user A (balance ₹5,000) → Transfer → Enter ₹5,001 → Submit → Expect error 'Insufficient balance', balance unchanged." |
| **Ratio** | 1 | → many | → many more |

---

## 3. Types of Scenarios

We'll use one requirement throughout:

> **Requirement:** User can transfer money between accounts.

| Type | Purpose | Example |
|---|---|---|
| **Positive** | Valid input → expected success | Transfer valid amount. |
| **Negative** | Invalid input/action → handled gracefully | Transfer negative amount. Transfer to invalid account. |
| **Boundary** | Values at or around limits | Transfer zero amount. Transfer exactly the balance. Transfer above daily limit. |
| **Alternate flow** | Valid variations of the main path | Transfer to a saved beneficiary vs. a new one; schedule a future-dated transfer. |
| **Exception** | System or environment failures | Network failure during transfer. Session expires during transfer. |
| **End-to-end** | Complete business flow across modules/systems | Add beneficiary → transfer → receive SMS → verify both account statements. |

### The Classic Scenario List

Possible scenarios:

1. Transfer valid amount.
2. Transfer amount exceeding balance.
3. Transfer zero amount.
4. Transfer negative amount.
5. Transfer to invalid account.
6. Transfer above daily limit.
7. Network failure during transfer.
8. Session expires during transfer.

Classified:

| # | Scenario | Type |
|---|---|---|
| 1 | Transfer valid amount | Positive |
| 2 | Transfer amount exceeding balance | Negative / Boundary |
| 3 | Transfer zero amount | Boundary / Negative |
| 4 | Transfer negative amount | Negative |
| 5 | Transfer to invalid account | Negative |
| 6 | Transfer above daily limit | Boundary / Business rule |
| 7 | Network failure during transfer | Exception |
| 8 | Session expires during transfer | Exception |

### A Thoughtful Tester Adds More…

| # | Scenario | Type |
|---|---|---|
| 9 | Transfer exactly the full available balance | Boundary |
| 10 | Transfer exactly the daily limit | Boundary |
| 11 | Transfer amount with more than 2 decimal places (₹100.555) | Negative |
| 12 | Transfer to own same account | Negative |
| 13 | Double-click "Submit": only one transfer should happen | Exception / Error guessing |
| 14 | Two transfers in parallel from two browser tabs exceeding the combined balance | Exception / Concurrency |
| 15 | Transfer to a newly added beneficiary within the cooling period | Business rule |
| 16 | Transfer requires OTP; wrong/expired OTP | Negative / Security |
| 17 | Sender and receiver statements both reflect the transfer; SMS sent | End-to-end |
| 18 | Unauthorised user tries to transfer from another user's account (URL tampering) | Security |

> Notice how **8 scenarios became 18** just by thinking systematically. Missing scenarios lead to missed bugs, and no amount of detail in your test cases can make up for a scenario you never thought of.

---

## 4. Scenario Identification Techniques

Use these systematically, not randomly:

### 4.1 Requirement / AC Walkthrough
Go through **each acceptance criterion** and write at least one positive and one negative scenario for it.

### 4.2 Use Case Flow Analysis
For each use case: **1 scenario per main flow + 1 per alternate flow + 1 per exception flow** (Chapter 3).

### 4.3 Input Analysis (CRUD + Fields)
For each **input field**, ask: valid? invalid? empty? boundary? wrong format? too long? special characters?
For each **data entity**, ask: can it be **C**reated, **R**ead, **U**pdated, **D**eleted correctly?

### 4.4 User Role Analysis
How does the feature behave for **each role**: guest, customer, premium customer, admin, blocked user?

### 4.5 State-Based Thinking
What states can the object be in? *(Order: Created → Paid → Shipped → Delivered → Cancelled.)* Test the valid transitions and try the invalid ones. (More in Chapter 7.)

### 4.6 "What If?" / Error Guessing
Use experience to guess where it breaks: double-click, Back button, refresh during submit, session timeout, slow network, copy-pasted text with spaces, emoji, very large numbers.

### 4.7 Integration Touchpoints
Where does the feature talk to another system (payment gateway, email, SMS, inventory)? What if that system is **slow, down, or returns an error**?

### 4.8 Mind Maps
Many testers draw a mind map with the feature at the centre and branches for inputs, roles, flows, integrations, and non-functional needs. Each leaf becomes a scenario.

```text
                       ┌─ Valid amount
            ┌─ Amount ─┼─ Zero / Negative / Decimals
            │          └─ > Balance / > Daily limit
            │
            ├─ Account ─┬─ Valid / Invalid / Own account
 FUND ──────┤           └─ New beneficiary (cooling period)
TRANSFER    │
            ├─ Security ─ OTP / Session / URL tampering
            │
            ├─ Failures ─ Network / Timeout / Double submit
            │
            └─ End-to-end ─ Statements / SMS / Audit log
```

---

## 5. Writing Good Scenarios

| ✅ Good | ❌ Poor | Why |
|---|---|---|
| Verify transfer is rejected when amount exceeds available balance | Test transfer | Too vague; which situation? |
| Verify user receives SMS after successful transfer | Click submit and check if SMS comes on phone number 98xxxx | Too detailed; that's a test case |
| Verify only one transfer is created when Submit is double-clicked | Check bugs in transfer | Not a specific situation |

**Format tips:**
- Start with "**Verify** …" or "**Check** …" (common convention).
- One situation per scenario.
- Give each scenario an ID (e.g., `TS_TRF_01`) for traceability.
- Link each scenario to its requirement/AC.

### Prioritise Your Scenarios
Apply **risk-based testing** (Chapter 2). Tag each scenario:
- **P1:** money, security, data loss, core flow (Scenarios 1, 2, 6, 13, 14, 18)
- **P2:** important validations (3, 4, 5, 9, 10, 16)
- **P3:** rare or cosmetic (11, 12)

---

## ⚠️ Common Beginner Mistakes

1. **Only positive scenarios.** The happy path is typically less than 20% of what you should test.
2. **Writing test cases instead of scenarios** (too much detail too early).
3. **Ignoring exceptions and integrations:** network failures, timeouts, third-party errors.
4. **Not tracing scenarios to requirements.** Then you can't prove coverage.
5. **Forgetting end-to-end scenarios.** Each module works alone, but the full journey fails.

---

## 📝 Practice Exercise (≈ 8 minutes)

**Requirement (ShopEasy):**
> As a shopper, I want to add products to my cart so that I can buy them together.

Known rules:
- Max quantity per product: 10
- Out-of-stock products cannot be added
- Cart persists for logged-in users across devices
- Guest users can add to cart; the cart is kept for 7 days

**Task:** list at least **12 scenarios**, classified by type (Positive, Negative, Boundary, Alternate, Exception, E2E).

<details>
<summary>✅ Model answer (click to expand)</summary>

| ID | Scenario | Type |
|---|---|---|
| TS_CART_01 | Verify a logged-in user can add an in-stock product to the cart | Positive |
| TS_CART_02 | Verify a guest user can add a product to the cart | Alternate |
| TS_CART_03 | Verify adding the same product again increases quantity (not a duplicate line) | Positive |
| TS_CART_04 | Verify quantity 10 is accepted | Boundary |
| TS_CART_05 | Verify quantity 11 is rejected with a clear message | Boundary / Negative |
| TS_CART_06 | Verify quantity 0 or negative is not allowed | Boundary / Negative |
| TS_CART_07 | Verify an out-of-stock product cannot be added | Negative |
| TS_CART_08 | Verify a product that goes out of stock *after* being added is flagged in the cart | Exception |
| TS_CART_09 | Verify the cart persists when a logged-in user switches from desktop to mobile | Alternate |
| TS_CART_10 | Verify a guest cart is retained on day 7 and cleared after day 7 | Boundary / Business rule |
| TS_CART_11 | Verify a guest cart merges into the user's cart after login | Alternate |
| TS_CART_12 | Verify the cart total updates correctly on quantity change/removal | Positive |
| TS_CART_13 | Verify behaviour when the network drops while adding to cart | Exception |
| TS_CART_14 | Verify rapid multiple clicks on "Add to Cart" don't add unexpected quantities | Exception / Error guessing |
| TS_CART_15 | Verify price change of an item in the cart is reflected before checkout | Exception / Business rule |
| TS_CART_16 | Verify the full journey: search → add to cart → update quantity → checkout | End-to-end |

**Question for the PO** (raised by scenario 11): when a guest cart merges with an existing user cart and the same product exceeds 10 units combined, what happens?

</details>

---

## 🧠 Quick Check

1. Scenario = ______ to test; test case = ______ to test it.
2. "Network failure during transfer" is which scenario type?
3. Name three scenario identification techniques.
4. Why write scenarios before test cases?

<details>
<summary>Answers</summary>

1. **What** to test; **how exactly** to test it.
2. **Exception** scenario.
3. Any three of: AC walkthrough, use case flow analysis, input analysis/CRUD, user role analysis, state-based thinking, error guessing, integration touchpoints, mind maps.
4. They're fast to write, easy to review for gaps, and easy to prioritise before investing in detail.

</details>

---

## 🔑 Key Takeaways

- A scenario is a **one-line "what to test"**. A test case is the **detailed "how"**.
- Cover **positive, negative, boundary, alternate, exception, and end-to-end** scenarios.
- Use **systematic techniques** (AC walkthrough, flows, inputs, roles, states, error guessing, integrations).
- **Prioritise** scenarios by risk and **trace** them to requirements.
- Missing a scenario means missing a bug. Breadth of thinking comes first.

---

**Next:** [Chapter 7 — Test Case Design →](07-test-case-design.md). The biggest chapter of the module. You'll turn scenarios into detailed, executable test cases and learn the formal techniques that make your tests efficient and effective.

# Chapter 5 — Acceptance Criteria

> **Software Testing Foundation · Module: Test Design & Strategy**
> **Chapter 5 of 10** · ⏱️ **Time: 20 minutes** (12 min reading + 8 min practice)
> **Previous:** [← Chapter 4 — User Stories](04-user-stories.md) · **Next:** [Chapter 6 — Test Scenarios →](06-test-scenarios.md)

---

## Learning Objectives

By the end of this chapter you will be able to:

1. Define acceptance criteria (AC) and explain why they matter to testers.
2. Write functional AC that cover **business rules**, **boundary conditions**, and **positive and negative conditions**.
3. Write AC in the **Given / When / Then** format.
4. Distinguish **Definition of Done** from **Acceptance Criteria**.
5. Review AC and find gaps before development starts.

---

## Where This Chapter Fits

In Chapter 4, a user story told us *who* wants *what* and *why*. But "I want to reset my password" leaves many questions open. How long is the link valid? What if the email is not registered? **Acceptance criteria** answer those questions. They are the bridge between the requirement and your **test scenarios** (Chapter 6) and **test cases** (Chapter 7).

---

## 1. What Are Acceptance Criteria?

**Acceptance criteria** are a set of **specific, testable conditions** that a user story must satisfy to be **accepted** by the product owner or customer.

> **Key insight:** Acceptance criteria defines what must be true for the story to be accepted.
> It therefore becomes a major input for test design.

### Characteristics of Good AC
- **Clear:** everyone reads them the same way.
- **Testable:** each one gives a pass/fail result.
- **Concise:** they describe *what*, not *how* to implement it.
- **Independent of UI design** where possible ("user is notified", not "a red toast appears at top-right"), unless the UI itself is the requirement.
- **Agreed** by product owner, developers, and testers *before* development.

---

## 2. Why Acceptance Criteria Matter to Testers

| Without AC | With AC |
|---|---|
| Tester guesses expected behaviour | Expected results are defined |
| Developer and tester disagree on "is this a bug?" | Shared, agreed definition of correct behaviour |
| Edge cases discovered in production | Edge cases discussed in refinement (shift-left) |
| "Done" is subjective | "Done" is verifiable |

Each acceptance criterion typically becomes **one or more test scenarios**, and each scenario becomes **one or more test cases**:

```text
User Story ──► Acceptance Criteria ──► Test Scenarios ──► Test Cases
   (1)              (5–10)                (10–30)            (20–100)
```

---

## 3. Types of Content in Acceptance Criteria

### 3.1 Functional Acceptance Criteria
What the system must **do**.
- "A reset link is sent to the registered email address."
- "The user can log in with the new password."

### 3.2 Business Rules
Organisational policies or constraints the system must enforce.
- "Reset link expires after 30 minutes."
- "The last 3 passwords cannot be reused."
- "Free shipping applies to orders above ₹999."

### 3.3 Boundary Conditions
Limits where behaviour changes. Defects cluster here (see Boundary Value Analysis in Chapter 7).
- "Password must be **8 to 20** characters." What about 7, 8, 20, 21?
- "Maximum **5** reset requests per hour." What happens on the 6th?
- "Link valid for **30 minutes**." What about 29:59 and 30:01?

### 3.4 Positive and Negative Conditions
- **Positive:** valid inputs and actions produce the expected success.
  "Given a registered email, the reset link is sent."
- **Negative:** invalid inputs and actions are handled gracefully.
  "Given an unregistered email, the user sees an appropriate message."

> 💡 Many stories arrive with only positive AC. **Asking for the negative ones is one of the most valuable things a tester does in refinement.**

### 3.5 Non-Functional Criteria (where relevant)
- "Reset email is delivered within 2 minutes."
- "The reset link token is single-use and not guessable."

---

## 4. Given / When / Then (Gherkin Format)

This format comes from **Behaviour-Driven Development (BDD)**. It makes AC precise and easy to turn into tests, including automated tests in tools like Cucumber.

| Keyword | Meaning | Maps to (in a test case) |
|---|---|---|
| **Given** | The starting context or state | Preconditions |
| **When** | The action or event | Test steps |
| **Then** | The expected outcome | Expected result |
| **And / But** | Extra conditions or outcomes | Additional preconditions, steps, or results |

### Worked Example: Password Reset

**User Story:**
> As a customer, I want to reset my password so that I can regain access to my account.

**Acceptance Criteria:**

```text
Given a registered user
When the user requests a password reset
Then a reset link should be sent to the registered email address.
```

Additional criteria:

- Reset link expires after a defined period.
- Invalid/expired links cannot be used.
- New password must satisfy password policy.
- User receives an appropriate error for an unregistered account.

Now let's make **all** of them precise in Given/When/Then form, with the "defined period" and the "password policy" made concrete. Making vague phrases concrete is exactly what a tester should push for.

```gherkin
Scenario: AC1 – Reset link sent to registered user
  Given a registered user with email "asha@example.com"
  When the user requests a password reset for "asha@example.com"
  Then a reset link is sent to "asha@example.com"
  And the screen shows "If this email is registered, you will receive a reset link"

Scenario: AC2 – Link expires after 30 minutes
  Given a reset link was generated 31 minutes ago
  When the user opens the link
  Then the user sees "This link has expired"
  And the user is offered to request a new link

Scenario: AC3 – Used link cannot be reused
  Given the user has already reset the password using a link
  When the user opens the same link again
  Then the user sees "This link is invalid"

Scenario: AC4 – New password must satisfy the policy
  Given the user opened a valid reset link
  When the user enters a new password "abc"
  Then the password is rejected
  And the user sees "Password must be 8–20 characters with at least 1 number and 1 uppercase letter"

Scenario: AC5 – Unregistered email
  Given no account exists for "ghost@example.com"
  When the user requests a password reset for "ghost@example.com"
  Then no email is sent
  And the user sees the same neutral message as AC1
```

> 🔐 **Tester insight on AC5:** the original criterion says the user gets "an appropriate error". But showing *"This email is not registered"* tells attackers which emails exist (account enumeration). A thoughtful tester raises this in refinement, and many secure systems show the **same neutral message** for both cases. This is why testers should **question** AC, not just consume them.

### Checklist Format (Alternative)
Not every team uses Gherkin. A plain checklist is also common:

- [ ] Reset link is sent to the registered email within 2 minutes.
- [ ] Link expires after 30 minutes.
- [ ] Link can be used only once.
- [ ] New password: 8–20 characters, ≥1 uppercase, ≥1 number.
- [ ] New password cannot match the last 3 passwords.
- [ ] Unregistered email shows a neutral message; no email is sent.

**Use Given/When/Then** for behaviour with clear context, action, and outcome. **Use checklists** for simple rules or lists of constraints.

---

## 5. Definition of Done vs. Acceptance Criteria

| | **Acceptance Criteria** | **Definition of Done (DoD)** |
|---|---|---|
| **Scope** | **Specific to one** user story | Applies to **every** story in the team |
| **Defines** | *What* the story must do (functional correctness) | *Quality standards / process steps* that must be completed |
| **Written by** | Product owner (with team input) | The whole team (agreed once, updated occasionally) |
| **Example** | "Reset link expires after 30 minutes" | "Code reviewed; unit tests pass; QA tested; no open High defects; documentation updated; deployed to staging" |

**A story is complete only when BOTH are satisfied:**

```text
Story is DONE  =  All its Acceptance Criteria pass  +  Team's Definition of Done met
```

*Example:* the password-reset feature works perfectly (all AC pass), but the code was never reviewed and no regression run was done. The DoD is **not** met, so the story is **not done**.

---

## 6. How to Review Acceptance Criteria (Tester's Checklist)

When you receive AC in refinement, ask:

| Question | Example |
|---|---|
| Is every criterion **testable** (pass/fail)? | "Fast" → "within 2 seconds" |
| Are **negative** paths covered? | What if the email is not registered? |
| Are **boundaries** specified? | Is "30 minutes" inclusive? What about exactly 30:00? |
| Are **business rules** explicit? | Can the old password be reused? |
| Are **error messages** defined? | What exact message does the user see? |
| Are **roles/permissions** clear? | Can an admin reset another user's password? |
| What about **concurrency / repetition**? | User requests 3 links; which one works? |
| Any **non-functional** expectations? | Email delivery time; link security |

---

## ⚠️ Common Beginner Mistakes

1. **Assuming the AC are complete.** They rarely are. Your questions improve them.
2. **Vague AC:** "should work properly", "appropriate error". Push for specifics.
3. **Mixing implementation details into AC** ("use a JWT token stored in Redis").
4. **Confusing DoD with AC.** "Code reviewed" is DoD, not an AC.
5. **Only testing the AC.** AC are the *minimum*. Exploratory testing finds what the AC missed.

---

## 📝 Practice Exercise (≈ 8 minutes)

**User Story:**
> As a shopper, I want to apply a discount coupon at checkout so that I can save money on my order.

Business information from the product owner:
- Coupon codes are 6–10 alphanumeric characters.
- Each coupon has an expiry date and a minimum order value.
- Only one coupon per order.
- Coupon `WELCOME10` gives 10% off, valid only on the first order.

**Task:** write at least **6 acceptance criteria** (Given/When/Then or checklist) that cover positive, negative, boundary, and business-rule conditions.

<details>
<summary>✅ Model answer (click to expand)</summary>

```gherkin
Scenario: Valid coupon applied
  Given my cart total is ₹1,500 and coupon "SAVE200" (₹200 off, min ₹1,000) is active
  When I apply "SAVE200"
  Then ₹200 is deducted and the new total ₹1,300 is shown

Scenario: Order below minimum value
  Given my cart total is ₹999 and coupon "SAVE200" requires a minimum of ₹1,000
  When I apply "SAVE200"
  Then the coupon is rejected with "Minimum order value ₹1,000 required"

Scenario: Order exactly at minimum value (boundary)
  Given my cart total is exactly ₹1,000
  When I apply "SAVE200"
  Then the coupon is applied

Scenario: Expired coupon
  Given coupon "SUMMER25" expired yesterday
  When I apply "SUMMER25"
  Then I see "This coupon has expired" and the total is unchanged

Scenario: Invalid coupon code
  When I apply "XYZ999" which does not exist
  Then I see "Invalid coupon code"

Scenario: Only one coupon per order
  Given "SAVE200" is already applied
  When I try to apply another coupon "FREESHIP"
  Then I see "Only one coupon can be applied per order"

Scenario: First-order-only coupon used by returning customer
  Given I have previously placed an order
  When I apply "WELCOME10"
  Then I see "This coupon is valid only on your first order"
```

**Checklist additions (boundaries and format):**
- [ ] Codes of 5 or 11 characters are rejected by validation; 6 and 10 are accepted.
- [ ] Special characters (`SAVE@20`) are rejected.
- [ ] Coupon codes are case-insensitive (**question for PO**: is `save200` valid?).
- [ ] If items are removed so the total drops below the minimum, the coupon is removed automatically (**question for PO**).
- [ ] The discount is shown as a separate line in the order summary.

**Notice** the questions to the product owner. Writing AC exposes gaps in the requirements, and that is shift-left in action.

</details>

---

## 🧠 Quick Check

1. In Given/When/Then, which part maps to the **expected result** of a test case?
2. "All code must be peer reviewed" is part of the AC or the DoD?
3. Name the four kinds of conditions good AC should cover.
4. Why might "Email not registered" be a risky error message?

<details>
<summary>Answers</summary>

1. **Then.**
2. **Definition of Done.**
3. Functional/business rules, boundary conditions, positive conditions, negative conditions.
4. It reveals which emails are registered (an **account enumeration** security risk).

</details>

---

## 🔑 Key Takeaways

- **Acceptance criteria define what must be true for a story to be accepted.** They are the tester's primary input.
- Good AC cover **functional behaviour, business rules, boundaries, and positive and negative conditions**.
- **Given / When / Then** maps directly to **Preconditions / Steps / Expected Result**.
- **AC** are story-specific. The **DoD** applies to all stories. You need both for "done".
- Testers **review and improve** AC before development. Don't just accept them.

---

**Next:** [Chapter 6 — Test Scenarios →](06-test-scenarios.md). With clear acceptance criteria in hand, you will learn how to identify **all the meaningful situations** that need testing.

# Chapter 4 — User Stories

> **Software Testing Foundation · Module: Test Design & Strategy**
> **Chapter 4 of 10** · ⏱️ **Time: 15 minutes** (9 min reading + 6 min practice)
> **Previous:** [← Chapter 3 — Use Cases](03-use-cases.md) · **Next:** [Chapter 5 — Acceptance Criteria →](05-acceptance-criteria.md)

---

## Learning Objectives

By the end of this chapter you will be able to:

1. Explain what a user story is and why Agile teams use them.
2. Write stories in the **As a / I want / So that** format.
3. Evaluate stories using the **INVEST** principles.
4. Distinguish **epic**, **feature**, and **user story**.
5. Compare user stories with use cases.

---

## Where This Chapter Fits

Chapter 3 covered **use cases**, which are detailed interaction descriptions. Most software companies today work in **Agile (Scrum/Kanban)**, where requirements arrive as short **user stories** in tools like Jira or Azure DevOps. As a QA engineer you will read, question, and test user stories every sprint. Chapter 5 then adds the most important part for testers: **acceptance criteria**.

---

## 1. What Is a User Story?

A **user story** is a short, simple description of a feature, told from the perspective of the **person who wants it**. It focuses on **who** needs it, **what** they need, and **why**.

> A user story is a *placeholder for a conversation*. It is not a full specification. The detail comes from discussion and from acceptance criteria.

### The "3 Cs" of a User Story
| C | Meaning |
|---|---|
| **Card** | The short written statement (traditionally on an index card) |
| **Conversation** | Discussion between product owner, developers, and testers to clarify details |
| **Confirmation** | Acceptance criteria that confirm when the story is done (Chapter 5) |

---

## 2. Agile Requirement Structure: User / Action / Value

### Standard Format

> **As a** [user]
> **I want** [capability]
> **So that** [business value]

| Part | Question | Why it matters to testers |
|---|---|---|
| **As a** *(user / role)* | **Who** benefits? | Tells you which role to test as (customer, admin, guest). Permissions differ by role. |
| **I want** *(action / capability)* | **What** do they need to do? | Tells you the functionality under test. |
| **So that** *(value)* | **Why** does it matter? | Tells you the *real goal*. A feature can "work" and still fail its purpose. |

### Example

> As a customer, I want to reset my password so that I can regain access to my account.

- **User:** customer (not admin, not guest)
- **Action:** reset password
- **Value:** regain access, so the whole flow must actually let them log in again

### More Examples

| Good user story |
|---|
| As a **shopper**, I want to **filter products by price range** so that **I can find items within my budget**. |
| As a **returning customer**, I want to **save my card details** so that **I can check out faster next time**. |
| As a **store admin**, I want to **mark products as out of stock** so that **customers don't order unavailable items**. |
| As a **bank customer**, I want to **receive an SMS for every transaction** so that **I can detect fraud quickly**. |

---

## 3. INVEST Principles

A good user story should be **INVEST**:

| Letter | Principle | Meaning | ❌ Bad example → ✅ Fix |
|---|---|---|---|
| **I** | **Independent** | Can be developed and delivered without depending on other stories | "Build the checkout page UI" + "Make checkout work" → combine into one story that delivers a working slice |
| **N** | **Negotiable** | Not a rigid contract; details can be discussed | "Use a 14px blue button at x=200" → describe the need, let the team decide |
| **V** | **Valuable** | Delivers value to a user or the business | "Refactor the database layer" (no user value by itself) → link it to a user-visible benefit or treat it as a technical task |
| **E** | **Estimable** | The team can estimate its size | "Improve the website" → too vague to estimate |
| **S** | **Small** | Fits within one sprint | "Build the entire payment system" → split it (card payment, UPI, wallet…) |
| **T** | **Testable** | Clear enough that you can verify it is done | "The page should be user-friendly" → "A new user can complete checkout in ≤ 3 steps" |

> 🔍 **The tester's special responsibility is the "T".** If you cannot work out how to test a story, raise it in refinement *before* development starts. This is shift-left testing (Chapter 2) in practice.

---

## 4. Epic vs. Feature vs. User Story

Requirements are organised as a hierarchy, from large to small:

```text
EPIC: Online Checkout                         (large business goal; spans many sprints)
│
├── FEATURE: Payment Processing               (a distinct capability; one or more sprints)
│   ├── STORY: Pay with credit card           (fits in one sprint)
│   ├── STORY: Pay with UPI
│   └── STORY: Retry a failed payment
│
├── FEATURE: Shipping
│   ├── STORY: Add a new shipping address
│   └── STORY: Choose delivery speed
│
└── FEATURE: Order Confirmation
    ├── STORY: View confirmation page
    └── STORY: Receive confirmation email
```

| | **Epic** | **Feature** | **User Story** |
|---|---|---|---|
| Size | Very large | Medium | Small |
| Duration | Multiple sprints / releases | One or a few sprints | Within one sprint |
| Testable directly? | Not directly; too big | Partially | ✅ Yes, via its acceptance criteria |
| Example | "Online Checkout" | "Payment Processing" | "Pay with credit card" |

> Some teams skip the "feature" level and go straight from epic to story. Terminology varies by company and tool, so learn your team's convention.

---

## 5. User Story vs. Use Case

| | **User Story** | **Use Case** |
|---|---|---|
| **Format** | One sentence + acceptance criteria | Structured document with flows |
| **Focus** | *Who* wants *what* and *why* (value) | *How* the actor and system interact (steps) |
| **Detail** | Low; details emerge through conversation | High; flows written up front |
| **Typical context** | Agile / Scrum | Waterfall, V-model, regulated domains |
| **Exceptions/alternates** | Captured in acceptance criteria (often incompletely) | Explicit alternate and exception flows |
| **Tester's job** | Ask questions to find the missing flows | Derive scenarios from the documented flows |

> 💡 **Pro tip:** when you receive a user story, mentally write the use case for it (main flow, alternates, exceptions). Every exception you think of that is *not* in the acceptance criteria is a question for the product owner.

---

## ⚠️ Common Beginner Mistakes

1. **Accepting stories without a "So that".** Without the value, you cannot judge whether the feature serves its purpose.
2. **Writing stories from the system's view:** "As the system, I want to validate emails…". Stories should be about a real user role.
3. **Huge stories** that cannot be tested within a sprint.
4. **Vague, untestable words** like "fast", "easy", "user-friendly", or "secure" with no measurable meaning.

---

## 📝 Practice Exercise (≈ 6 minutes)

Convert these raw requirements into user stories (As a / I want / So that). Then name **one INVEST concern** if there is any.

1. "The system must allow registered users to view their past orders."
2. "Admins should be able to issue refunds."
3. "Add wishlist functionality."
4. "The app should send notifications when an item is back in stock."
5. "Make the whole mobile app better."

<details>
<summary>✅ Model answer (click to expand)</summary>

1. **As a** registered customer, **I want** to view my past orders **so that** I can track purchases and reorder items.
   *INVEST:* fine. To keep it **Small**, you might define a limit (for example, the last 12 months, paginated).

2. **As a** store admin, **I want** to issue a full or partial refund for an order **so that** I can resolve customer complaints quickly.
   *INVEST:* **Testable?** Ask: is there a refund limit? Does it need approval? How is the customer notified?

3. **As a** shopper, **I want** to add products to a wishlist **so that** I can buy them later without searching again.
   *INVEST:* possibly not **Small**. Consider splitting into "add/remove items", "view wishlist", and "move to cart".

4. **As a** shopper, **I want** to be notified when an out-of-stock item I'm interested in becomes available **so that** I don't miss buying it.
   *INVEST:* **Negotiable / Testable.** Ask which channel (email, push, SMS), how quickly after restock, and whether it is sent once or repeatedly.

5. ❌ **Cannot be written as a good story.** It fails **E**stimable, **S**mall, and **T**estable. Ask the product owner: *"Better in what way? Faster load times? Fewer crashes? Easier navigation?"* Then write specific stories, e.g. *"As a mobile shopper, I want product pages to load in under 2 seconds so that I don't abandon my search."*

</details>

---

## 🧠 Quick Check

1. What are the three parts of the standard user story format?
2. Which INVEST letter matters most directly to a tester?
3. Which is bigger: an epic or a user story?
4. Where are the detailed conditions of a user story captured?

<details>
<summary>Answers</summary>

1. **As a** [user], **I want** [capability], **so that** [business value].
2. **T, Testable.**
3. **Epic.**
4. In the **acceptance criteria**, which is the next chapter.

</details>

---

## 🔑 Key Takeaways

- A user story = **who + what + why**, written from the user's perspective.
- Stories are **conversation starters**. Testers should ask questions early.
- Use **INVEST** to judge story quality, especially **Testable**.
- **Epic → Feature → Story**, from large business goal to sprint-sized slice.
- Use cases describe **interaction flows**. User stories describe **needs and value**.

---

**Next:** [Chapter 5 — Acceptance Criteria →](05-acceptance-criteria.md). A story only says *what* the user wants. **Acceptance criteria** define *exactly when it is done*, and they are the tester's most important input.

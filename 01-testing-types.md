# Chapter 1 — Testing Types

> **Software Testing Foundation · Module: Test Design & Strategy**
> **Chapter 1 of 10** · ⏱️ **Time: 20 minutes** (12 min reading + 8 min practice)
> **Next:** [Chapter 2 — Test Strategies →](02-test-strategies.md)

---

## Learning Objectives

By the end of this chapter you will be able to:

1. Explain the difference between a **testing level** and a **testing type**.
2. Distinguish functional vs. non-functional, manual vs. automated, static vs. dynamic, and black-box vs. white-box testing.
3. Describe the four testing levels: unit, integration, system, acceptance.
4. Recognise when to use regression, smoke, sanity, exploratory, performance, security, usability, and compatibility testing.
5. Choose the right testing types for a real feature.

---

## Where This Chapter Fits

This is where the course starts. Before you can plan testing (Chapter 2) or design test cases (Chapter 7), you need the vocabulary QA teams use every day. In stand-ups, test plans, and bug reports you will hear things like *"Did we smoke test the build?"* or *"Is that covered by regression?"*. This chapter explains those terms.

---

## 1. What Is a Testing Type?

A **testing type** is a category of testing defined by **what** you are checking, or **how** you are checking it. Login tests check *functionality*. Load tests check *performance*. Penetration tests check *security*.

There are dozens of testing terms, and beginners often think each one is a separate, competing activity. They are not. Each term describes **one dimension** of a testing activity.

### The Key Distinction

> **Testing level answers:** *Where are we testing?* (at which stage or layer of the system)
> **Testing type answers:** *What characteristic are we testing?*

One activity can be described along several dimensions at the same time. For example:

> **System + Functional + Regression testing**

This means:

- **System** (level): we test the fully integrated application.
- **Functional** (type): we check that features behave correctly.
- **Regression** (purpose): we confirm that existing features still work after a change.

These are **not competing categories**. They describe different dimensions of the same activity, much like a car can be *red*, *electric*, and *a sedan* all at once.

### The Dimensions at a Glance

| Dimension | Question it answers | Options |
|---|---|---|
| **Level** | Where / at what scope? | Unit, Integration, System, Acceptance |
| **Characteristic** | What quality are we checking? | Functional, Non-functional (performance, security, usability, compatibility…) |
| **Execution** | Who or what runs it? | Manual, Automated |
| **Code execution** | Is the software running? | Static, Dynamic |
| **Knowledge of internals** | Can we see the code? | Black-box, White-box (and grey-box) |
| **Purpose / timing** | Why are we running it now? | Smoke, Sanity, Regression, Exploratory |

---

## 2. Functional vs. Non-Functional Testing

| | **Functional Testing** | **Non-Functional Testing** |
|---|---|---|
| **Checks** | *What* the system does | *How well* the system does it |
| **Based on** | Business requirements, user stories | Quality attributes (speed, security, usability…) |
| **Example question** | "Does clicking *Login* with valid credentials open the dashboard?" | "Does login respond within 2 seconds with 1,000 users online?" |
| **Examples** | Login, search, add to cart, checkout | Performance, security, usability, compatibility, reliability |

**Rule of thumb:** if a requirement can be phrased as *"The system shall do X"*, it is functional. If it is phrased as *"The system shall be fast / secure / easy / available…"*, it is non-functional.

---

## 3. Manual vs. Automated Testing

| | **Manual Testing** | **Automated Testing** |
|---|---|---|
| **Executed by** | A human tester | A script or tool (Selenium, Playwright, Cypress, JUnit, Postman…) |
| **Best for** | New features, exploratory work, usability, one-off checks, frequently changing UI | Repetitive regression, data-driven tests, smoke tests in CI/CD, performance/load |
| **Strengths** | Human judgement, intuition, sees visual problems | Fast, repeatable, runs at night, scales |
| **Weaknesses** | Slow, can be tedious, prone to human error | Costly to build and maintain; checks only what it is told to check |

> 💡 Automation does **not** replace manual testers. Automation *checks* known expectations. Humans *explore* and find the unknowns. Most teams use both.

---

## 4. Static vs. Dynamic Testing

| | **Static Testing** | **Dynamic Testing** |
|---|---|---|
| **Is code executed?** | ❌ No | ✅ Yes |
| **What is examined** | Requirements, designs, code, test cases | The running application |
| **Techniques** | Reviews, walkthroughs, inspections, static code analysis (e.g., SonarQube) | Executing test cases, exploratory sessions, automated runs |
| **Finds** | Ambiguous requirements, missing rules, coding-standard violations | Failures in actual behaviour |

**Example:** reading a user story and noticing it never says what happens when a payment fails is **static testing**. Static testing is the cheapest way to find defects, because nothing has been built yet.

---

## 5. Black-Box vs. White-Box Testing

| | **Black-Box** | **White-Box** |
|---|---|---|
| **Knowledge of code** | None. Tester sees only inputs and outputs | Full. Tester designs tests from the code structure |
| **Usually done by** | QA testers, business users | Developers, SDETs |
| **Based on** | Requirements, specifications | Code paths, branches, conditions |
| **Techniques** | Equivalence partitioning, boundary values, decision tables (Chapter 7) | Statement coverage, branch coverage, path coverage |

**Grey-box testing** is a mix of the two. The tester knows *some* internals, such as database tables or API contracts, and uses that knowledge to design better black-box tests. For example, after placing an order through the UI, the tester checks the `orders` table to confirm the record was created correctly.

---

## 6. Testing Levels: Unit → Integration → System → Acceptance

Testing levels follow the order in which software is built: from the smallest piece up to the complete product in the customer's hands.

```text
   ┌──────────────────────────┐
   │   Acceptance Testing     │  ← Does it meet business needs? (users / business)
   ├──────────────────────────┤
   │     System Testing       │  ← Does the complete system work end to end? (QA)
   ├──────────────────────────┤
   │   Integration Testing    │  ← Do the components work together? (Dev + QA)
   ├──────────────────────────┤
   │      Unit Testing        │  ← Does each small piece work alone? (Developers)
   └──────────────────────────┘
```

### 6.1 Unit Testing
- **What:** tests the smallest testable part of the code (a function, method, or class) in isolation.
- **Who:** usually developers.
- **Example:** a function `calculateDiscount(price, percent)` returns `90` for `(100, 10)`.
- **Why it matters:** it is fast, cheap, and catches logic errors early.

### 6.2 Integration Testing
- **What:** tests the **interfaces and data flow** between components, modules, or systems.
- **Who:** developers and testers.
- **Example:** when the Cart service sends the order total to the Payment service, does the Payment service receive the correct amount and currency?
- **Common defects:** wrong data formats, missing fields, timeouts, mismatched API contracts.

### 6.3 System Testing
- **What:** tests the **complete, integrated application** against the requirements in an environment close to production.
- **Who:** the QA team. This is where most manual testers spend their time.
- **Example:** a user searches for a product, adds it to the cart, checks out, pays, and receives a confirmation email.

### 6.4 Acceptance Testing
- **What:** confirms that the system meets **business needs** and is ready for release.
- **Who:** the customer, business users, the product owner (often supported by QA).
- **Forms:** **UAT** (User Acceptance Testing), **Alpha** (internal users), **Beta** (real users in a limited release), and contract or regulatory acceptance.
- **Example:** the finance team confirms that invoices generated by the system match their accounting rules.

---

## 7. Testing Types by Purpose and Timing

### 7.1 Regression Testing
- **What:** re-running existing tests to confirm that **a change has not broken something that used to work**.
- **When:** after bug fixes, new features, configuration changes, or upgrades.
- **Example:** after a developer changes the discount code logic, you re-test the cart total, tax, and checkout.
- **Note:** regression suites grow over time, which makes them the **#1 candidate for automation**.

### 7.2 Smoke Testing
- **What:** a quick, **broad and shallow** check that a new build is stable enough for further testing. It is sometimes called a *build verification test*.
- **When:** right after a new build is deployed to the test environment.
- **Example:** Can the app launch? Can a user log in? Does the home page load? Can a product be added to the cart?
- **If it fails:** the build is rejected and returned to development. There is no point testing deeper.

### 7.3 Sanity Testing
- **What:** a quick, **narrow and deep** check of one specific area after a small change or bug fix.
- **When:** after a minor fix, to decide whether it is worth running a full regression.
- **Example:** a bug in the "Apply Coupon" button was fixed. You check only that coupons now apply correctly.

| | **Smoke** | **Sanity** |
|---|---|---|
| Scope | Broad and shallow (the whole app) | Narrow and deep (one area) |
| Trigger | New build | Small fix or change |
| Question | "Is this build testable?" | "Is this fix rational and working?" |

### 7.4 Exploratory Testing
- **What:** learning, test design, and execution happen **at the same time**. There are no pre-written scripts. The tester explores the application using curiosity and experience.
- **Usually** time-boxed (for example, a 60-minute session) and guided by a **charter**, such as *"Explore the checkout with unusual addresses to discover validation issues."*
- **Strength:** it finds defects that scripted tests miss.
- Covered in more depth in Chapters 2 and 7.

---

## 8. Non-Functional Testing Types

### 8.1 Performance Testing
Checks speed, responsiveness, and stability under a workload.

| Sub-type | Question |
|---|---|
| **Load testing** | Does it perform well under *expected* user load? |
| **Stress testing** | At what point does it break, *beyond* expected load? |
| **Spike testing** | Can it handle a *sudden* surge (for example, a flash sale)? |
| **Endurance / Soak** | Does it degrade over *long* periods (memory leaks)? |

*Example:* the product search returns results in under 2 seconds with 5,000 concurrent users.

### 8.2 Security Testing
Checks that the system protects data and functionality from unauthorised access.
- **Common checks:** authentication, authorisation (can user A see user B's orders?), session timeout, SQL injection, cross-site scripting (XSS), data encryption, password storage.
- *Example:* after logout, pressing the browser's Back button must not show the account page.

### 8.3 Usability Testing
Checks how **easy, intuitive, and pleasant** the system is to use.
- **Common checks:** clear labels, helpful error messages, consistent navigation, accessibility (screen readers, colour contrast, keyboard navigation).
- *Example:* a first-time user completes checkout without help in under 3 minutes.

### 8.4 Compatibility Testing
Checks that the system works across different **environments**.
- **Browsers:** Chrome, Firefox, Safari, Edge
- **Devices:** desktop, tablet, mobile
- **Operating systems:** Windows, macOS, Android, iOS
- **Screen sizes and resolutions,** network conditions
- *Example:* the "Place Order" button is visible and clickable on Safari on iPhone.

---

## 9. Putting It Together: One Activity, Many Labels

| Activity | Level | Characteristic | Execution | Purpose |
|---|---|---|---|---|
| Developer runs tests on a tax-calculation function | Unit | Functional | Automated | — |
| QA re-runs the checkout suite after a bug fix | System | Functional | Manual or Automated | Regression |
| QA checks that the Cart → Payment API passes the correct total | Integration | Functional | Automated | — |
| 5,000 virtual users search products | System | Non-functional (Performance) | Automated | — |
| Business users verify the invoice format before go-live | Acceptance | Functional | Manual | UAT |
| Tester explores checkout with odd addresses for 60 minutes | System | Functional | Manual | Exploratory |

---

## ⚠️ Common Beginner Mistakes

1. **Treating levels and types as alternatives:** "Should we do system testing *or* regression testing?" You usually do both, together.
2. **Confusing smoke and sanity.** Remember: smoke means *broad and shallow, new build*. Sanity means *narrow and deep, small fix*.
3. **Thinking automation means no manual testing.** Exploratory and usability testing need human judgement.
4. **Ignoring non-functional testing.** A checkout that works but takes 30 seconds will still lose customers.
5. **Forgetting static testing.** Reviewing requirements is testing too, and it is the cheapest place to find defects.

---

## 📝 Practice Exercise (≈ 8 minutes)

You are testing **ShopEasy**, an e-commerce web application. ShopEasy is the case study we will keep returning to, ending in the Chapter 10 capstone. For each feature, list the testing types that apply and give **one reason** for each.

| Feature | Testing types that apply | Why |
|---|---|---|
| Login | | |
| Product search | | |
| Checkout | | |
| Payment | | |
| Order confirmation | | |

<details>
<summary>✅ Model answer (click to expand)</summary>

| Feature | Testing types that apply | Why |
|---|---|---|
| **Login** | Functional, Security, Smoke, Regression, Compatibility, Usability | Core gateway feature. Credentials need protection (brute force, SQL injection, session handling). It is the first thing a smoke test checks. It must work on all browsers. Error messages must be clear. |
| **Product search** | Functional, Performance, Usability, Regression, Exploratory | Results must be correct and relevant. Search gets heavy traffic, so it must be fast under load. Filters and sorting must be intuitive. Exploratory testing with odd inputs (typos, special characters, very long strings) finds hidden issues. |
| **Checkout** | Functional, Integration, System (end-to-end), Usability, Compatibility, Regression | Combines cart, address, tax, shipping, and payment modules, so integration testing is essential. It is a critical revenue flow, so it must be in the regression suite. Many users check out on mobile, so compatibility matters. |
| **Payment** | Functional, Integration (payment gateway), Security, Performance, Regression | Handles money and card data. Security is critical (encryption, PCI-DSS compliance). Integration with a third-party gateway (authorisation, decline, timeout). Must not double-charge under load. |
| **Order confirmation** | Functional, Integration (email/SMS service), System, Usability | The order must be created with the correct details. The confirmation email or SMS is sent by an external service, so integration testing applies. The confirmation page must clearly show the order number and summary. |

**Notice:** every feature gets **several** types. That is the key lesson of this chapter.

</details>

---

## 🧠 Quick Check

1. A tester reviews a requirements document and finds a contradiction. Is this static or dynamic testing?
2. A new build arrives and you spend 15 minutes checking that the main pages load and login works. What type of testing is this?
3. True or false: "Regression" is a testing level.
4. Which testing level is usually done by developers?
5. Checking that the website works on Safari and Firefox is which type of testing?

<details>
<summary>Answers</summary>

1. **Static.** No code was executed.
2. **Smoke testing.**
3. **False.** Regression is a type or purpose of testing, and it can happen at any level.
4. **Unit testing.**
5. **Compatibility testing** (non-functional).

</details>

---

## 🔑 Key Takeaways

- **Level = where** you test (unit, integration, system, acceptance). **Type = what** you test (functional, performance, security…).
- One testing activity carries several labels, for example *System + Functional + Regression*.
- Functional testing checks **what** the system does. Non-functional testing checks **how well** it does it.
- Smoke means *broad and shallow*. Sanity means *narrow and deep*.
- Regression protects what already works, and it is the best candidate for automation.

---

**Next:** [Chapter 2 — Test Strategies →](02-test-strategies.md). Now that you know the testing types, you will learn how to decide **which ones to use, where, when, and how much.**

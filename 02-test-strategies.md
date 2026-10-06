# Chapter 2 — Test Strategies

> **Software Testing Foundation · Module: Test Design & Strategy**
> **Chapter 2 of 10** · ⏱️ **Time: 25 minutes** (15 min reading + 10 min practice)
> **Previous:** [← Chapter 1 — Testing Types](01-testing-types.md) · **Next:** [Chapter 3 — Use Cases →](03-use-cases.md)

---

## Learning Objectives

By the end of this chapter you will be able to:

1. Explain what a test strategy is and how it differs from a test approach and a test plan.
2. Apply **risk-based** and **requirement-based** thinking to decide what to test first.
3. Describe regression, automation, and exploratory testing strategies.
4. Explain **shift-left**, **shift-right**, and the **test pyramid**.
5. Define **test coverage** and **entry/exit criteria** for a test phase.

---

## Where This Chapter Fits

Chapter 1 gave you the *vocabulary* (the testing types). This chapter gives you the *decision-making*: there is never enough time to test everything, so how do you choose? Everything you design later (scenarios, test cases, estimates) should follow from the strategy.

---

## 1. What Is a Test Strategy?

A **test strategy** is a high-level description of **how testing will be done** to give confidence in the quality of a product. It answers five questions:

| Question | Example answer |
|---|---|
| **What** will we test? | Checkout, payments, login. Not the legacy admin reports. |
| **Where** will we test? | Unit tests in CI, system tests in the QA environment, UAT in staging. |
| **When** will we test? | Every sprint; regression before each release. |
| **How** will we test? | Automated regression, manual exploratory, performance test before Black Friday. |
| **How deeply?** | Payments: deep, with many negative cases. Static "About Us" page: light check only. |

### The Core Mental Model

```text
Requirements → Risks → Test approach → Test coverage → Execution → Evidence
```

| Step | Meaning |
|---|---|
| **Requirements** | What should the system do? (stories, use cases, specifications) |
| **Risks** | What could go wrong, and how bad would it be? |
| **Test approach** | Which types, levels, and techniques will reduce those risks? |
| **Test coverage** | How much of the requirements and risks do our tests address? |
| **Execution** | Run the tests and log the results and defects. |
| **Evidence** | Reports, metrics, and sign-off that let stakeholders decide whether to release. |

> 💡 Testing does not *prove* software is bug-free. It provides **evidence** that helps people make a **release decision**.

---

## 2. Test Approach vs. Test Strategy vs. Test Plan

These three terms get mixed up all the time. Here is the difference:

| | **Test Strategy** | **Test Plan** | **Test Approach** |
|---|---|---|---|
| **Level** | Organisation or product level | Project or release level | Applied within the plan to a feature or area |
| **Answers** | *What are our general rules and principles for testing?* | *Who tests what, when, with which resources, for this release?* | *How exactly will we test this particular thing?* |
| **Changes** | Rarely | Every project or release | Per feature or risk |
| **Example content** | "All critical flows must have automated regression. We follow the test pyramid." | "Release 3.2: 2 testers, 3 weeks, scope = checkout redesign, exit criteria = …" | "Checkout: risk-based. Automate the happy paths, explore payment edge cases, run a load test at 2× peak." |

**Analogy:** the *strategy* is a country's road safety policy. The *plan* is your specific road trip itinerary. The *approach* is how you drive a particular dangerous mountain pass.

> In many small companies, the strategy and plan are combined into one document. That is fine. What matters is that the thinking happens.

---

## 3. Risk-Based Testing

**Risk = Likelihood × Impact**

- **Likelihood:** how probable is a failure? Think about new code, complex logic, many integrations, an inexperienced team, or frequent changes.
- **Impact:** how bad would a failure be? Think about financial loss, legal or compliance issues, safety, reputation, or the number of users affected.

### Risk Matrix

```text
                  IMPACT →
              Low        Medium       High
         ┌──────────┬──────────┬──────────┐
  High   │  Medium  │   High   │ CRITICAL │
L        ├──────────┼──────────┼──────────┤
I Medium │   Low    │  Medium  │   High   │
K        ├──────────┼──────────┼──────────┤
E  Low   │   Low    │   Low    │  Medium  │
         └──────────┴──────────┴──────────┘
```

**How to use it:**
1. List the features or areas.
2. Rate the likelihood and impact of each (1–3 or Low/Medium/High).
3. Test **critical and high-risk** areas **first** and **most deeply**.
4. Test low-risk areas lightly, or later.

*Example:* in an e-commerce app, **payment** is high impact (money) and high likelihood (third-party integration), so it is critical. The **"About Us" page** is low on both, so a quick check is enough.

---

## 4. Requirement-Based Testing

Test cases are derived **directly from the requirements** (user stories, acceptance criteria, use cases), so that **every requirement is tested at least once**.

- **Main tool:** the **Requirements Traceability Matrix (RTM)**, a table that maps each requirement to its test cases.

| Requirement ID | Requirement | Test Cases | Status |
|---|---|---|---|
| REQ-01 | User can log in with a valid email and password | TC_LOGIN_001, TC_LOGIN_002 | Passed |
| REQ-02 | Account locks after 5 failed attempts | TC_LOGIN_007 | Failed |
| REQ-03 | User can reset password | — | ⚠️ Not covered |

The RTM shows gaps immediately (REQ-03 above). You will build traceability in Chapter 7.

> **Risk-based** decides *priority and depth*. **Requirement-based** makes sure *nothing is forgotten*. Good strategies use **both**.

---

## 5. Regression Strategy

Every change can break something. A regression strategy decides **what to re-test, and when**.

| Option | When to use |
|---|---|
| **Retest all** | Major releases, or high-risk platform upgrades. Most expensive. |
| **Selective (impact-based)** | Re-test the areas affected by the change and their neighbours. Most common. |
| **Priority-based** | Run P1 (critical) tests every build, P2 every release, and P3 occasionally. |
| **Automated nightly suite** | A stable, repeated core suite run in CI/CD. |

A good regression suite is **maintained**: obsolete tests are removed, new tests are added for every fixed defect, and the suite is tagged by priority and area.

---

## 6. Automation Strategy

Not everything should be automated. Use these criteria:

| ✅ Good automation candidates | ❌ Poor automation candidates |
|---|---|
| Repeated often (regression, smoke) | Run once or rarely |
| Stable features and UI | Features still changing every sprint |
| Data-driven (same steps, many data sets) | Needs human judgement (look and feel, usability) |
| Critical business flows (login, checkout) | Exploratory testing |
| Tedious or error-prone for humans (large calculations) | Very complex setup with little benefit |
| Performance and load tests (impossible manually) | CAPTCHA, physical devices, visual subjectivity |

**Questions to ask:** *How often will this run? How stable is it? What does a bug here cost? How much effort is it to build and maintain?*

---

## 7. Exploratory Testing Strategy

Exploratory testing is most useful when it is **structured**, using **Session-Based Test Management (SBTM)**:

- **Charter:** a mission statement, for example *"Explore payment with different card types and network conditions to discover error-handling issues."*
- **Time-box:** 45–90 minutes of focused, uninterrupted testing.
- **Notes:** what you tested, the bugs you found, questions, and ideas.
- **Debrief:** share the findings with the team.

**Use exploratory testing for:** new features, complex areas, areas with vague requirements, high-risk areas (*after* the scripted tests pass), and to complement automation.

---

## 8. Shift-Left and Shift-Right Testing

```text
  Requirements → Design → Code → Test → Release → Production
  ◄──────── SHIFT LEFT                      SHIFT RIGHT ────────►
  (test earlier)                         (test in/after production)
```

### Shift-Left
Move testing **earlier** in the life cycle. A defect found in requirements costs much less to fix than one found in production.
- QA reviews user stories and acceptance criteria (static testing).
- Testers join refinement meetings and ask "what if…?" questions.
- Developers write unit tests, and automated tests run on every commit.
- BDD (Given/When/Then, see Chapter 5) agrees on the tests *before* coding.

### Shift-Right
Continue testing **in or after production** to learn from real usage.
- Monitoring, logging, and alerting
- A/B testing, feature flags, and canary releases (release to 5% of users first)
- Synthetic monitoring (automated scripts that check production regularly)
- Analysing real user behaviour and crash reports

> Shift-left **prevents** defects. Shift-right **detects** what slipped through in the real world. Mature teams do **both**.

---

## 9. The Test Pyramid

```text
              /\
             /  \        UI / End-to-End tests
            / UI \       • Few • Slow • Expensive • Brittle
           /──────\
          /  API /  \     Service / Integration tests
         / Service   \    • More • Faster • Stable
        /─────────────\
       /     Unit      \  Unit tests
      /                 \ • Many • Very fast • Cheap
     /───────────────────\
```

**Message:** have **many** fast, cheap unit tests at the bottom, **fewer** integration/API tests in the middle, and **only a few** slow UI end-to-end tests at the top.

**The anti-pattern ("ice-cream cone"):** mostly manual and UI tests with few unit tests. This gives a slow, brittle, expensive test suite.

> Manual exploratory testing sits *on top* of the pyramid, as a "cloud" of human investigation that complements the automated layers.

---

## 10. Test Coverage

**Test coverage** measures **how much of something** your tests exercise.

| Coverage type | Formula / meaning |
|---|---|
| **Requirement coverage** | (Requirements with ≥1 test ÷ total requirements) × 100 |
| **Risk coverage** | Percentage of identified high risks that have tests |
| **Code coverage** (white-box) | Percentage of statements or branches executed by tests |
| **Test execution coverage** | (Tests executed ÷ tests planned) × 100 |
| **Platform coverage** | Browsers, devices, and OS combinations tested |

*Example:* 45 of 50 requirements have tests, so requirement coverage = **90%**.

> ⚠️ **100% coverage does not mean 0 bugs.** Coverage tells you what you *touched*, not how *well* you tested it.

---

## 11. Entry and Exit Criteria

**Entry criteria** are the conditions that must be true **before** testing starts. **Exit criteria** are the conditions that must be true **before** testing can be declared complete.

| Entry Criteria (example: System Test) | Exit Criteria (example: System Test) |
|---|---|
| Requirements/stories are approved | 100% of planned P1 test cases executed |
| Test environment is ready and stable | ≥ 95% pass rate overall |
| Build has passed the smoke test | 0 open Critical/High defects |
| Test data is available | All Medium defects reviewed and fixed or deferred with sign-off |
| Test cases are reviewed and approved | Requirement coverage = 100% |
| | Test summary report delivered |

Without exit criteria, testing either never ends or ends when the deadline arrives, whatever the quality.

---

## ⚠️ Common Beginner Mistakes

1. **Testing everything equally.** Spend more effort where the risk is higher.
2. **Automating unstable features.** The scripts break every sprint.
3. **Having no exit criteria.** Then "done" just means "out of time".
4. **Treating exploratory testing as random clicking.** It needs a charter and a time-box.
5. **Inverting the pyramid.** Relying only on UI tests.

---

## 📝 Practice Exercise (≈ 10 minutes)

**Scenario:** you are the QA lead for **SafeBank**, an online banking application with these features:

- Login with OTP (two-factor authentication)
- View account balance and statements
- Fund transfer (own accounts and third-party)
- Bill payments
- Update profile (address, phone)
- Loan EMI calculator (informational)
- Branch/ATM locator
- Customer support chat

Identify:
1. **High-risk areas** (use likelihood × impact)
2. **Testing priorities** (P1 / P2 / P3)
3. **Tests that should be automated**
4. **Tests that require exploratory testing**

<details>
<summary>✅ Model answer (click to expand)</summary>

**1. High-risk areas**

| Feature | Likelihood | Impact | Risk |
|---|---|---|---|
| Fund transfer | High (complex rules, integrations) | High (money, regulation) | **Critical** |
| Login with OTP | Medium | High (account takeover) | **High** |
| Bill payments | Medium | High (money, third-party billers) | **High** |
| View balance/statements | Medium | High (wrong balance destroys trust) | **High** |
| Update profile | Medium | Medium (phone change can enable fraud) | **Medium–High** |
| Support chat | Medium | Low | Low–Medium |
| Loan EMI calculator | Low | Medium (wrong figures mislead) | Medium |
| Branch/ATM locator | Low | Low | **Low** |

**2. Priorities**
- **P1:** Fund transfer, Login/OTP, Bill payments, Balance/Statements. Deep functional, negative, security, and integration testing.
- **P2:** Update profile (especially changing the phone or email, which needs OTP re-verification), Loan EMI calculator (boundary values).
- **P3:** Support chat, Branch locator. Light functional and compatibility checks.

**3. Automate**
- Login (valid, invalid, locked account), regression for fund transfer happy paths, balance calculations after transactions.
- Data-driven transfer tests (many amounts and limits), EMI calculator formulas.
- API tests for transfer and bill-payment services, the smoke suite for every build.
- Performance tests on login and transfer at salary-day peak loads.

**4. Exploratory testing**
- Fund transfer: double-clicking Submit, browser Back after transfer, two tabs at once, session timeout mid-transfer, network drop.
- OTP: expired OTP, reused OTP, resend limits, an OTP arriving late.
- Statements: unusual date ranges, very large statements, PDF download on mobile.
- Security-minded exploration: changing the account ID in the URL to see someone else's account.

</details>

---

## 🧠 Quick Check

1. Risk = ______ × ______.
2. Which document is usually written once for a product and changes rarely: the strategy or the plan?
3. Why should the test pyramid have more unit tests than UI tests?
4. Give one example each of shift-left and shift-right activity.
5. "The build has passed the smoke test" is an example of an ______ criterion.

<details>
<summary>Answers</summary>

1. Likelihood × Impact.
2. The **test strategy**.
3. Unit tests are faster, cheaper, more stable, and pinpoint failures precisely.
4. Shift-left: reviewing user stories before coding. Shift-right: monitoring production or canary releases.
5. **Entry** criterion.

</details>

---

## 🔑 Key Takeaways

- A strategy turns **requirements → risks → approach → coverage → execution → evidence**.
- **Strategy** = general principles. **Plan** = this release's who, what, and when. **Approach** = how to test a specific area.
- Use **risk-based** testing to set priority and **requirement-based** testing to make sure nothing is missed.
- Automate what is **repetitive, stable, and critical**. Explore what is **new, complex, and risky**.
- Follow the **test pyramid**. Shift **left** to prevent defects and **right** to learn from production.
- **Entry/exit criteria** turn "are we done?" into an objective decision.

---

**Next:** [Chapter 3 — Use Cases →](03-use-cases.md). A strategy needs requirements to work from. Next you will learn to read and write **use cases**, one of the main ways requirements describe user-system interaction.

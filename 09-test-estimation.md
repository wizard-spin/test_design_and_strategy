# Chapter 9 — Test Estimation

> **Software Testing Foundation · Module: Test Design & Strategy**
> **Chapter 9 of 10** · ⏱️ **Time: 20 minutes** (11 min reading + 9 min practice)
> **Previous:** [← Chapter 8 — Defect Life Cycle](08-defect-life-cycle.md) · **Next:** [Chapter 10 — Capstone: E-Commerce Checkout →](10-capstone-ecommerce-checkout.md)

---

## Learning Objectives

By the end of this chapter you will be able to:

1. Explain why testing needs estimation.
2. List the factors that contribute to testing effort.
3. Apply five estimation approaches: **expert judgement, WBS, test-case-based, three-point, and historical**.
4. Calculate a three-point estimate using **E = (O + 4M + P) / 6**.
5. State your **assumptions** and convert effort into a **schedule**.

---

## Where This Chapter Fits

You have now seen the full testing workflow: choosing **types** (Ch. 1) and **strategy** (Ch. 2), analysing **requirements** (Ch. 3–5), designing **scenarios** and **test cases** (Ch. 6–7), and managing **defects** (Ch. 8). Every one of those activities takes time. Sooner or later your lead will ask: *"How long will testing take?"* This chapter teaches you how to answer with something better than a guess.

---

## 1. Why Testing Needs Estimation

- **Planning:** the project manager needs dates for the release plan.
- **Resourcing:** how many testers, devices, and environments are needed?
- **Negotiation:** if time is fixed, what scope can we test? Which risks will remain untested?
- **Credibility:** testing is often squeezed at the end. A reasoned estimate protects quality.
- **Tracking:** comparing estimate vs. actual helps you estimate better next time.

> 💡 An estimate is **not a promise**. It is a **best prediction based on stated assumptions**. When an assumption changes, the estimate changes too.

---

## 2. What Contributes to Testing Effort?

| Factor | How it affects effort | Example |
|---|---|---|
| **Scope** | More features/modules = more testing | Login only vs. full checkout |
| **Complexity** | Complex logic, integrations, and rules need more scenarios | Payment with 3 gateways vs. a static page |
| **Number of requirements** | Each requirement/AC needs coverage | 12 stories × ~6 AC each |
| **Number of test cases** | Design, review, and execution time scale with count | 40 vs. 400 test cases |
| **Test data preparation** | Accounts, products, cards, coupons, masked production data | Creating 20 users in different states |
| **Environment setup** | Builds, configs, devices, browsers, third-party sandboxes | Payment gateway sandbox access |
| **Automation effort** | Building, debugging, and maintaining scripts | ~1–3 hours per automated test case initially |
| **Regression effort** | Re-running existing tests every cycle/release | 2 regression cycles × 1 day |
| **Defect retesting** | Logging, reproducing, retesting, and re-running regression | Often 15–30% of execution effort |
| **Team capacity** | Number of testers, skill, availability, holidays, meetings | 2 testers × 6 productive hours/day |
| **Risk and uncertainty** | Unclear requirements, new technology, unstable builds | Add contingency (buffer) |

> ⚠️ **Productive hours ≠ working hours.** A tester in an 8-hour day typically has **~6 productive testing hours** after meetings, stand-ups, emails, and environment issues.

---

## 3. Estimation Approaches

Here they are, from simplest to most data-driven.

### 3.1 Expert Judgement
Ask experienced people (QA lead, senior testers, developers) to estimate based on their experience.
- **Variants:** *Wideband Delphi* (experts estimate independently, discuss differences, re-estimate) and *Planning Poker* in Agile teams (estimating in story points).
- ✅ Fast; uses real-world knowledge. ❌ Subjective; depends on the expert's familiarity.

### 3.2 Work Breakdown Structure (WBS)
Break testing into **small tasks**, estimate each, and add them up. Small tasks are easier to estimate accurately than one big task.

```text
Test Login Module
├── 1. Test planning & requirement analysis ......... 3 h
├── 2. Test design
│   ├── 2.1 Write scenarios ......................... 2 h
│   ├── 2.2 Write test cases ........................ 10 h
│   └── 2.3 Review test cases ....................... 2 h
├── 3. Test data & environment setup ................ 4 h
├── 4. Test execution (cycle 1) ..................... 8 h
├── 5. Defect reporting & retesting ................. 4 h
├── 6. Regression (cycle 2) ......................... 4 h
└── 7. Test summary report .......................... 2 h
                                           TOTAL ≈ 39 h
```

- ✅ Thorough; reveals forgotten tasks (test data! reporting!). ❌ Takes time; needs a clear scope.

### 3.3 Test-Case-Based Estimation
Estimate the number of test cases, classify them by complexity, and multiply by the average time per test case.

| Complexity | Count | Design time / TC | Execution time / TC | Total (h) |
|---|---|---|---|---|
| Simple | 20 | 10 min | 5 min | 20 × 15 min = **5.0** |
| Medium | 15 | 20 min | 10 min | 15 × 30 min = **7.5** |
| Complex | 5 | 40 min | 20 min | 5 × 60 min = **5.0** |
| **Total** | **40** | | | **17.5 h** (design + 1 execution cycle) |

Then add the overheads: setup, defects, regression, reporting.
- ✅ Objective, easy to explain. ❌ Needs a reasonable test-case count up front (use scenarios from Ch. 6 × ~2).

### 3.4 Three-Point Estimation
Uncertainty is real, so estimate three values for each task (or for the total):

- Optimistic estimate: **O** (everything goes well)
- Most likely estimate: **M** (realistic)
- Pessimistic estimate: **P** (things go wrong: unstable builds, many defects)

Then calculate the **weighted average (PERT formula)**:

```text
E = (O + 4M + P) / 6
```

Optionally, the **standard deviation** shows the uncertainty:

```text
SD = (P − O) / 6
```

**Worked example:** test execution for the checkout module.
- O = 20 h, M = 30 h, P = 52 h
- E = (20 + 4×30 + 52) / 6 = (20 + 120 + 52) / 6 = 192 / 6 = **32 hours**
- SD = (52 − 20) / 6 = 32 / 6 ≈ **5.3 hours**
- You could communicate it as: *"About 32 hours, likely between ~27 and ~37 hours (E ± 1 SD)."*

- ✅ Builds risk into the number; easy to justify. ❌ Still relies on judgement for O, M, P.

### 3.5 Historical Estimation
Use **actual data from past similar projects**.

*Example:* in the last 3 releases, the team averaged **0.9 hours of total testing effort per test case** (design + execution + defects + regression). The new feature has ~60 test cases, so the estimate is **60 × 0.9 = 54 hours**, adjusted for differences (new payment gateway, so +15%: 54 × 1.15 ≈ **62 hours**).

- ✅ Grounded in reality; improves over time. ❌ Requires good record-keeping, and past projects must be comparable.

### Comparing the Approaches

| Approach | Best when | Main risk |
|---|---|---|
| Expert judgement | Early, quick, little data | Bias, optimism |
| WBS | Scope is clear | Forgetting tasks |
| Test-case-based | Scenarios/test cases are known | Wrong TC count |
| Three-point | Uncertainty is high | Garbage-in, garbage-out |
| Historical | Similar past projects with data | Projects not comparable |

> In practice, **combine them**: build a WBS, estimate each task with three-point, and sanity-check against history and expert opinion.

---

## 4. From Effort to Schedule

**Effort** (person-hours) is not the same as **duration** (calendar days).

```text
Duration (days) = Total effort (h) ÷ (Number of testers × Productive hours per tester per day)
```

*Example:* 60 h ÷ (2 testers × 6 h/day) = 60 ÷ 12 = **5 working days**.

Watch out for things that can't be parallelised (waiting for builds or fixes), holidays, and dependencies.

---

## 5. Always State Your Assumptions

An estimate without assumptions can't be defended. Typical assumptions:

- Requirements are stable; changes will be re-estimated.
- The test environment is available and stable from Day 1.
- Builds pass the smoke test.
- Defects are fixed within 1 day (for Critical/High).
- Test data will be provided by the dev team / created by QA.
- 2 testers, 6 productive hours per day, no planned leave.
- Payment sandbox access available.

Also list what is **out of scope**, for example: *"Performance and security penetration testing are not included."*

---

## ⚠️ Common Beginner Mistakes

1. **Estimating only execution time.** Forgetting design, test data, setup, defects, regression, and reporting.
2. **Assuming 8 productive hours a day.**
3. **No contingency** for unstable builds or a high defect count.
4. **Giving one number with no assumptions.**
5. **Never comparing estimate vs. actual**, so estimates never improve.

---

## 📝 Practice Exercise (≈ 9 minutes)

Estimate testing effort for a **login module**:

- 20 functional scenarios
- 40 test cases
- 5 browsers
- Regression required
- 2 testers
- Some automation required

Calculate the effort, convert it to a schedule, and **identify your assumptions**.

<details>
<summary>✅ Model answer (click to expand)</summary>

**Assumptions**
1. Average design time: 15 min per test case; average execution: 12 min per test case on the primary browser (Chrome).
2. Not all 40 TCs run on all 5 browsers. A **cross-browser subset of 15 UI-relevant TCs** runs on the other 4 browsers at ~8 min each (faster on repeat).
3. **10 test cases** (smoke + core regression) are automated at ~1.5 h each (build + debug).
4. Defect logging and retesting ≈ 25% of execution effort.
5. 2 regression cycles of ~4 h each (manual portion; the automated portion runs unattended).
6. Management, meetings, and reporting ≈ 10% overhead.
7. 2 testers, 6 productive hours/day each. The environment is stable.

**WBS + test-case-based calculation**

| Task | Calculation | Effort (h) |
|---|---|---|
| Requirement analysis & scenario review | — | 2 |
| Test case design | 40 × 15 min = 600 min | 10 |
| Test case review | — | 2 |
| Test data & environment setup (5 browsers) | — | 4 |
| Execution: primary browser | 40 × 12 min = 480 min | 8 |
| Execution: 4 other browsers | 15 × 4 × 8 min = 480 min | 8 |
| Defect logging & retesting | 25% × (8 + 8) | 4 |
| Automation (10 TCs) | 10 × 1.5 h | 15 |
| Regression | 2 cycles × 4 h | 8 |
| **Subtotal** | | **61** |
| Management & reporting (10%) | 10% × 61 ≈ 6.1 | 6 |
| **Most likely total (M)** | | **≈ 67 h** |

**Three-point on the total**
- O = 55 h (few defects, stable builds)
- M = 67 h
- P = 95 h (unstable builds, many defects, automation flakiness)
- **E = (55 + 4×67 + 95) / 6 = (55 + 268 + 95) / 6 = 418 / 6 ≈ 69.7 h → ~70 h**
- SD = (95 − 55) / 6 ≈ 6.7 h

**Schedule**
- Capacity = 2 testers × 6 h = 12 h/day
- Duration = 70 ÷ 12 ≈ **5.8 → 6 working days**

**How to communicate it:**
> "Testing the login module will take about **70 person-hours (~6 working days with 2 testers)**, likely between 63 and 76 hours. This assumes a stable environment, 15 cross-browser test cases, 10 automated tests, and 2 regression cycles. If all 40 tests must run on all 5 browsers, add ~13 hours."

*(How the ~13 h is worked out: running all 40 TCs × 4 extra browsers × 8 min = 1,280 min ≈ 21.3 h, minus the 8 h already planned for the 15-TC subset ≈ 13.3 h extra. A good tester shows the **impact of changing an assumption**.)*

> ✏️ Your numbers will differ, and that's fine. Estimates are judged on **completeness of tasks, clear assumptions, and sound arithmetic**, not on matching a "correct" number.

</details>

---

## 🧠 Quick Check

1. Write the three-point (PERT) formula.
2. O = 10, M = 16, P = 28. What is E?
3. Name four factors that contribute to testing effort besides executing test cases.
4. 48 hours of effort, 2 testers, 6 productive h/day. How many days?
5. Why must an estimate include assumptions?

<details>
<summary>Answers</summary>

1. **E = (O + 4M + P) / 6**
2. (10 + 64 + 28) / 6 = 102 / 6 = **17 hours**
3. Any four of: test design, test data preparation, environment setup, automation, regression, defect retesting, reporting, meetings, risk/uncertainty.
4. 48 ÷ 12 = **4 days**
5. So stakeholders know **what the number depends on**, and the estimate can be revised when an assumption changes.

</details>

---

## 🔑 Key Takeaways

- Estimation supports planning, resourcing, and negotiation. It is a **prediction with assumptions**, not a promise.
- Effort comes from **scope, complexity, requirements, test cases, data, environments, automation, regression, defects, capacity, and risk**.
- Use **WBS** to avoid forgetting tasks, **test-case-based** for objectivity, **three-point** for uncertainty, and **historical** data to stay realistic.
- **E = (O + 4M + P) / 6**
- Convert **effort → duration** using productive hours and team size, and always **state your assumptions**.

---

**Next:** [Chapter 10 — Capstone: E-Commerce Checkout →](10-capstone-ecommerce-checkout.md). Time to put **everything** together on one realistic feature.

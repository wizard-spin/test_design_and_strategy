## Software Testing Foundation — Module: Test Design & Strategy

**Module objective:**  
Enable learners to translate requirements into a structured testing approach, identify appropriate testing types, design test scenarios and test cases, manage defects, and estimate testing effort.

### Recommended learning sequence

| Order | Topic | What the learner will understand |
|---|---|---|
| 1 | **Testing Types** | Different dimensions of testing and when each type is appropriate |
| 2 | **Test Strategies** | How to decide what, where, when, and how deeply to test |
| 3 | **Use Cases** | How user-system interactions become testable flows |
| 4 | **User Stories** | How Agile requirements are expressed from the user's perspective |
| 5 | **Acceptance Criteria** | How to determine whether a user story is complete and acceptable |
| 6 | **Test Scenarios** | How to identify meaningful situations that need to be tested |
| 7 | **Test Case Design** | How to convert scenarios into detailed executable test cases |
| 8 | **Defect Life Cycle** | How defects move from discovery through resolution and closure |
| 9 | **Test Estimation** | How to estimate testing effort, time, and resources |

---

# Concept 1: Testing Types

### What to Cover
- What is a testing type?
- Functional vs. non-functional testing
- Manual vs. automated testing
- Static vs. dynamic testing
- Black-box vs. white-box testing
- Unit testing
- Integration testing
- System testing
- Acceptance testing
- Regression testing
- Smoke testing
- Sanity testing
- Exploratory testing
- Performance testing
- Security testing
- Usability testing
- Compatibility testing

### Key distinction to teach

> **Testing level answers:** *Where are we testing?*  
> **Testing type answers:** *What characteristic are we testing?*

For example:

**System + Functional + Regression testing**

These are not competing categories; they describe different dimensions of the testing activity.

### Practice
Given an e-commerce application, identify which testing types would apply to:
- Login
- Product search
- Checkout
- Payment
- Order confirmation

---

# Concept 2: Test Strategies

### What to Cover
- What is a test strategy?
- Test approach vs. test strategy vs. test plan
- Risk-based testing
- Requirement-based testing
- Regression strategy
- Automation strategy
- Exploratory testing strategy
- Shift-left testing
- Shift-right testing
- Test pyramid
- Test coverage
- Entry and exit criteria

### Core mental model

**Requirements → Risks → Test approach → Test coverage → Execution → Evidence**

### Practice
Given a banking application, identify:
- High-risk areas
- Testing priorities
- Tests that should be automated
- Tests that require exploratory testing

---

# Concept 3: Use Cases

### What to Cover
- What is a use case?
- Actor
- System
- Preconditions
- Trigger
- Main success flow
- Alternate flow
- Exception flow
- Postconditions
- Use case vs. test case

### Example

**Use Case:** Withdraw cash from an ATM

**Actor:** Customer

**Precondition:** Customer has a valid card and sufficient balance.

**Main flow:**
1. Insert card
2. Enter PIN
3. Select withdrawal
4. Enter amount
5. System validates balance
6. ATM dispenses cash
7. Account is debited

From this one use case, testers can derive many scenarios.

### Practice
Create a use case for:
- Login
- Online shopping
- Booking a flight

---

# Concept 4: User Stories

### What to Cover
- What is a user story?
- Agile requirement structure
- User / action / value
- Story format
- INVEST principles
- Epic vs. feature vs. user story
- User story vs. use case

### Standard format

> **As a** [user]  
> **I want** [capability]  
> **So that** [business value]

Example:

> As a customer, I want to reset my password so that I can regain access to my account.

### Practice
Convert requirements into user stories.

---

# Concept 5: Acceptance Criteria

### What to Cover
- What is acceptance criteria?
- Why acceptance criteria matters to testers
- Functional acceptance criteria
- Business rules
- Boundary conditions
- Positive and negative conditions
- Given / When / Then
- Definition of Done vs. Acceptance Criteria

### Example

**User Story:** Password reset

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

### Key insight

> **Acceptance criteria defines what must be true for the story to be accepted.**

It therefore becomes a major input for test design.

---

# Concept 6: Test Scenarios

### What to Cover
- What is a test scenario?
- Scenario vs. requirement
- Scenario vs. test case
- Positive scenarios
- Negative scenarios
- Boundary scenarios
- Alternate flows
- Exception scenarios
- End-to-end scenarios
- Scenario identification techniques

### Example

**Requirement:** User can transfer money between accounts.

Possible scenarios:

1. Transfer valid amount.
2. Transfer amount exceeding balance.
3. Transfer zero amount.
4. Transfer negative amount.
5. Transfer to invalid account.
6. Transfer above daily limit.
7. Network failure during transfer.
8. Session expires during transfer.

### Core distinction

> **Test scenario = What should we test?**  
> **Test case = How exactly will we test it?**

---

# Concept 7: Test Case Design

This should be the **largest topic in the module** because it brings the previous concepts together.

### What to Cover
- What is a test case?
- Test case structure
- Preconditions
- Test data
- Steps
- Expected result
- Actual result
- Pass/Fail
- Traceability
- Positive test cases
- Negative test cases

### Test case structure

| Field | Example |
|---|---|
| Test Case ID | TC_LOGIN_001 |
| Scenario | Valid login |
| Preconditions | Registered user exists |
| Test Data | Valid username/password |
| Steps | Enter credentials → Click Login |
| Expected Result | User reaches dashboard |
| Actual Result | — |
| Status | Not Executed |

### Test design techniques

Introduce these after basic test cases:

- Equivalence Partitioning
- Boundary Value Analysis
- Decision Table Testing
- State Transition Testing
- Pairwise Testing
- Error Guessing
- Exploratory Testing

### Example: Age field

Requirement:

> User must be between 18 and 60 years old.

Instead of testing every possible age:

**Equivalence partitions**

- `<18` → Invalid
- `18–60` → Valid
- `>60` → Invalid

**Boundary values**

- 17
- 18
- 19
- 59
- 60
- 61

This teaches learners **why good test design is more important than simply writing more test cases.**

---

# Concept 8: Defect Life Cycle

### What to Cover
- What is a defect?
- Error vs. defect vs. failure
- Defect reporting
- Defect severity
- Defect priority
- Defect states
- Defect ownership
- Retesting
- Regression testing
- Defect closure

### Typical lifecycle

**New → Assigned → Open → Fixed → Retest → Verified → Closed**

With possible alternate paths:

**Rejected**

**Duplicate**

**Deferred**

**Reopened**

### Severity vs. Priority

This deserves a dedicated exercise.

> **Severity:** How badly does the defect affect the system?

> **Priority:** How urgently should we fix it?

Example:

A typo on the homepage may have **low severity but high priority** if it is visible to millions of customers.

---

# Concept 9: Test Estimation

### What to Cover
- Why testing needs estimation
- What contributes to testing effort?
- Scope
- Complexity
- Number of requirements
- Number of test cases
- Test data preparation
- Environment setup
- Automation effort
- Regression effort
- Defect retesting
- Team capacity
- Risk and uncertainty

### Estimation approaches

Introduce progressively:

1. **Expert judgment**
2. **Work Breakdown Structure**
3. **Test-case-based estimation**
4. **Three-point estimation**
5. **Historical estimation**

### Three-point estimation

Use:

- Optimistic estimate — **O**
- Most likely estimate — **M**
- Pessimistic estimate — **P**

Then introduce:

\[
E = \frac{O + 4M + P}{6}
\]

### Practice

Estimate testing effort for a login module:

- 20 functional scenarios
- 40 test cases
- 5 browsers
- Regression required
- 2 testers
- Some automation required

Learners calculate effort and identify assumptions.

---

# Final Integrated Exercise

The module should culminate in **one realistic case study**, rather than isolated exercises.

### Case Study: E-Commerce Checkout

Learner receives:

**User Story**

> As a customer, I want to purchase products using my credit card so that I can complete my order online.

**Acceptance Criteria**

- Customer must have at least one item in the cart.
- Customer must provide valid shipping information.
- Customer must provide valid payment information.
- Payment must be authorized.
- Order should be created after successful payment.
- Customer should receive an order confirmation.
- Failed payment should not create an order.

### Learner must produce

**1. Testing Types**
- Functional
- Integration
- System
- Security
- Performance
- Regression

**2. Test Strategy**
- Identify risks
- Define testing priorities
- Identify automation candidates

**3. Test Scenarios**
- Successful payment
- Failed payment
- Invalid card
- Expired card
- Insufficient funds
- Network interruption
- Duplicate payment attempt
- etc.

**4. Test Cases**
Convert selected scenarios into detailed executable test cases.

**5. Defects**
Given several failed test cases, create defect reports and move them through the defect lifecycle.

**6. Estimation**
Estimate the effort required to test the checkout feature.

---

## Recommended Module Structure

I would therefore make the learning hierarchy:

```text
Software Testing Foundation
│
└── Module: Test Design & Strategy
    │
    ├── 1. Testing Types
    │
    ├── 2. Test Strategies
    │
    ├── 3. Use Cases
    │
    ├── 4. User Stories
    │
    ├── 5. Acceptance Criteria
    │
    ├── 6. Test Scenarios
    │
    ├── 7. Test Case Design
    │   ├── Equivalence Partitioning
    │   ├── Boundary Value Analysis
    │   ├── Decision Tables
    │   ├── State Transitions
    │   └── Error Guessing
    │
    ├── 8. Defect Life Cycle
    │
    ├── 9. Test Estimation
    │
    └── 10. Capstone: E-Commerce Checkout Testing
```
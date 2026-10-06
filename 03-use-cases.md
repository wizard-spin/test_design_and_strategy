# Chapter 3 — Use Cases

> **Software Testing Foundation · Module: Test Design & Strategy**
> **Chapter 3 of 10** · ⏱️ **Time: 15 minutes** (8 min reading + 7 min practice)
> **Previous:** [← Chapter 2 — Test Strategies](02-test-strategies.md) · **Next:** [Chapter 4 — User Stories →](04-user-stories.md)

---

## Learning Objectives

By the end of this chapter you will be able to:

1. Define a use case and name its parts: actor, system, preconditions, trigger, flows, postconditions.
2. Separate the **main success flow** from the **alternate** and **exception** flows.
3. Explain how a use case differs from a test case.
4. Write a simple use case and identify the scenarios it contains.

---

## Where This Chapter Fits

In Chapter 2 you learned that testing starts from **requirements**. A use case is one of the most tester-friendly ways to write requirements, because it describes a **step-by-step interaction** that maps naturally to test flows. In Chapter 6 you will turn each flow of a use case into test scenarios.

---

## 1. What Is a Use Case?

A **use case** describes **how an actor interacts with a system to achieve a specific goal**, including what happens when things go right and when they go wrong.

> A use case tells a *story of interaction*: "The user does X, the system responds with Y…" until the goal is achieved or abandoned.

Use cases are common in traditional (Waterfall/V-model) projects, in regulated industries (banking, healthcare), and in system design documents. Agile teams often use user stories instead (Chapter 4), but use-case thinking is still valuable to testers.

---

## 2. Parts of a Use Case

| Element | Meaning | ATM example |
|---|---|---|
| **Use Case Name** | A short, verb-first goal | Withdraw cash from an ATM |
| **Actor** | Who (or what) interacts with the system to achieve a goal. It can be a person or another system. | Customer (primary); Bank Core System (secondary) |
| **System** | The thing being built or tested (the boundary) | ATM + banking software |
| **Preconditions** | What must be true **before** the use case can start | Customer has a valid card and sufficient balance; ATM has cash |
| **Trigger** | The event that **starts** the use case | Customer inserts card |
| **Main Success Flow** | The ideal, "happy path" sequence of steps | See below |
| **Alternate Flow** | A *different but valid* path that still reaches the goal (or a valid variation) | Customer chooses a different account type |
| **Exception Flow** | A path where something goes **wrong** and the goal is **not** achieved | Wrong PIN 3 times, so the card is retained |
| **Postconditions** | What must be true **after** the use case ends | Cash dispensed, account debited, transaction logged |

### Actors in More Detail
- **Primary actor:** starts the interaction to achieve a goal (Customer).
- **Secondary / supporting actor:** helps the system achieve the goal (Bank Core System, SMS gateway).
- Actors are **roles**, not people. One person can be a "Customer" today and an "Admin" tomorrow.

---

## 3. Complete Example: Withdraw Cash from an ATM

**Use Case ID:** UC-ATM-01
**Use Case:** Withdraw cash from an ATM
**Actor:** Customer
**Supporting actors:** Bank Core System
**Precondition:** Customer has a valid card and sufficient balance. ATM is online and has cash.
**Trigger:** Customer inserts card into the ATM.

**Main success flow:**
1. Insert card
2. Enter PIN
3. Select withdrawal
4. Enter amount
5. System validates balance
6. ATM dispenses cash
7. Account is debited

**Alternate flows:**
- **A1 (at step 4): Customer selects a quick-cash amount** (for example, ₹2,000) instead of typing an amount. Continue at step 5.
- **A2 (at step 6): Customer requests a receipt.** ATM prints a receipt and continues.
- **A3 (at step 3): Customer has multiple accounts** and selects Savings or Current.

**Exception flows:**
- **E1 (at step 2): Incorrect PIN.** The system shows an error and allows a retry. After 3 incorrect attempts, the card is blocked or retained and the use case ends.
- **E2 (at step 5): Insufficient balance.** The system shows "Insufficient funds" and offers another amount or cancel.
- **E3 (at step 4): Amount not a multiple of 100 / above the per-transaction limit.** The system shows a validation error.
- **E4 (at step 6): ATM has insufficient cash or the dispenser jams.** The transaction is reversed and no debit happens.
- **E5 (any step): Customer does not respond within 30 seconds.** The session times out and the card is ejected.
- **E6 (at step 5): Connection to the bank is lost.** Transaction is cancelled; no debit.

**Postconditions:**
- **Success:** Cash dispensed. Account debited by exactly the amount withdrawn. Transaction recorded. Card returned.
- **Failure:** No cash dispensed. Account **not** debited (or debit reversed). Reason logged.

### From One Use Case, Many Scenarios

| Flow | Test scenario |
|---|---|
| Main flow | Withdraw a valid amount successfully |
| A1 | Withdraw using quick-cash |
| A2 | Withdraw with receipt |
| E1 | Wrong PIN once, then correct PIN; wrong PIN 3 times |
| E2 | Withdraw more than the balance |
| E3 | Withdraw ₹150 (not a multiple of 100); withdraw above the limit |
| E4 | ATM cash runs out mid-transaction |
| E5 | Customer walks away; timeout |
| E6 | Network failure during validation |

> One use case with 1 main + 3 alternate + 6 exception flows produced **10+ scenarios**. Testers often find most defects in the **exception flows**, because developers tend to focus on the happy path.

---

## 4. Use Case vs. Test Case

| | **Use Case** | **Test Case** |
|---|---|---|
| **Purpose** | Describes *required behaviour* (requirement) | *Verifies* that the behaviour works |
| **Written by** | Business analyst / product team | Tester |
| **Contains** | Actor, flows, preconditions, postconditions | Test data, exact steps, expected result, pass/fail |
| **Level of detail** | Business-level ("Customer enters amount") | Exact ("Enter 2000 in Amount field, click Proceed") |
| **Has test data?** | No | Yes |
| **Has pass/fail?** | No | Yes |
| **Relationship** | One use case → **many** test cases | Each test case traces back to a use case flow |

---

## ⚠️ Common Beginner Mistakes

1. **Writing only the happy path.** The value is in the alternate and exception flows.
2. **Mixing UI detail into the use case** ("click the blue button"). Keep it about the interaction, not the screen design.
3. **Forgetting postconditions on failure.** For example: "Account is **not** debited." That is a critical test point.
4. **Confusing actors with job titles.** An actor is a role, and it can be a system.

---

## 📝 Practice Exercise (≈ 7 minutes)

Write a brief use case (actor, preconditions, trigger, main flow, at least 2 alternate/exception flows, postconditions) for **one or more** of these:

1. **Login**
2. **Online shopping** (buy a product)
3. **Booking a flight**

<details>
<summary>✅ Model answer: Login</summary>

**Use Case:** Log in to the application
**Actor:** Registered user
**Precondition:** User has an active account; login page is accessible.
**Trigger:** User opens the login page.

**Main flow:**
1. User enters a registered email.
2. User enters the password.
3. User clicks *Login*.
4. System validates the credentials.
5. System creates a session and shows the dashboard.

**Alternate flows:**
- A1: User selects "Remember me", so the session persists after the browser closes.
- A2: User logs in with Google (SSO).

**Exception flows:**
- E1: Invalid email or password. Show a generic error ("Invalid credentials") and stay on the login page.
- E2: 5 consecutive failures. Lock the account for 15 minutes and show a lock message.
- E3: Account deactivated. Show "Account inactive, contact support."
- E4: Empty fields. Show "Email and password are required."

**Postconditions:** Success: user is authenticated, session created, last-login time recorded. Failure: no session; failed attempt counted.

</details>

<details>
<summary>✅ Model answer: Online shopping</summary>

**Use Case:** Purchase a product online
**Actor:** Customer; supporting: Payment gateway, Email service
**Precondition:** Customer is logged in; product is in stock.
**Trigger:** Customer clicks *Add to Cart* on a product.

**Main flow:**
1. Customer adds a product to the cart.
2. Customer opens the cart and clicks *Checkout*.
3. Customer enters or selects a shipping address.
4. Customer selects a payment method and enters card details.
5. System requests authorisation from the payment gateway.
6. Payment is authorised; system creates the order.
7. System shows the confirmation page and sends a confirmation email.

**Alternate flows:**
- A1: Customer applies a valid coupon at step 2, so the total is reduced.
- A2: Customer uses a saved address or card.

**Exception flows:**
- E1: Product goes out of stock before checkout. Notify the customer and remove the item.
- E2: Payment declined. Show an error; no order created; cart retained.
- E3: Payment gateway timeout. Show "Payment could not be completed"; no duplicate charge.

**Postconditions:** Success: order created, stock reduced, payment captured, email sent. Failure: no order, no charge, cart intact.

</details>

<details>
<summary>✅ Model answer: Booking a flight</summary>

**Use Case:** Book a one-way flight
**Actor:** Traveller; supporting: Airline inventory system, Payment gateway
**Precondition:** Flights exist for the selected route and date.
**Trigger:** Traveller searches for flights.

**Main flow:**
1. Traveller enters origin, destination, date, and passenger count.
2. System displays available flights.
3. Traveller selects a flight and fare.
4. Traveller enters passenger details.
5. Traveller pays.
6. System confirms the booking and issues a PNR and e-ticket.

**Alternate flows:**
- A1: Traveller selects a seat (paid or free).
- A2: Traveller adds extra baggage.

**Exception flows:**
- E1: No flights found. Suggest nearby dates.
- E2: Fare changes or the seat is taken during booking. Show the new fare and ask for confirmation.
- E3: Passenger name has invalid characters. Show a validation error.
- E4: Payment fails. Hold the booking for 15 minutes, then release the seat.

**Postconditions:** Success: seat reserved, PNR generated, ticket emailed. Failure: no booking, seat released, no charge.

</details>

---

## 🧠 Quick Check

1. What is the difference between an alternate flow and an exception flow?
2. Can an actor be a system? Give an example.
3. In the ATM use case, what is the trigger?
4. Does a use case contain test data and pass/fail status?

<details>
<summary>Answers</summary>

1. An **alternate** flow is a valid variation that usually still reaches the goal. An **exception** flow is an error condition where the goal is not achieved.
2. Yes, for example a payment gateway or a bank core system.
3. The customer **inserts the card**.
4. **No.** Those belong in test cases.

</details>

---

## 🔑 Key Takeaways

- A use case describes **actor ↔ system interaction** to reach a goal.
- Key parts: **actor, system, preconditions, trigger, main flow, alternate flows, exception flows, postconditions**.
- Each flow becomes **one or more test scenarios**, and exception flows are where many bugs hide.
- A use case is a **requirement**. A test case is a **verification** of it.

---

**Next:** [Chapter 4 — User Stories →](04-user-stories.md). Agile teams usually express requirements as short **user stories** instead of detailed use cases. You will learn how they work and how testers use them.

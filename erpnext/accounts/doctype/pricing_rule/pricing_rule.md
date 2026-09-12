# Pricing Rule

> **What this page is:** This documents only the extra fields that the cstm_erpnext app adds to the standard ERPNext **Pricing Rule** DocType. All other Pricing Rule fields behave exactly as they do in standard ERPNext and are not covered here.

---

## Bundle / quantity-multiple discounts

### Is QTY Multiple?
- **What it controls:** Whether the discount is given per whole bundle of the "Minimum Qty" you set on the rule, instead of on any loose quantity.
- **Effect when set / enabled:** In the Easy POS Plus screen the discounted item is treated as a bundle. The discount is only granted once the quantity reaches the minimum, and it is applied in whole multiples of that minimum. If the cashier lowers the quantity below the minimum, the discount is automatically removed from that line.
- **How it works:**
  1. When the item is added at POS, the system checks the quantity against the rule's minimum quantity.
  2. If the quantity is still below the minimum, the pricing rule is dropped from that line so no discount is applied.
  3. As matching units are added, they are grouped together into a bundle (the POS gives the grouped lines a shared, tagged barcode) so the system can track how many complete bundles exist.
  4. The discount is only counted for as many units as fit into complete bundles; leftover units above a whole multiple do not receive the discount.
- **Example:** A rule has minimum quantity 6 and "Is QTY Multiple?" enabled. If the cashier adds 4 units, no discount is applied. At 6 units the discount applies to the bundle. At 13 units the discount applies to 12 units (two complete bundles) and the 13th unit stays at full price.
- **Related settings:** Works together with the standard "Minimum Qty" field on the same rule, which defines the bundle size.

---

## Discount calculator (helper tool)

These four fields plus the Calculate button form a small "what-if" calculator that helps a business admin work out a discount figure. **Important:** the calculator only fills in the two read-only result fields for you to read. It does not change the rule's real discount by itself — after reading the result you still enter it into the rule's normal discount field (Rate / Discount Percentage / Discount Amount) yourself.

### Item Original Rate
- **What it controls:** The normal, undiscounted unit price of the item you are pricing. This is an input you type into the calculator.
- **Effect when set / enabled:** On its own it does nothing until you press **Calculate**. It is the starting price the calculator works from.
- **How it works:** The calculator multiplies this unit price by the rule's minimum quantity to get the normal total price of one bundle before any discount.

### Desired Total Rate
- **What it controls:** The total price you want the customer to actually pay for the minimum quantity (the whole bundle). This is an input you type into the calculator.
- **Effect when set / enabled:** On its own it does nothing until you press **Calculate**. It is the target price the calculator aims for.
- **How it works:** The calculator subtracts this target total from the normal total price to find how much discount is needed.

### Calculate (button)
- **What it controls:** Runs the discount calculation from the three inputs above (Item Original Rate, Desired Total Rate, and the rule's Minimum Qty).
- **Effect when set / enabled:** Fills in **Calculated Discount Amount** and **Calculated Discount Percentage** with the results. Nothing happens unless all three inputs (original rate, desired total, and a minimum quantity) have values.
- **How it works:**
  1. Normal total = Item Original Rate multiplied by the Minimum Qty.
  2. Discount amount = Normal total minus Desired Total Rate.
  3. Discount percentage = Discount amount divided by the Normal total, shown as a percentage.
  4. Both results are written into the two read-only fields below.

### Calculated Discount Amount
- **What it controls:** The read-only result showing the total money taken off for the whole minimum-quantity bundle.
- **Effect when set / enabled:** Display only. It is filled in by the Calculate button and cannot be edited by hand. It is not automatically copied into the rule's real discount field.
- **How it works:** Equals the normal bundle total minus your Desired Total Rate. Note this is the discount for the full bundle, not per single unit.

### Calculated Discount Percentage
- **What it controls:** The read-only result showing the discount as a percentage.
- **Effect when set / enabled:** Display only. It is filled in by the Calculate button and cannot be edited by hand. It is not automatically copied into the rule's real discount field.
- **How it works:** Equals the Calculated Discount Amount divided by the normal bundle total, expressed as a percentage.

- **Example (calculator):** Item Original Rate = 10.00, the rule's Minimum Qty = 5, and you want the customer to pay 40.00 for those 5 units (Desired Total Rate = 40.00). Pressing Calculate gives a normal total of 50.00, a **Calculated Discount Amount of 10.00** (for the 5-unit bundle) and a **Calculated Discount Percentage of 20%**. You would then enter 20% (or 10.00) into the rule's normal discount field to actually apply it.

---

## Cross-item ("other item") discounts

### Other UOM
- **What it controls:** The unit of measure of the *other* item that receives the discount, when the rule is set up to discount a different item than the one that triggers it (standard ERPNext "Apply Rule On Other" behaviour).
- **Effect when set / enabled:** The field only appears, and becomes required, when the rule's "Apply Rule On Other" is set to Item Code or Item Group. It pins exactly which unit of measure of the other item the discount attaches to. The picker only offers unit-of-measure options that belong to the chosen other item.
- **How it works:** At POS, when the triggering item is present, the system looks for the other item at this specific unit of measure. The discount is matched and applied to the other item only when both its item code and this unit of measure match what is on the rule.
- **Example:** A rule is triggered by buying a coffee machine and gives a discount on coffee capsules. If capsules are sold both as "Box" and "Piece", setting Other UOM to "Box" means only the capsule line sold in boxes receives the discount, not the loose pieces.
- **Related settings:** Works together with the standard "Apply Rule On Other" and "Other Item Code" / "Other Item Group" fields on the same rule.

---

## Usage limits and eligibility (POS)

### Usable Count
- **What it controls:** How many times a single customer may benefit from this rule's discount for the item, within the rule's valid-from / valid-to dates. Leave it at 0 to place no limit.
- **Effect when set / enabled:** When set above 0, POS starts enforcing a per-customer limit. Once the customer has reached the limit, the discount stops being offered on new invoices and the cashier is asked to add the item at full price instead. To count usage per customer, the rule also requires a real (non-cash) customer to be selected; if only the cash/walk-in customer is set, POS asks the cashier to pick the actual customer first.
- **How it works:**
  1. When the item is added, POS looks up how many times this customer has already used this rule for this item and unit of measure, between the rule's valid-from and valid-to dates.
  2. Past returns are subtracted, so a returned sale frees the usage back up.
  3. The way usage is counted and the cap that applies both depend on the **Useable By** setting (see below).
  4. If the used amount has reached the cap, the discount is withheld; otherwise POS shows the remaining allowance and applies the discount.
- **Example:** A rule with Usable Count = 3 and Useable By = "Invoice Count" lets a given customer receive the discount on three separate invoices. On their fourth invoice the item is offered at full price.
- **Related settings:** Useable By (counting method and cap), and the standard "Customer" and validity-date fields on the rule. Also interacts with the standard "Max Qty" field when Useable By is "Max QTY".

### Useable By
- **What it controls:** How the usage limit is measured — by the number of invoices, or by total quantity.
- **Effect when set / enabled:**
  - **Invoice Count** (the default): usage is counted as the number of separate invoices in which the customer used the discount, and the cap is the **Usable Count** value.
  - **Max QTY:** usage is counted as the total discounted quantity the customer has taken across invoices, and the cap is the rule's standard **Max Qty** field.
- **How it works:** The usage check (described under Usable Count) still only switches on when Usable Count is set above 0. When it is on, this setting decides both what gets counted (invoices vs. quantity) and which number is used as the ceiling (Usable Count vs. Max Qty).
- **Example:** With Useable By = "Max QTY" and Max Qty = 10, a customer can receive the discount on up to 10 units in total (for example 4 units on one invoice and 6 on another); an 11th discounted unit is refused. Switching to "Invoice Count" with Usable Count = 10 would instead allow the discount across 10 separate invoices regardless of quantity.
- **Related settings:** Usable Count and the standard Max Qty field on the same rule.

### App Users Only
- **What it controls:** Whether the discount is reserved only for sales that come from an app user (for example a mobile-app / online customer), rather than ordinary in-store sales.
- **Effect when set / enabled:** At POS, if the current sale is not flagged as coming from an app user, the discount is not applied. Depending on the situation POS either asks the cashier to add the item at full price or prompts for customer selection.
- **How it works:** Each sale carries an "app user" flag. When this option is on, POS grants the discount only when that flag is set; otherwise the pricing rule is removed from the line.
- **Example:** A promotion is set for app customers only. A walk-in paying at the counter does not get the discount, while the same item ordered through the app does.
- **Related settings:** Relates to app / online (e-commerce) ordering, which sets the app-user flag on the sale.

---

## Time-of-day validity

The standard **Valid From** and **Valid Upto** dates decide *when* a rule is live. **From Time** and **To Time** refine that to the clock, and the **Working Hours Mode** checkbox decides how the four values are read together. Both time fields are optional. A rule with both of them empty applies for its whole date range, in either mode, so every rule created before these fields existed keeps behaving exactly as it did.

### Working Hours Mode
- **What it controls:** Whether From Time / To Time are a **daily window** that repeats on every valid day (checked), or the **clock part of the start and end dates** of one continuous range (unchecked).
- **Effect when set / enabled:**
  - **Checked (Working Hours Mode):** Valid From / Valid Upto decide the days; From Time / To Time decide the hours *within each of those days* — a happy hour, a breakfast offer, a night-shift price. Outside those hours, on any of the days, the rule is not offered.
  - **Unchecked (continuous range, the default for new rules):** Valid From + From Time is the single moment the rule starts, and Valid Upto + To Time is the single moment it stops. The rule is live for the whole stretch in between, across midnight and across as many days as the range spans.
- **How it works:**
  1. Every time a price is worked out — on a quotation, order, invoice, purchase document, at the POS, or on the Item Board — the system first narrows the rules down to those whose date range covers the transaction's date.
  2. It then takes the transaction's date and time together as one moment and asks each surviving rule, in its own mode, whether it is live at that moment.
  3. The time it uses is the document's own **Posting Time** where the document has one (sales invoices, purchase invoices, POS invoices, delivery notes). Documents that only carry a date — quotations, sales orders, purchase orders — are judged at the current time instead. Because the document's own posting date and time are used, re-saving a backdated invoice re-prices it at the moment it was posted, not at the moment you happen to be editing it.
  4. All of these values are compared in the system time zone (System Settings → Time Zone), which is the zone posting dates and times are stored in, so no conversion takes place.
- **Example (checked):** Valid From 01-01-2027, Valid Upto 05-01-2027, From Time 08:00, To Time 18:00. The rule applies on 01-01 at 09:00 and on 03-01 at 10:00, but not on 01-01 at 19:00, not on 03-01 at 23:00, and not at all on 06-01.
- **Example (unchecked):** Valid From 01-01-2027, From Time 07:00, Valid Upto 02-01-2027, To Time 06:00. The rule applies from 01-01 07:00:00 through 02-01 06:00:00 without a break — at 15:00, at 23:59, at 00:00 and at 03:00 alike. At 06:59 on 01-01 and at 06:01 on 02-01 it does not.
- **Related settings:** From Time, To Time, Valid From, Valid Upto. The checkbox is off on every rule that existed before it was introduced. A rule that already carries a From Time or To Time was, until then, always read as a daily window, so tick the checkbox on any such rule that should keep that behaviour; rules with no times behave the same either way and need nothing.

### From Time
- **What it controls:** The clock time the rule starts applying — on every valid day in Working Hours Mode, or on the Valid From day only in continuous mode.
- **Effect when set / enabled:** Before this time the rule is not offered at all — the item is priced as if the rule did not exist. Leaving it empty means the rule starts at the very beginning of the day: every day in Working Hours Mode, the Valid From day in continuous mode.
- **How it works:**
  1. The boundary is **inclusive**: a From Time of 09:00 applies at exactly 09:00:00.
  2. In continuous mode the time needs a Valid From date to attach to; the form refuses to save a From Time without one. In Working Hours Mode a From Time with no dates simply applies every day from that time.
- **Example:** Working Hours Mode, Valid From 1 March, Valid Upto 31 March, From Time 16:00, To Time 19:00: an invoice posted at 17:30 on 12 March gets the discount; one posted at 15:00 the same day, or at 17:30 on 2 April, does not.
- **Related settings:** Works together with **To Time** and **Working Hours Mode**, and with the standard **Valid From** / **Valid Upto** dates.

### To Time
- **What it controls:** The clock time the rule stops applying — on every valid day in Working Hours Mode, or on the Valid Upto day only in continuous mode.
- **Effect when set / enabled:** After this time the rule is no longer offered. Leaving it empty means the rule runs to the very end of the day: every day in Working Hours Mode, the Valid Upto day in continuous mode.
- **How it works:**
  1. The boundary is **inclusive**: a To Time of 17:00 still applies at exactly 17:00:00 and stops at 17:00:01.
  2. **Working Hours Mode, To Time earlier than From Time:** the daily window crosses midnight. 22:00 to 06:00 is live from 22:00 until midnight and again from midnight until 06:00 — the check is "at or after 22:00 *or* at or before 06:00", not "between". The window is still limited by the validity dates, so on the last valid day it runs from 22:00 to the end of that day, and on the first valid day it also covers the early-morning stretch up to 06:00.
  3. **Working Hours Mode, To Time equal to From Time:** the rule applies all day, just as it does with both fields empty. The only other reading would be a window one second long, which would leave the rule silently never firing.
  4. **Continuous mode:** neither special case exists — the times are just the clock parts of the two dates. A To Time earlier than the From Time on a later date is simply a range that ends in the morning (22:00 → 06:00 the next day is one eight-hour stretch), and the form refuses to save a range whose start moment falls after its end moment (for example 18:00 → 08:00 on the same day).
- **Example:** Working Hours Mode with From Time 22:00 and To Time 06:00 applies to a POS sale rung up at 23:40 and to one at 01:15, but not to one at 14:00. The same four values with Working Hours Mode off, Valid From 01-01-2027 and Valid Upto 02-01-2027, apply to exactly one night — 22:00 on 01-01 through 06:00 on 02-01 — and to nothing else in the range.
- **Related settings:** Works together with **From Time** and **Working Hours Mode**. When several rules overlap at the same moment, the standard **Priority** field decides which one wins, exactly as it does without time windows.

---

## Expiry reminders

### Remind On Expire
- **What it controls:** Whether the people responsible for a rule are automatically reminded the day before it expires.
- **Effect when set / enabled:** Each morning a scheduled job looks for active rules whose valid-to date is the next day and, for those with this option enabled, sends the responsible users a reminder by email and SMS.
- **How it works:**
  1. Once a day the system lists rules that are not disabled, have this option enabled, and expire tomorrow.
  2. For each rule it reads the warehouse on the rule, finds that warehouse's branch, and looks up the users listed as pricing-rule responsibles for that branch.
  3. Each responsible user is emailed (with a link to the rule) and, if they have a mobile number on their employee record, texted.
  4. The wording of these messages comes from the templates in Custom Selling Settings. A rule with no warehouse, or a branch with no responsible users, produces no reminder.
- **Example:** A rule valid until the 31st with Remind On Expire enabled, stored against a warehouse whose branch has two responsible users, triggers an email and SMS to both users on the morning of the 30th.
- **Related settings:** The message wording is taken from **Custom Selling Settings → Pricing Rule Expiry Reminder** (Reminder Email Subject, Reminder Email Template, Reminder SMS Template). The recipient list comes from the branch's pricing-rule responsible users, and the rule's warehouse and validity dates determine which rules qualify.

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

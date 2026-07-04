# POS Profile

> **What this page is:** This document describes the extra fields that the cstm_erpnext app adds to the standard ERPNext **POS Profile**. A POS Profile is the till/register configuration that a cashier logs into; these custom fields tune how that register behaves. It also covers the per-cashier custom fields on the register's **Applicable for Users** table (see the last section). Only the custom (cstm_erpnext) fields are covered here — the standard ERPNext POS Profile fields are documented by ERPNext itself.

## Branch and Receipts

### Branch
- **What it controls:** The default branch (physical shop/location) tied to this register.
- **Effect when set:** Every sale made on this register is automatically stamped with this branch if the invoice does not already carry one.
- **How it works:** When a POS invoice is being prepared and no branch has been set on it yet, the register's Branch is copied onto the invoice. From then on the branch travels with the sale — it is used to match stock movements to the shop and to control where a sale may later be returned.
- **Related settings:** The branch stamped here is later checked on returns — a receipt can only be returned in the same branch it was sold in.

### Sales Footer
- **What it controls:** The footer block (logo, legal text, thank-you message, etc.) printed at the bottom of a **sale** receipt from this register.
- **Effect when set:** The chosen footer layout is pulled in and printed underneath the items on every sale receipt.
- **How it works:** This points to a saved "Layout Markup" (a reusable receipt-footer design). When a sale receipt is printed, the register looks up this footer and inserts its content into the printout.
- **Related settings:** See **Return Footer** below for the equivalent used on refund receipts.

### Return Footer
- **What it controls:** The footer block printed at the bottom of a **return / refund** receipt from this register.
- **Effect when set:** The chosen footer layout is printed underneath the items whenever a return receipt is produced instead of a sale receipt.
- **How it works:** Same mechanism as Sales Footer, but the system automatically switches to this footer when the receipt is a return rather than a sale, so refunds can carry different wording or legal text.
- **Related settings:** **Sales Footer** (used for normal sales).

## End of Day

### Postponing Transaction End of Day (in hours)
- **What it controls:** The hour of the day at which the business day "rolls over" for this register's end-of-day cashier summary.
- **Effect when set:** Sales made late at night, after midnight, can still be counted against the previous business day instead of starting a new one at 00:00.
- **How it works:** When the cashier end-of-day summary is generated, the "from" and "to" times of the day are shifted to this hour rather than midnight. The business day is treated as running from this hour on one day up to one minute before the same hour the next day. For example, a value of 6 makes the day run from 06:00 to 05:59 the following morning.
- **Example:** With this set to 6, a sale rung up at 01:30 in the morning is grouped into the previous day's cashier session (which ends at 05:59), so a late-night shift's takings all land on the same day.

## Sales and Commission

### apply Sales Person Commission
- **What it controls:** Whether a salesperson can be attached to each item on this register and whether the salesperson column is shown.
- **Effect when enabled:** A "Salesperson" selector appears against cart items and in the receipt view, so sales can be credited to staff for commission purposes.
- **How it works:** When enabled, the register shows the salesperson field on each line so the cashier can assign who made the sale; when disabled, that column is hidden and no salesperson is prompted for. (Assignment is only offered when the sale is one that carries commission.)

## Cart Display

### Hide Item Code In Cart
- **What it controls:** Whether the internal item code is shown next to each product in the on-screen cart.
- **Effect when enabled:** The item code line is hidden in the cart; only the item name (and, unless also hidden, the barcode) is shown.
- **How it works:** This is a display-only preference for the cashier's cart view. It does not change what is stored on the invoice — only what the cashier sees while ringing up.
- **Related settings:** **Hide Barcode In Cart** (hides the barcode line the same way).

### Hide Barcode In Cart
- **What it controls:** Whether the barcode is shown next to each product in the on-screen cart.
- **Effect when enabled:** The barcode line is hidden in the cart display.
- **How it works:** Same display-only behaviour as Hide Item Code In Cart, applied to the barcode. Storage of the sale is unaffected.
- **Related settings:** **Hide Item Code In Cart**.

## Scanning

### Get QTY From Barcode
- **What it controls:** Whether quantity is read directly out of certain scanned barcodes (weighed/embedded-quantity labels) instead of always adding one unit.
- **Effect when enabled:** When a barcode that begins with "999" is scanned, the register extracts the quantity encoded in that barcode and uses it, rather than treating the scan as a single piece.
- **How it works:** These "999" barcodes are the type printed by scales and similar devices, where the weight or count is baked into the barcode itself. With this option on, scanning such a label adds the encoded amount in one step; the plus/minus and manual-quantity behaviour for those items is also adjusted to respect the embedded amount.
- **Example:** A deli scale prints a label starting "999" for 1.250 kg of cheese. With this enabled, scanning it adds 1.250 to the cart automatically instead of adding a quantity of 1.

## Quantity Controls

These three options let you lock down how much a cashier can change item quantities at this register — useful for scale-based items or to prevent accidental edits.

### Disable QTY Increase
- **What it controls:** Whether the cashier can use the "+" button to increase an item's quantity in the cart.
- **Effect when enabled:** The "+" (increment) button on cart items is greyed out and cannot be pressed.

### Disable QTY Decrease
- **What it controls:** Whether the cashier can use the "−" button to decrease an item's quantity in the cart.
- **Effect when enabled:** The "−" (decrement) button on cart items is greyed out and cannot be pressed.

### Disable QTY Change
- **What it controls:** Whether the cashier can edit an item's quantity at all.
- **Effect when enabled:** Manual editing of the quantity is blocked, so the quantity stays as first added (for example, exactly what a scanned scale label set it to).
- **Related settings:** Works alongside **Disable QTY Increase** and **Disable QTY Decrease**; use all three to fully fix quantities at a register.

## Server / Sync

### Main Server
- **What it controls:** Marks this register as belonging to the central "main" server rather than a branch/sub server.
- **Effect when enabled:** Returns are blocked on this register — attempting to return an invoice shows "You can't return an invoice from the main server."
- **How it works:** In a multi-site setup the main server is the head-office copy, while branches sell and return locally. Flagging a profile as main server prevents refunds from being processed there so that returns are handled at the branch that made the sale.

## Refunds

### Card Refund
- **What it controls:** Whether credit-card (Geidea) refunds are allowed on returns processed from this register — overriding the company-wide default for this specific POS Profile.
- **Effect when set:** Choosing **Enabled** turns card refunds on for this register; **Disabled** turns them off; **Inherit** (the default) means "use the company-wide setting."
- **How it works:** Card-refund permission is resolved from the most specific setting that has a firm answer, working from the individual cashier up to the company default, in this order:
  1. **POS Profile User → Card Refund** (the cashier's own override on this register)
  2. **POS Profile → Card Refund** (this field)
  3. **Custom Selling Settings → Enable Card Refund By Default** (the company-wide switch)

  Each level defaults to **Inherit**, meaning "fall through to the next level down." The first level that is set to **Enabled** or **Disabled** wins. If every level is left on **Inherit**, the company-wide switch decides.
- **Example:** The company default is off. You set this register's Card Refund to **Enabled** so this shop can give card refunds, while every other register keeps following the (off) company default. If one cashier on this register should still be blocked, set that person's POS Profile User → Card Refund to **Disabled** and their choice takes priority.
- **Related settings:**
  - **Custom Selling Settings → Enable Card Refund By Default** — the company-wide default this field overrides.
  - **POS Profile User → Card Refund** — a more specific, per-cashier override that takes priority over this field (see the next section).

## Applicable for Users — per-cashier settings

The **Applicable for Users** table on this POS Profile lists the cashiers allowed on the register. Each row (a *POS Profile User* — a child table with no screen of its own) can carry per-cashier overrides, edited inline in that table. The custom fields on each row are:

### Discount Percentage (per cashier)
- **What it controls:** The largest line-item discount, as a percentage, this cashier may give.
- **Effect when set:** A discount above this is refused, with a message showing the cashier's allowed limits.
- **How it works:** When the cashier types a discount, it is accepted only if it is within this limit or within the row's **Margin Discount Percentage**, whichever is higher; above both, it is blocked.
- **Example:** Discount Percentage is 10 — a 15% discount is blocked; 10% or less goes through.

### Margin Discount Percentage (per cashier)
- **What it controls:** A second, alternative discount allowance for this cashier.
- **Effect when set:** A discount is allowed if it is within **either** the Discount Percentage **or** this Margin Discount Percentage.
- **Example:** Discount Percentage 5, Margin Discount Percentage 12 — a 10% discount is allowed (within 12); 15% is refused (above both).

### Tamara (per cashier)
- **What it controls:** Whether this cashier may mark a sale as a Tamara (buy-now-pay-later) payment.
- **Effect when enabled:** The Tamara option appears in this cashier's payment screen; without it, they cannot flag a sale as Tamara.

### Return Tamara (per cashier)
- **What it controls:** Whether this cashier may return an invoice that was originally sold on Tamara.
- **How it works:** On a return, if the original sale was Tamara and this is off, the return is blocked ("You don't have permission to return a Tamara invoice"). Non-Tamara returns are unaffected.

### Only Return Tamara (per cashier)
- **What it controls:** Restricts this cashier to returning **only** Tamara invoices — normal (non-Tamara) returns are blocked.
- **How it works:** On a return, if this is on and the original sale was not Tamara, the return is refused ("You are only allowed to return Tamara"). Usually paired with **Return Tamara**.

### Card Refund (per cashier)
- **What it controls:** The most specific card-refund override — for this one cashier on this register.
- **Effect when set:** **Enabled** / **Disabled** force card refunds on/off for this cashier; **Inherit** (default) falls through to the register's, then the company's setting.
- **How it works:** This sits at the top of the resolution chain described under **Card Refund** above (cashier → register → company default), so an Enabled/Disabled here beats both the POS Profile and the company-wide switch.

# Branch

> **What this page is:** This documents the custom fields that the `cstm_erpnext` app adds to the standard ERPNext **Branch** record. Standard Branch fields (like the branch name itself) are not covered here — only the extra fields added by this app, and what each one actually does in the system.

---

## Naming & Identity

These fields describe how the branch is named on receipts, in accounting, and in official paperwork.

### Main Commercial Name
- **What it controls:** The primary trading name of the branch (the top-line business name).
- **Effect when set:** It is passed to the point-of-sale receipt printout, where it appears as part of the branch/company header.
- **How it works:** When a POS receipt or return slip is generated, the system reads this value from the branch and prints it alongside the Commercial Name. Purely a display value — it changes what is printed, nothing else.

### Commercial Name
- **What it controls:** The branch's shorter commercial/display name.
- **Effect when set:** Printed as the bold branch name on POS sales and return receipts.
- **How it works:** Read from the branch at print time and shown on the receipt header. No accounting or pricing effect.

### No
- **What it controls:** A free-text reference code for the branch. It is marked required and must be unique across branches.
- **Effect when set:** Stored on the branch as a label only. No feature in the app reads or acts on this value — it is informational, kept for your own referencing.

### Branch Number
- **What it controls:** A numeric identifier for the branch.
- **Effect when set:** Stored for reference only. No feature in the app reads or acts on it — informational.

---

## Pricing

### Default Price List
- **What it controls:** The selling price list that this branch uses. This is one of the most important custom fields on the branch.
- **Effect when set:** Every item price the branch shows or sells at — in the desktop POS/item board, in the mobile app, and in several sales and stock reports — is read from this price list. Bulk price-change tools also use it to know which prices belong to this branch.
- **How it works:** Warehouses are attached to a branch. Whenever the system needs "the price of this item at this branch", it looks up the branch, reads its Default Price List, and uses that list's rates. It works in both directions: reading prices for selling, and locating the right prices to update when you run a price change for the branch's warehouses.
- **Example:** A cashier scans an item in the mobile app for the "Riyadh Main" branch. The app reads Riyadh Main's Default Price List and shows the item's rate from that list. If no Default Price List is set on the branch, the mobile app refuses the lookup and reports that no price list was found.
- **Related settings:** Warehouses must be linked to this branch for the branch price list to apply to them. The Pricing tool and price-change reports rely on this field to map prices to the branch.

---

## Branch Management & Responsibilities

### Branch Manager
- **What it controls:** The employee who manages this branch.
- **Effect when set:** In the mobile app, this is how the system knows which branch a signed-in manager runs. It gates manager-only actions (such as creating and assigning tasks to the branch's employees) and is used for permission checks.
- **How it works:** When a manager uses a manager feature in the mobile app, the system finds the branch whose Branch Manager is that employee. If the signed-in employee is not set as the Branch Manager on any branch, manager actions are refused.
- **Related settings:** Points to an Employee record. The related **Branch Manager Name** below fills in automatically.

### Branch Manager Name
- **What it controls:** The display name of the chosen Branch Manager. This field is read-only.
- **Effect when set:** Fills in automatically from the selected Branch Manager employee — you do not type it. It exists so the manager's name is visible in the branch list without opening the employee record.

### Pricing Rule Responsables
- **What it controls:** The list of users who are responsible for the branch's pricing rules (a multi-select list of users).
- **Effect when set:** These users receive an email and SMS reminder the day before any Pricing Rule tied to one of the branch's warehouses is due to expire.
- **How it works:** Once a day the system looks for pricing rules that expire the next day and are flagged to remind on expiry. For each one, it finds the warehouse's branch, reads that branch's Pricing Rule Responsables, and sends each listed user a reminder listing the expiring rules. Users with no email or mobile number simply get whichever channel they have.
- **Related settings:** The reminder wording (email subject, email body, SMS text) comes from **Custom Selling Settings**. A pricing rule is only included if it is set to remind on expiry.

---

## Accounting & Daily Cash Handover

Most of the account fields below are used when a branch's daily takings (cash, card, and Tamara sales) are handed over and posted through a **Sales Submission**. Note that several of these are free-text fields where you type the exact account, branch, or customer name — they are not validated dropdowns, so the name must match an existing record exactly.

### Currant Account
- **What it controls:** The branch's inter-branch current (clearing) account. (An Account dropdown.)
- **Effect when set:** Used to balance journal entries that touch more than one branch. When a single journal entry mixes accounts from different branches, the system automatically adds offsetting lines through each branch's current account so each branch stays balanced on its own.
- **How it works:** On a multi-branch journal entry, the system identifies the "one side" branch and the "other side" branches, then posts matching entries between each branch's Currant Account so the entry nets out per branch. It is also read in some stock-recovery routines that need the branch's clearing account.
- **Related settings:** Links to an Account in your chart of accounts.

### Branch Transfer Cost Account
- **What it controls:** The account that absorbs the difference between last-purchase price and average cost when stock is transferred **out** of this branch to another branch. (An Account dropdown.)
- **Effect when set:** When the "value branch transfers at last purchase price" feature is turned on, a cross-branch transfer values the goods at the source branch's last purchase price, and the resulting price-vs-cost gap is booked to this account, tagged to the source branch.
- **How it works:** On a branch-to-branch stock transfer, the system prices the moving items at the source branch's last purchase price. The gap between that price and the moving-average cost is posted to this account. If this field is empty, the system falls back to the account configured centrally; if neither is set, the transfer is blocked with a message.
- **Related settings:** Falls back to the **Branch Transfer Cost Account** on **Custom Stock Settings**, and the feature is switched on there via the last-purchase-price transfer option.

### Master Branch
- **What it controls:** The name of the head-office / consolidation branch that this branch hands its takings over to. (Free-text — type the exact branch name.)
- **Effect when set:** In a Sales Submission, the master side of the handover journal entries is tagged to this branch, and its cash account (below) is where the branch's cash lands.

### Master Cash Account
- **What it controls:** The cash account of the head-office / master branch that receives this branch's cash. (Free-text — type the exact account name.)
- **Effect when set:** When a Sales Submission is posted, the branch's cash and card amounts are debited to this master cash account. It is also used to calculate the branch's opening balance for the day.

### Cash Account
- **What it controls:** This branch's own cash account (its till). (Free-text — type the exact account name.)
- **Effect when set:** In a Sales Submission, this account is credited/debited on the branch side for cash, card, Tamara, and shortage/surplus movements as the day's takings are booked.

### Bank Account
- **What it controls:** The account used for card (bank/POS-machine) receipts on the master side of the handover. (Free-text — type the exact account name.)
- **Effect when set:** In a Sales Submission, card amounts are debited to this account against the branch's cash account.

### Tamara Account
- **What it controls:** The receivable account for Tamara (buy-now-pay-later) sales. (Free-text — type the exact account name.)
- **Effect when set:** When a branch has Tamara sales, the Sales Submission books the Tamara amount to this account.

### Tamara Customer
- **What it controls:** The customer record that Tamara receivables are booked against. (Free-text — type the exact customer name.)
- **Effect when set:** Used as the party on the Tamara journal-entry line so the Tamara balance is tracked against this customer.

### Branch Naming Series
- **What it controls:** The numbering series applied to journal entries this branch generates.
- **Effect when set:** Every journal entry that a Sales Submission creates for this branch (cash/card handover, Tamara, inter-branch, and transaction entries) is numbered using this series.
- **Related settings:** Applied by the **Sales Submission** process.

> **How the handover works (Sales Submission):** When you post a Sales Submission for a branch, the system reads Master Branch, Master Cash Account, Cash Account, Bank Account and Branch Naming Series from the branch and uses them to build the journal entries that move the day's cash and card takings from the branch's own accounts to the head-office accounts, plus separate entries for Tamara sales and any shortage/surplus. Master Branch, Master Cash Account, Cash Account, Bank Account and Branch Naming Series are treated as required — if any is blank, posting stops with a message asking you to set it on the branch. Tamara Account and Tamara Customer are only needed when there are Tamara sales.

---

## Attendance

### Finger Print Device
- **What it controls:** The fingerprint/attendance device that serves this branch. This field is required. (A Fingerprint Device dropdown.)
- **Effect when set:** Attendance and absence-penalty processing for the branch reads the linked device — for example, to know the date attendance was last processed from that device before applying absence penalties.
- **How it works:** When the system evaluates absences and punishments for a branch, it opens the branch's Finger Print Device and reads its last-processed information to decide the period to work over.
- **Related settings:** Links to a **Fingerprint Device** record; overall behaviour is governed by **Fingerprint Settings**.

---

## Registration, Licensing & Documents (informational)

These fields hold the branch's official registration and license details and document images. Except where noted, they are stored for reference and shown on the branch record only — no automation acts on them (no expiry alerts are driven from these dates).

- **Commercial registration number** — The branch's commercial registration (CR) number. Stored for reference, and also printed on POS sales/return receipts as the branch's commercial number.
- **Address** — The branch's address text. Stored for reference, and also printed on POS receipts.
- **Expiry date of the registration** — The CR expiry date. Informational only.
- **Registration image** / **Image url** — Uploaded picture(s) of the commercial registration. Informational only.
- **Municipal License number** — The municipal (baladiya) license number. Informational only.
- **Expiry date of the Municipal License** — The municipal license expiry date. Informational only.
- **Municipal License image** / **Municipal License image url** — Uploaded picture(s) of the municipal license. Informational only.
- **Civil Defence Expire Date** — The civil-defence certificate expiry date. Informational only.

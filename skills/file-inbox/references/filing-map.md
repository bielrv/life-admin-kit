# Filing map — exact destinations for recurring inbox items

This file is the system's **learned state**. The file-inbox skill checks it FIRST and
only explores directories when nothing here matches.

## Maintenance rules (for the skill)

- After every filing run, if a **new recurring pattern** appeared (a provider seen for
  the second time, a new monthly receipt, a new document family), add one row.
- If a mapping **proved wrong** (the file actually belonged elsewhere), fix the row in
  the same session — never leave a known-bad mapping in place.
- Keep rows one line each. Put disambiguation hints (amounts, reference numbers,
  filename patterns) in the row itself so a match is decisive.
- Update the "Verified" date below whenever the whole table is checked against reality.

Verified: {{DATE}}

## Address-tied invoices → `<property or location folder>/Utilities/<type>/`

When one provider serves several addresses, the supply address inside the document is
authoritative — never route on provider name alone.

| Provider | Type | Usual destination | Hint |
|---|---|---|---|
| Acme Energy | Electricity | `02. Property/01. Property A/Utilities/Electricity/` | serves A and B — check supply address |
| Metro Water Co | Water | `02. Property/01. Property A/Utilities/Water/` | |
| FiberFast | Internet | `02. Property/02. Property B/Utilities/Internet/` | invoice filename is a UUID — decode from PDF text |

<!-- Replace the example rows above with real providers as they appear. -->

## Other recurring items

| Item | Destination |
|---|---|
| Payslip | `01. Documents/03. Payslips/<year>/` |
| Tax return / tax-agency letter | `01. Documents/04. Tax returns/<year>/` |
| Brokerage export/report | `05. Investments/<broker>/` |
| Medical report, lab results, vaccination cert | `01. Documents/07. Medical/` |
| Insurance policy/claim | `01. Documents/06. Insurance/` |
| Loan / credit-report doc | `01. Documents/<loans category>/` |
| Freelance invoice (issued or received) | `04. Work/<client or business folder>/` |
| Vehicle doc (plate number in name/text) | `03. Vehicles/<vehicle folder>/` |
| Rent transfer receipt (match tenant name / amount) | `02. Property/<property>/Rental Proof of Payments/<year>/` |
| Mortgage receipt (match amount / account ref) | `02. Property/<property>/Mortgage/` |
| Property tax / community-of-owners doc | `02. Property/<property>/Others/` |
| Book (epub/mobi/pdf) | `<books folder>/` as `Author - Title.ext` |
| Trip booking/ticket | `06. Trips/YYYY-MM Destination/` |
| Device manual | `99. Others/Manuals/` |
| Drive Health Report (monthly, from /drive-health-check) | `99. Others/` as `YYYY-MM-DD Drive Health Report YYYY-MM.md` |

<!-- The paths above assume the template folder layout; the setup-drive skill rewrites
     them to match the schema it generates. Rows for domains the owner doesn't have
     should be deleted, not left as noise. -->

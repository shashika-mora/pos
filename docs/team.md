# Super-Saving POS System — Team Work Division

**Group Name:** BinaryMinds  
**Team Size:** 5 members  
**Main Class:** `SuperSaverPOSGroup_BinaryMinds.java`

---

## Member Roles & Responsibilities

### Member 1 — Data Layer Lead
**Focus: CSV Loading + Product & Custom Exceptions**

| Task | Class(es) / Section |
|------|---------------------|
| Parse the CSV file into a `Map<String, Product>` | `CsvLoader` |
| Define the `Product` entity with all attributes, getters, setters, `isExpired()` | `Product` |
| Write custom exception classes | `ItemNotFoundException`, `InvalidDataException`, `ExpiredProductException` |
| Handle malformed rows gracefully (skip + log) | Inside `CsvLoader.loadInventory()` |

**Deliverable:** Working `CsvLoader` that returns a fully populated product map at startup.

---

### Member 2 — Billing Core
**Focus: Bill, LineItem, Calculations**

| Task | Class(es) / Section |
|------|---------------------|
| Define `LineItem` with `calculateLineTotals()` | `LineItem` |
| Define `Bill` with item list management and `calculateTotals()` | `Bill` |
| Implement `addItem()` / `removeItem()` logic | `Bill` |
| Ensure both classes implement `Serializable` with `serialVersionUID` | `Bill`, `LineItem` |

**Deliverable:** A `Bill` object that correctly calculates net price, discounts, and totals per line.

---

### Member 3 — Persistence (Serialization + PDF)
**Focus: Pending Bills (`.ser`) + PDF Receipt**

| Task | Class(es) / Section |
|------|---------------------|
| Save a bill to disk using `ObjectOutputStream` | `BillSerializer.savePending()` |
| Load a bill from disk using `ObjectInputStream` | `BillSerializer.loadPending()` |
| Generate a formatted text-based PDF receipt to file | `PdfGenerator.generate()` |
| Create the `pending_bills/` directory if it doesn't exist | Setup in `BillSerializer` |

**Deliverable:** Cashier can hold a bill, and the file appears in `pending_bills/`. Finalized bills produce a `.pdf` file.

---

### Member 4 — Revenue Reports + Email
**Focus: Report Generation + Email Dispatch**

| Task | Class(es) / Section |
|------|---------------------|
| Filter completed bills by date range | `ReportEngine.generateReport()` |
| Compute total revenue, item counts, discount totals | Inside `generateReport()` |
| Format the report as readable plain text | Return value of `generateReport()` |
| Send report via SMTP or simulate email output to console | `ReportEngine.emailReport()` |

**Deliverable:** A formatted revenue report printed to console (and optionally emailed). Exception caught gracefully if SMTP is unavailable.

---

### Member 5 — Main Driver + Console Menu + Integration
**Focus: `main()`, Menu, Javadoc, Final Assembly**

| Task | Class(es) / Section |
|------|---------------------|
| Build the console-based cashier menu (loop + switch) | `SuperSaverPOSGroup_BinaryMinds.main()` |
| Wire up all components: load CSV at startup, route user choices | `handleNewBill()`, `handleAddItem()`, etc. |
| Write the top-level Javadoc comment listing all requirements | File header comment |
| Add Javadoc comments to every class and method (coordinate with team) | All classes |
| Final review: naming, readability, comment quality | Entire file |

**Deliverable:** A fully runnable single `.java` file that the team can submit.

---

## Integration Checklist

- [ ] Member 1 completes `CsvLoader` + `Product` + exception classes
- [ ] Member 2 completes `Bill` + `LineItem` (depends on `Product` from M1)
- [ ] Member 3 completes `BillSerializer` + `PdfGenerator` (depends on `Bill` from M2)
- [ ] Member 4 completes `ReportEngine` (depends on `Bill` from M2)
- [ ] Member 5 assembles all into `main()` and runs end-to-end tests
- [ ] Member 5 adds file header Javadoc
- [ ] All members add Javadoc to their own classes and methods
- [ ] Final pass: check similarity < 20% (TurnItIn)

---

## Coding Guidelines (All Members)

1. **One file only** — paste all classes below the `public` main class in `SuperSaverPOSGroup_BinaryMinds.java`
2. **Javadoc on everything** — every class, every method, every custom exception
3. **Meaningful names** — `totalDiscount` not `td`, `calculateTotals()` not `calc()`
4. **No AI-generated boilerplate phrases** — write comments in your own words
5. **Handle exceptions visibly** — always print a user-friendly message to the console before failing

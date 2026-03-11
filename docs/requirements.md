# Super-Saving POS System — Requirements

## 1. Functional Requirements

| # | Requirement |
|---|-------------|
| FR-01 | Cashier can enter an item code and quantity to add a grocery item to the current bill |
| FR-02 | System looks up item details (price, weight/size, manufacturing date, expiry date, manufacturer) from a CSV database using the item code |
| FR-03 | Each item may carry a discount between 0% and 75%; the system must apply it automatically |
| FR-04 | System processes all added items and calculates the bill totals |
| FR-05 | Bill displays: cashier name, branch, customer name (if registered), item list (unit price, quantity, discount %, net price), total discount, total cost, print date and time |
| FR-06 | Finalized bill is saved as a PDF file for printing and customer handover |
| FR-07 | Cashier can **pause** (hold) an in-progress bill and serve the next customer |
| FR-08 | Cashier can **resume** a previously held pending bill and continue adding items |
| FR-09 | System can generate a revenue report for a user-specified date range from historical bills |
| FR-10 | Generated revenue report is emailed automatically to `salesteam@supersaving.lk` |

---

## 2. Non-Functional Requirements

| # | Requirement |
|---|-------------|
| NFR-01 | System is implemented in **Java** using OOP principles (encapsulation, abstraction, composition) |
| NFR-02 | **CSV file** acts as the product database (no external DB required) |
| NFR-03 | All Java classes must reside in a **single `.java` file** |
| NFR-04 | Main class must be named exactly: `SuperSaverPOSGroup_BinaryMinds` |
| NFR-05 | File-level Javadoc comment must list all requirements and explain program functionality |
| NFR-06 | Every class and method must have a Javadoc comment (description + `@param` / `@return`) |
| NFR-07 | Code must be readable: meaningful names, inline comments, consistent formatting |
| NFR-08 | Plagiarism similarity must stay **below 20%** (TurnItIn checked) |

---

## 3. Data Storage Strategy

| Data Type | Format | Reason |
|-----------|--------|---------|
| Pending (paused) bills | Java Object Serialization (`.ser` file) | Preserves exact object state in memory for resumption |
| Finalized customer bills | PDF file | Professionally formatted, print-ready, tamper-evident |
| Revenue report (management) | Plain text / PDF attached to email | Easily viewable; standard attachment format |
| Product inventory | CSV file | Provided by lab; read-only at runtime |
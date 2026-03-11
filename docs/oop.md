# Super-Saving POS System — OOP Design

## 1. Class Architecture Overview

All classes live in a single file: `SuperSaverPOSGroup_BinaryMinds.java`.  
One class is `public` (the main driver). All others are package-private.

---

## 2. Core Entity Classes

These classes hold data and must implement `java.io.Serializable` so pending bills can be written to disk.

### `Product` — inventory item
```
Attributes
  - itemCode        : String
  - name            : String
  - price           : double
  - weightSize      : String
  - mfgDate         : LocalDate
  - expDate         : LocalDate
  - manufacturer    : String
  - discountPercent : double   // 0.0 – 75.0

Methods
  + getters / setters for all attributes
  + isExpired()     : boolean  // compares expDate to today
```

### `LineItem` — one row on the bill
```
Attributes
  - product        : Product
  - quantity       : double
  - netPrice       : double   // computed
  - discountAmount : double   // computed

Methods
  + calculateLineTotals()   // sets netPrice and discountAmount
  + getters for all attributes
```

### `Bill` — a full customer transaction
```
Attributes
  - billId        : String         // timestamp-based ID
  - cashierName   : String
  - branch        : String
  - customerName  : String         // empty string if not registered
  - items         : List<LineItem>
  - totalDiscount : double
  - totalCost     : double
  - printDateTime : LocalDateTime
  - isPending     : boolean

Methods
  + addItem(Product, double quantity)
  + removeItem(String itemCode)
  + calculateTotals()
  + getters / setters
```

---

## 3. System & Utility Classes

### `CsvLoader` — reads the product database
```
Methods
  + loadInventory(String filePath) : Map<String, Product>
      Parses each CSV row into a Product and maps it by itemCode.
      Throws: FileNotFoundException, IOException, InvalidDataException
```

### `BillSerializer` — saves and loads pending bills
```
Methods
  + savePending(Bill bill, String dirPath) : String  // returns file path
      Uses ObjectOutputStream to write bill to pending_bills/<billId>.ser
  + loadPending(String filePath) : Bill
      Uses ObjectInputStream to read and return the Bill object
      Throws: IOException, ClassNotFoundException
```

### `PdfGenerator` — produces the customer PDF receipt
```
Methods
  + generate(Bill bill, String outputDir) : String  // returns PDF path
      Writes a plain-text PDF layout using standard Java file I/O.
      (No third-party library needed — write formatted text to a .pdf file.)
```

### `ReportEngine` — revenue analysis and emailing
```
Methods
  + generateReport(LocalDate from, LocalDate to, List<Bill> bills) : String
      Filters bills by date range, sums revenue, returns formatted report text.
  + emailReport(String reportText) : void
      Opens an SMTP connection (or simulates it) to salesteam@supersaving.lk
      Throws: MessagingException (or caught internally with a console warning)
```

### `SuperSaverPOSGroup_BinaryMinds` — main driver (public class)
```
Methods
  + main(String[] args)         // entry point
  - showMenu()                  // prints cashier console menu
  - handleNewBill()
  - handleAddItem(Bill)
  - handleHoldBill(Bill)
  - handleResumeBill()
  - handleFinalizeBill(Bill)
  - handleRevenueReport()
```

---

## 4. OOP Relationships

```
SuperSaverPOSGroup_BinaryMinds
    uses ──► CsvLoader        (loads Map<String,Product> at startup)
    uses ──► BillSerializer   (hold / resume pending bills)
    uses ──► PdfGenerator     (finalize bill → PDF)
    uses ──► ReportEngine     (generate + email report)

Bill  ──has-a (composition)──► List<LineItem>
LineItem ──has-a (aggregation)──► Product
```

**Composition** — `LineItem` objects belong entirely to one `Bill`; they are created and destroyed with it.  
**Aggregation** — A `Product` exists independently in the inventory map; `LineItem` only references it.  
**Dependency** — `BillSerializer` depends on `java.io.ObjectOutputStream` / `ObjectInputStream`.

---

## 5. Exception Handling Plan

| Situation | Exception | Handling |
|-----------|-----------|----------|
| Item code not found in CSV | Custom `ItemNotFoundException` | Print error, prompt re-entry |
| CSV file missing or unreadable | `FileNotFoundException` | Fatal — print message, exit |
| Malformed CSV row | Custom `InvalidDataException` | Skip row, log warning, continue |
| Product expired | Custom `ExpiredProductException` | Warn cashier, still allow add |
| Serialization read/write failure | `IOException` | Print error, bill not saved/loaded |
| Email send failure | `Exception` (caught) | Print error, report shown on console |

### Custom Exception Classes

```java
class ItemNotFoundException extends Exception { ... }
class InvalidDataException extends Exception { ... }
class ExpiredProductException extends RuntimeException { ... }
```

---

## 6. Serialization Notes

- `Product`, `LineItem`, and `Bill` all implement `java.io.Serializable`.
- Each has a `serialVersionUID` constant to prevent version mismatch errors.
- Pending bills are stored in a `pending_bills/` folder relative to the working directory.
- The file name is `<billId>.ser` so multiple pending bills can coexist.
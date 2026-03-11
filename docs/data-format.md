# Super-Saving POS System — Data Formats & Serialization Guide

## Overview

This document explains **where data is stored**, **what format is used**, and **why** for each type of data in the system.

---

## 1. Product Inventory — CSV File

**File:** `products.csv` (provided by lab)  
**Access:** Read-only at startup

**Expected CSV columns:**

| Column | Type | Example |
|--------|------|---------|
| ItemCode | String | `GR001` |
| Name | String | `Organic Banana` |
| Price | double | `45.50` |
| WeightSize | String | `1 kg` |
| MfgDate | yyyy-MM-dd | `2026-02-01` |
| ExpDate | yyyy-MM-dd | `2026-03-15` |
| Manufacturer | String | `FreshFarm Ltd` |
| DiscountPercent | double | `10.0` |

**Loaded into:** `Map<String, Product>` (key = ItemCode) by `CsvLoader`.

---

## 2. Pending Bills — Java Object Serialization

**Folder:** `pending_bills/`  
**File name:** `<billId>.ser`  
**Format:** Binary (Java native serialization)

**Why serialization?**  
A pending bill is a live Java object (`Bill` containing `List<LineItem>`). Serialization freezes the exact state — including all calculated totals and item references — so it can be thawed back into memory exactly as it was left. No manual parsing needed.

**Classes that must implement `Serializable`:**
- `Bill`
- `LineItem`
- `Product`

All three must declare:
```java
private static final long serialVersionUID = 1L;
```

**Lifecycle:**
```
Cashier holds bill  →  BillSerializer.savePending()  →  pending_bills/B001.ser
Cashier resumes     →  BillSerializer.loadPending()  →  Bill object back in memory
```

---

## 3. Finalized Bills — PDF File

**Folder:** `bills/`  
**File name:** `<billId>.pdf`  
**Format:** Text written to a `.pdf` file (no external library)

**Why PDF?**  
The assignment explicitly requires PDF for the customer copy. PDFs preserve formatting and are print-friendly.

**What goes in the PDF:**

```
====================================
       SUPER-SAVING SUPERMARKET
====================================
Branch   : Colombo 07
Cashier  : Kavya Silva
Customer : Nimal Perera
Date     : 2026-03-10   Time: 14:32

ITEM            QTY   UNIT   DISC%  NET
--------------------------------------------
Organic Banana  2kg   45.50  10%    81.90
...
--------------------------------------------
Total Discount  :   Rs. 9.10
Total Cost      :   Rs. 81.90
====================================
```

Written using `FileWriter` / `PrintWriter` wrapped in a try-with-resources block.

---

## 4. Revenue Report — Plain Text (Emailed)

**Format:** Formatted plain text string  
**Sent to:** `salesteam@supersaving.lk`

**Report structure:**

```
SUPER-SAVING REVENUE REPORT
Period: 2026-03-01 to 2026-03-10
------------------------------------
Total Bills Processed : 42
Total Items Sold      : 318
Total Discount Given  : Rs. 4,250.00
Total Revenue         : Rs. 87,600.00
------------------------------------
Generated: 2026-03-10 21:45
```

**Email approach:**  
Use `javax.mail` if available on the classpath, otherwise catch the exception and print the report to console with a notice: `"Email unavailable — report printed to console."`

---

## 5. File & Folder Structure at Runtime

```
SuperSaverPOSGroup_BinaryMinds.java   ← single source file
products.csv                          ← product database
pending_bills/
    B001.ser
    B002.ser
bills/
    B003.pdf
    B004.pdf
```

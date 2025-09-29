# 💰 SQL Server Transactions with Error Handling

This project demonstrates **safe money transfer logic** in SQL Server using:

- ✅ Transactions (`BEGIN TRAN`, `COMMIT`, `ROLLBACK`)
- ✅ Error Handling (`TRY...CATCH`)
- ✅ Custom Errors (`THROW`)
- ✅ Row existence check (`@@ROWCOUNT`)

---

## 📌 Why Transactions?
Transactions ensure that multiple SQL statements are treated as **a single unit of work**.

- If **all succeed** → changes are permanently saved (`COMMIT`).
- If **any fail** → all changes are undone (`ROLLBACK`).

This prevents inconsistent data, especially in financial systems.

---

## 📌 Table Schema

```sql
CREATE TABLE accounts (
    id INT PRIMARY KEY,
    name NVARCHAR(50),

INSERT INTO accounts (id, name, balance) VALUES
(1, 'Ali', 5000.00),
(2, 'Sara', 3000.00),
(3, 'Ahmed', 7000.00);

BEGIN TRY
    BEGIN TRANSACTION;

    -- Step 1: Deduct from sender
    UPDATE accounts SET balance = balance - 1000 WHERE id = 1;
    IF @@ROWCOUNT = 0  
        THROW 50001, 'From Account not found!', 1;

    -- Step 2: Add to receiver
    UPDATE accounts SET balance = balance + 1000 WHERE id = 5;
    IF @@ROWCOUNT = 0  
        THROW 50002, 'To Account not found!', 1;

    -- Commit only if both updates succeed
    COMMIT;
END TRY
BEGIN CATCH
    -- Rollback on any failure
    ROLLBACK;
    PRINT 'Transaction failed. Changes rollback ho gaye.';
    PRINT ERROR_MESSAGE();
END CATCH;

📌 How It Works

Deducts money from sender account.

If account doesn’t exist → throws error.

Adds money to receiver account.

If account doesn’t exist → throws error.

If both succeed → COMMIT (permanent save).

If any step fails → ROLLBACK (undo all changes).

📌 Demo Scenarios

✅ Successful Transfer

-- Transfer 1000 from Ali (id=1) to Sara (id=2)


Ali → 5000 → 4000

Sara → 3000 → 4000

❌ Failed Transfer

-- Transfer 1000 from Ali (id=1) to id=99 (non-existing account)


Error: "To Account not found!"

Transaction rollback → balances unchanged.

📌 Benefits

Data consistency guaranteed.

No partial updates.

Safer financial operations.

Custom error messages for debugging.

📌 Best Practices

Always wrap critical operations in transactions.

Use @@ROWCOUNT to verify updates.

Raise custom errors with THROW.

Handle errors gracefully with TRY...CATCH.

✍️ Author: Daim Malik
📅 Created: September 2025

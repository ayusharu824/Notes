
VIEW VS STORED PROC

Need to QUERY data flexibly?         → View
Need to JOIN it with other tables?   → View
Need to hide sensitive columns?      → View

Need IF/ELSE logic?                  → Stored Procedure
Need to INSERT/UPDATE/DELETE?        → Stored Procedure
Need to accept input parameters?     → Stored Procedure
Need to run a complex workflow?      → Stored Procedure


Transactions
BEGIN TRANSACTION

BEGIN TRY

  -- Step 1: Deduct from Alice
  UPDATE accounts
  SET balance = balance - 10000
  WHERE acc_id = 1;

  -- Step 2: Add to Bob
  UPDATE accounts
  SET balance = balance + 10000
  WHERE acc_id = 2;

  -- If both succeed, save changes
  COMMIT;
  PRINT 'Transaction successful!';

END TRY

BEGIN CATCH

  -- If anything fails, undo everything
  ROLLBACK;
  PRINT 'Transaction failed! Changes rolled back.';
  PRINT ERROR_MESSAGE();

END CATCH



Diff between Rank & Dense_Rank() & Row_Number()
`RANK()` gives same rank to duplicates, but skips ranks afterward.

| Value | RANK() |
| ----- | ------ |
| 100   | 1      |
| 100   | 1      |
| 90    | 3      |
| 80    | 4      |
| 70    | 5      |
| 60    | 6      |
| 50    | 7      |
| 50    | 7      |

| Value | DENSE_RANK() |
| ----- | ------------ |
| 100   | 1            |
| 100   | 1            |
| 90    | 2            |
| 80    | 3            |
| 70    | 4            |
| 60    | 5            |
| 50    | 6            |
| 50    | 6            |
`DENSE_RANK()` gives same rank to duplicates, but does NOT skip numbers.

| Value | ROW_NUMBER() |
| ----- | ------------ |
| 100   | 1            |
| 100   | 2            |
| 90    | 3            |
| 80    | 4            |
| 70    | 5            |
| 60    | 6            |
| 50    | 7            |
| 50    | 8            |
|       |              |
|       |              |
|       |              |
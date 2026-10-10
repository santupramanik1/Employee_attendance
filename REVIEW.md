# REVIEW.md

| # | Where (function / line) | What is wrong | How you'd notice it (test, input, or symptom) | How you fixed it |
|---|---|---|---|---|
| 1 | `compute_late_minutes` | Returns 0 for times exactly on the grace period limit, incorrectly truncates seconds, and does not handle overnight shifts for the date. | Punch-in at 09:40:01 on a 09:30 shift returns 0 instead of 10. | Converted times to seconds and checked if the difference is strictly > 600. |
| 2 | `compute_work_hours` | Uses standard rounding instead of ROUND_HALF_UP. | 3.55 hours rounds to 3.5 (Banker's rounding) instead of 3.6. | Used `decimal.Decimal` with `ROUND_HALF_UP`. |
| 3 | `compute_overtime` | Fails to increment the date for overnight shifts, and records overtime under 30 minutes. | Overtime of 25 mins is recorded instead of 0. | Added 1 day to shift end if overnight, and enforced >= 30 check. |
| 4 | `create_employee` | Has a race condition by doing find_one then insert_one. Uses local naive datetime instead of UTC. | Concurrent creates bypass unique check. API returns datetime formatted string instead of epoch milliseconds. | Removed `find_one` and used a unique index `emp_code` catching `DuplicateKeyError`. Fixed the datetime object to UTC BSON date. |
| 5 | `list_employees` | `total` count ignores the `department` filter, returning the total for all employees. | Querying for a department shows `total` equal to the whole collection count. | Passed the query filter `q` into `count_documents(q)`. |
| 6 | `punch_in` | Uses local time instead of UTC. `date` calculation fails for overnight shifts. Race condition on `emp_code, date`. | `date` is wrongly assigned to the next day for a 22:00 shift. Concurrent punch-ins create multiple records. | Fixed naive datetimes to UTC. Extracted date via rule R1. Used unique index `(emp_code, date)` and caught `DuplicateKeyError`. |
| 7 | `list_attendance` | Pulls entire collection into memory. Sorts in Python instead of database. Returns `_id` as `id`. | A slow and memory-intensive query that times out on 100k docs. | Used `.skip()`, `.limit()`, and `.sort()` on the DB cursor. Removed the mapping to `id`. |

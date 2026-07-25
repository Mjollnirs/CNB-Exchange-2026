The SQL dump in ../database/reclaim-db.sql is the authoritative dataset — its
dates are computed at import time, so "today" data is always current.

These JSON files are a failsafe copy of the same records (dates as exported).
Use them only if you cannot import the SQL dump.

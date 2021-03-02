# Jore3

The mighty predecessor to Jore4 is connected to many systems and affects many business processes.

## Test database

To rotate a password for the jore3-test-db SQL server, connect to the test database and run:
```tsql
ALTER LOGIN user_name WITH PASSWORD = 'new_password' OLD_PASSWORD = 'old_password';
```

`OLD_PASSWORD` is required so that the current user does not need [`ALTER ANY LOGIN` privilege](https://docs.microsoft.com/en-us/sql/t-sql/statements/alter-login-transact-sql?view=sql-server-ver15#c-changing-the-password-of-a-login-when-logged-in-as-the-login).

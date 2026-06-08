## Evidence Screenshots

### Failed `su` Login Attempts

![Failed login attempts](failed.png)

This screenshot shows the failed `su` login attempts generated during the investigation.

---

### Failed Login Log Evidence Using `grep`

![Grep evidence](grep.png)

This screenshot shows the extracted failed login entries from `/var/log/auth.log` using the `grep` command.

---

### Successful `su` Login in Terminal

![Successful su login](pass.png)

This screenshot shows the successful `su` login and confirms that the session switched to the user `testuser`.

---

### Successful `su` Login Log Evidence

![Successful login log evidence](success.png)

This screenshot shows the log evidence for the successful `su` session. The log entry confirms that a session was opened for the user `testuser`.

---

### User-Related Log Evidence

![User evidence](user.png)

This screenshot shows user-related log evidence collected during the investigation.

---

### New User Account Creation Evidence

![New user evidence](newuser.png)

This screenshot shows evidence related to the creation of the new user account `newuser1`.

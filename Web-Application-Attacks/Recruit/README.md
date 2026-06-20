# TryHackMe: Recruit
![recruit screenshot](Evidence/image.png)

**Difficulty:** Medium | **Platform:** TryHackMe

## Objective
Exploit web vulnerabilities in a recruitment portal to escalate privileges and obtain administrative access.

## Attack Chain
1. **Reconnaissance:** Identified an exposed `/file.php` endpoint via directory enumeration and `sitemap.xml`.
2. **Local File Inclusion (LFI):** Bypassed a weak RFI filter using the `file://` wrapper (`?cv=file:///var/www/html/config.php`) to extract hardcoded HR credentials.
3. **SQL Injection (SQLi):** Authenticated as HR and identified a boolean-based SQL vulnerability in the candidate search feature.
4. **Data Exfiltration:** Leveraged a UNION-based injection (`ORDER BY 4` -> `UNION SELECT`) to dump the `users` table and extract the administrator password.

## Key Vulnerabilities & Remediations
* **Local File Inclusion (LFI):** The application passes user input directly to file-reading functions. 
  * **Fix:** Use indirect object references (IDs mapped to files) or strictly validate input against a hardcoded allowlist, stripping URI wrappers and directory traversal characters.
* **SQL Injection (SQLi):** The search feature concatenates user input directly into backend MySQL queries. 
  * **Fix:** Implement Parameterized Queries (Prepared Statements) to securely separate executable SQL code from user-supplied data.
* **Sensitive Data Exposure:** Hardcoded credentials were left in the web root.
  * **Fix:** Store secrets in secure environment variables outside of the `/var/www/html/` directory.
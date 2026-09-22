# Individual Assignment II: Oracle Pluggable Databases (PDB) Management

## Required Submission Details Block
- **Student Name:** Shema Can Daddy Nathan
- **Registration Number:** 20251SEN067
- **Course:** Database Development with PL/SQL (INSY 8311)
- **Group:** B
- **Repository Link:** https://github.com/ShemaDaddy/oracle_pdb_ass_II_20251SEN067_shema
- **PDB Name Created:** sh_pdb_20251SEN067
- **PDB User Created:** shema_plsqlauca_20251SEN067
- **Issues Encountered:** No

---

## 1. Overview
This project demonstrates multitenant architecture administration in Oracle Database 21c Express Edition (XE). The tasks cover the complete lifecycle of Pluggable Databases (PDBs), including configuration using Oracle Managed Files (OMF), creation and provisioning of persistent PDBs, user access management, temporary PDB creation and deletion with file cleanup, and monitoring via Oracle Enterprise Manager (OEM) Express.

---

## 2. Oracle Environment Used
- **Database Engine:** Oracle Database 21c Express Edition (XE) x64 (Multitenant Architecture)
- **Container Database (CDB):** `CDB$ROOT`
- **Command Line Tool:** SQL*Plus (Windows Command Prompt)
- **Management Interface:** Oracle Enterprise Manager (OEM) Express

---

## 3. Tasks Executed & Explanation

### Task 0: Oracle Managed Files (OMF) Pre-Configuration
To ensure automated file path management and prevent path conversion errors (`ORA-65005`), Oracle Managed Files (OMF) was enabled at the system level before performing PDB operations.

```sql
ALTER SYSTEM SET DB_CREATE_FILE_DEST = 'C:\APP\HP\PRODUCT\21C\ORADATA\XE' SCOPE=BOTH;
```

---

### Task 1: Create a New Pluggable Database & User Creation

#### Step 1.1: Permanent PDB Creation
The primary PDB `sh_pdb_20251SEN067` was instantiated using OMF from the seed database template (`PDBSEED`).

```sql
CREATE PLUGGABLE DATABASE sh_pdb_20251SEN067
  ADMIN USER pdb_admin IDENTIFIED BY MySecurePass123;
```
* **Execution Evidence:**  
![PDB Creation Command](screenshots/pdb_creation/01_create_pdb.png)

#### Step 1.2: Opening and Verifying PDB State
The PDB was opened in `READ WRITE` mode and verified using `SHOW PDBS`.

```sql
ALTER PLUGGABLE DATABASE sh_pdb_20251SEN067 OPEN;
SHOW PDBS;
```
* **Execution Evidence:**  
![PDB Open State](screenshots/pdb_creation/02_open_pdb.png)

#### Step 1.3: User Provisioning Inside PDB
Session context was shifted to `sh_pdb_20251SEN067` to create the persistent database user `shema_plsqlauca_20251SEN067` with administrative privileges.

```sql
ALTER SESSION SET CONTAINER = sh_pdb_20251SEN067;

CREATE USER shema_plsqlauca_20251SEN067 IDENTIFIED BY MySecurePass123;
GRANT CONNECT, RESOURCE, DBA TO shema_plsqlauca_20251SEN067;
```
* **Execution Evidence:**  
![User Creation inside PDB](screenshots/pdb_creation/03_create_user.png)

---

### Task 2: Create and Delete a Temporary PDB

#### Step 2.1: Temporary PDB Creation
Switched back to `CDB$ROOT` and created a temporary PDB named `sh_to_delete_pdb_20251SEN067`.

```sql
ALTER SESSION SET CONTAINER = CDB$ROOT;

CREATE PLUGGABLE DATABASE sh_to_delete_pdb_20251SEN067
  ADMIN USER temp_admin IDENTIFIED BY MySecurePass123;

SHOW PDBS;
```
* **Execution Evidence:**  
![Temporary PDB Creation](screenshots/pdb_deletion/04_create_temp_pdb.png)

#### Step 2.2: Complete PDB Deletion & File Cleanup
The temporary PDB was closed immediately and completely dropped including its underlying OS data files using `INCLUDING DATAFILES`.

```sql
ALTER PLUGGABLE DATABASE sh_to_delete_pdb_20251SEN067 CLOSE IMMEDIATE;
DROP PLUGGABLE DATABASE sh_to_delete_pdb_20251SEN067 INCLUDING DATAFILES;

SHOW PDBS;
```
* **Execution Evidence:**  
![Temporary PDB Deletion](screenshots/pdb_deletion/05_delete_temp_pdb.png)

---

### Task 3: Oracle Enterprise Manager (OEM) Setup & Monitoring

The built-in web management interface was configured to monitor the multitenant environment.

1. Configured HTTPS port for Oracle XML DB (XDB):
   ```sql
   EXEC DBMS_XDB_CONFIG.SETHTTPSPORT(5500);
   ```
2. Accessed the dashboard via `https://localhost:5500/em` using `SYSDBA` credentials.

* **Execution Evidence:**  
![OEM Express Dashboard](screenshots/oem_dashboard/06_oem_dashboard.png)

---

## 4. Challenges Faced & Technical Solutions

1. **Path Mapping Error (`ORA-65005`):**  
   * *Issue:* Manual path specification (`FILE_NAME_CONVERT`) failed due to path mismatch with local Oracle 21c installation directories.  
   * *Solution:* Configured Oracle Managed Files (OMF) via `DB_CREATE_FILE_DEST` parameter, allowing Oracle to handle file allocation and deletion automatically.

2. **Upgrading from Legacy 11g to Multitenant 21c Architecture:**  
   * *Issue:* Oracle 11g Express Edition lacks container/pluggable architecture support.  
   * *Solution:* Upgraded database environment to Oracle Database 21c XE x64 to meet assignment specifications.

---

## 5. Academic Integrity Statement

I hereby declare that all technical tasks, SQL command executions, and documentation presented in this repository represent my own individual effort and work without unauthorized assistance.

**Name:** Shema Can Daddy Nathan  
**Student ID:** 20251SEN067  

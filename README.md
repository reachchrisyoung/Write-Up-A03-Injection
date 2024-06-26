# OWASP Top 10 - A03:2021 - Injection Write-Up

## Topic: Injection

### Notable Common Weakness Enumerations (CWEs) include:

+ CWE079: Cross-site Scripting
+ CWE-89: SQL Injection
+ CWE–73: External Control of File Name or Path

<br />
<br />
<br />

### Table of Contents

+ [Overview](https://github.com/reachchrisyoung/Write-Up-A03-Injection/tree/main?tab=readme-ov-file#table-of-contents)
+ [Common Injections](https://github.com/reachchrisyoung/Write-Up-A03-Injection/tree/main?tab=readme-ov-file#overview)
+ [How to Detect Vulnerabilities](https://github.com/reachchrisyoung/Write-Up-A03-Injection/tree/main?tab=readme-ov-file#common-injections)
+ [How to Identify Flaws Before Production Deployment](https://github.com/reachchrisyoung/Write-Up-A03-Injection/tree/main?tab=readme-ov-file#how-to-detect-vulnerabilities)
+ [How to Prevent Injection Exploits](https://github.com/reachchrisyoung/Write-Up-A03-Injection/tree/main?tab=readme-ov-file#how-to-identify-flaws-before-production-deployment)
+ [Example Attack Scenarios](https://github.com/reachchrisyoung/Write-Up-A03-Injection/tree/main?tab=readme-ov-file#option-4-use-limit-and-other-sql-controls-within-queries)
+ [Review of Sample Attack Scenarios](https://github.com/reachchrisyoung/Write-Up-A03-Injection/tree/main?tab=readme-ov-file#review-of-sample-attack-scenario-1--attack-scenario-2)
+ [References](https://github.com/reachchrisyoung/Write-Up-A03-Injection/tree/main?tab=readme-ov-file#references)

<br />
<br />
<br />

### Overview

An application is vulnerable to an attack when:

(1) User-supplied data is…


(a) not validated by the application,


(b) not filtered by the application, or…


(c) not sanitized by the application.


(2) Dynamic queries or non-parameterized calls without context-aware escaping are used 
      directly in the interpreter.

(3) Hostile data is used within object-relational mapping (ORM) search parameters to extract 
      additional, sensitive records.

(4) Hospital data is directly [(a) used or (b) concatenated].  The SQL or command contains the 
      structure and malicious data in dynamic queries, commands, or stored procedures.  

<br />
<br />
<br />

### Common Injections

Some of the more common injections are:

(1) SQL

(2) NoSQL

(3) OS command

(4) Object Relational Mapping (ORM) 

(5) LDAP

(6) Expression Language (EL) or Object Graph Navigation Library (ONGL) injection.

NOTE: The concept is identical to all interpreters.

<br />
<br />
<br />

### How to Detect Vulnerabilities

Best method of detecting if applications are vulnerable to injections: <b>Source Code Reviews.</b>

<b>Detection Automation</b>

Strongly encouraged detection strategy: <b>Automated Testing of all data inputs</b>, including…

(1) Parameters

(2) Headers

(3) URL

(4) Cookies

(5) JSON

(6) SOAP 

…and…

(7) XML

<br />
<br />
<br />

### How to Identify Flaws Before Production Deployment

To identify introduced injection flaws before production deployment, organizations can include the following in their CI/CD pipeline:

(1) Static application security testing tools (SAST),

(2) Dynamic application security testing tools (DAST), and…

(3) Interactive application security testing tools (IAST).

<br />
<br />
<br />

### How to Prevent Injection Exploits

Preventing injection requires keeping data separate from commands & queries:

#### Option 1 (THE PREFERRED OPTION): Use a Safe API

Use a safe API, which… 	(a) avoids using the interpreter entirely,

(b) provides a parameterized interface, or…

(c) migrates to Object Relational Mapping Tools (ORMs).  

NOTE: Even when parameterized, stored procedures can still introduce SQL injection if PL/SQL or T-SQL concatenates queries and data or executes hostile data with EXECUTE IMMEDIATE or exec().  


#### Option 2: Positive Server-Side Input Validation

NOTE: This is <b>not</b> a complete defense.

	The reason for positive server-side input validation failing to act as a complete defense is because many applications require special characters, such as text areas or APIs for mobile applications.  

#### Option 3: Escape special characters using the specific escape syntax for that interpreter.

NOTE: This is for any residual dynamic queries.

	SQL structures such as table names, column names, and so on, cannot be escaped, and thus user-supplied structure names are dangerous.  This is a common issue in report-writing software.  

#### OPTION 4: Use LIMIT and other SQL controls within queries.

The aim here is to prevent mass disclosure of records in case of SQL injection.

<br />
<br />
<br />

### Example Attack Scenarios

#### Attack Scenario #1

An application uses untrusted data in the construction of the following vulnerable SQL call:

<b>Sample Attack Scenario #1 Code:</b>

String query = "SELECT \* FROM accounts WHERE custID='" + request.getParameter("id") + "'";
Verbal Translation of the Code:</b>

<b>Verbal Translation of the Code</b>

String Query = SELECT \* FROM accounts WHERE custID=[apostraphe][quotes] + request.getParameter([quotes]id[quotes]) + [quotes][apostraphe][quotes][semi-colon]

<b>Detailed Verbal Translation of the Code:</b>

String Query [equalsign] SELECT [backslash][asterisk] FROM accounts WHERE custID=[apostraphe][quotes] + request[period]getParameter[openparenthesis][quotes]id[quotes][closeparenthesis] [additionsign] [quotes][apostraphe][quotes][semi-colon]

<b>Very Detailed Verbal Translation of the Code:</b>

String[space]Query[space][equalsign][space]SELECT[space][backslash][asterisk][space]FROM[space]accounts[space]WHERE[space]custID=[apostraphe][quotes][space]+[space]request[period]getParameter[openparenthesis][quotes]id[quotes][closeparenthesis][space][additionsign][space][quotes][apostraphe][quotes][semi-colon]



#### Attack Scenario #2

Similarly, an application’s blind trust in frameworks may result in queries that are still vulnerable, (e.g., Hibernate Query Language (HQL)): 

<b>Sample Attack Scenario #2 Code:</b>

Query HQLQuery = session.createQuery("FROM accounts WHERE custID='" + request.getParameter("id") + "'");

<b>Verbal Translation of the Code:</b>

Query HQLQuery = session.createQuery(“FROM accounts WHERE custID=[apostraphe][quotes] + request.getParameter(“id” + [quotes][apostraphe][quotes]);

<b>Detailed Verbal Translation of the Code:</b>

Query HQLQuery [equalsign] session[period]createQuery[openparenthesis][quotes]FROM accounts WHERE custID[equalsign][apostraphe][quotes] [additionsign] request[period]getParameter[openparenthesis][quotes]id[quotes][closeparenthesis] [additionsign] [quotes][apostraphe][quotes][closeparenthesis][semi-colon]

<b>Very Detailed Verbal Translation of the Code:</b>

Query[space]HQLQuery[space][equalsign][space]session[period]createQuery[openparenthesis][quotes]FROM[space]accounts[space]WHERE[space]custID[equalsign][apostraphe][quotes][space][additionsign][space]request[period]getParameter[openparenthesis][quotes]id[quotes][closeparenthesis][additionsign][space][quotes][apostraphe][quotes][closeparenthesis][semi-colon]

<b>Restatement of SQL lines from Attack Scenario #1 & Attack Scenario #2</b>

Sample Attack Scenario #1 Code:

String query = "SELECT \* FROM accounts WHERE custID='" + request.getParameter("id") + "'";

Sample Attack Scenario #2 Code:

Query HQLQuery = session.createQuery("FROM accounts WHERE custID='" + request.getParameter("id") + "'");

<br />
<br />
<br />

### Review of Sample Attack Scenario #1 & Attack Scenario #2

In both cases above, the attacker modifies the ‘id’ parameter value in their browser to send: ‘ UNION SLEEP(10);- <br />

<br />
For instance… 
<br /> 
<br />

http://[nospace]example[dot]com/app/accountView?id=' UNION SELECT SLEEP(10);--

What this does: The code changes the meaning of both queries to return all the records from the accounts table.  

More dangerous attacks could: Modify or delete data or even invoke stored procedures.

<br />
<br />
<br />

### References

A03 - Injection.  OWASP Website.  Retrieved from [https://owasp.org/Top10/A03_2021-Injection/](https://owasp.org/Top10/A03_2021-Injection/)


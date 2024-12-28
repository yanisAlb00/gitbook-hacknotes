# SQLMap Output Description

## Log Messages Description

"target URL content is stable"
"GET parameter 'id' appears to be dynamic"

Parameter might be vulnerable to XSS attacks
"heuristic (XSS) test shows that GET parameter 'id' might be vulnerable to cross-site scripting (XSS) attacks"

Back-end DBMS is '...'
"it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n]"

Level/risk values
"for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n]"

Reflective values found
"reflective value(s) found and filtering out"

Parameter appears to be injectable
"GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="luther")"

Time-based comparison statistical model
"time-based comparison requires a larger statistical model, please wait........... (done)"

"GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N]"

Sqlmap identified injection points
"sqlmap identified the following injection point(s) with a total of 46 HTTP(s) requests:"

Data logged to text files
"fetched data logged to text files under '/home/user/.sqlmap/output/www.example.com'"

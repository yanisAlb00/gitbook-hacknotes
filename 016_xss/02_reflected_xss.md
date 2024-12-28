# Reflected XSS

2 types of Non-Persistent XSS vulnerabilities: 
- Reflected XSS, which gets processed by the back-end server
- DOM-based XSS, which is completely processed on the client-side and never reaches the back-end server

test -> returns an error

If the input is not sanitize, we can try to execute code into the error code we get back

XSS Testing Payloads
<script>alert(window.origin)</script>

Response :

<div></div><ul class="list-unstyled" id="todo"><div style="padding-left:25px">Task '<script>alert(window.origin)</script>' could not be added.</div></ul>

But if the XSS vulnerability is Non-Persistent, how would we target victims with it?

- Check this through the Firefox Developer Tools by clicking [CTRL+I] and selecting the Network tab
- We can see that the first row shows that our request was a GET request
- So, to target a user, we can send them a URL containing our payload

## LAB

Input : test
Task 'test' could not be added.

<script>alert(document.cookie)</script>
cookie=HTB{r3fl3c73d_b4ck_2_m3}

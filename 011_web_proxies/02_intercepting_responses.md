# Intercepting Responses

## Burp
Proxy>Options : enabling Intercept Response under Intercept Server Responses:

Examples of change in source code specification : 
type="number" to type="text"
maxlength="3" to maxlength="100"

Proxy>Options>Response Modification
Unhide hidden form fields

Comments button

## ZAP

Click on Step : it will send the request and automatically intercept the response

Examples of actions :
enable disabled input fields or show hidden input fields, then we can click on the third button on the left (the light bulb icon)

Comments button
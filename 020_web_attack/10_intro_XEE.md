# Intro to XXE : XML External Entity

XML External Entity (XXE) Injection vulnerabilities occur when XML data is taken from a user-controlled input without properly sanitizing or safely parsing it, which may allow us to use XML features to perform malicious actions.

## XML

tags for root and child elements

<?xml version="1.0" encoding="UTF-8"?>
<email>
  <date>01-01-2022</date>
  <time>10:00 am UTC</time>
  <sender>john@inlanefreight.com</sender>
  <recipients>
    <to>HR@inlanefreight.com</to>
    <cc>
        <to>billing@inlanefreight.com</to>
        <to>payslips@inlanefreight.com</to>
    </cc>
  </recipients>
  <body>
  Hello,
      Kindly share with me the invoice for the payment made on January 1, 2022.
  Regards,
  John
  </body> 
</email>

| Key         | Example                                | Definition                                                                                                    |
|-------------|----------------------------------------|---------------------------------------------------------------------------------------------------------------|
| Tag         | <date>                                 | The keys of an XML document, usually wrapped with (</>) characters.                                           |
| Entity      | &lt;                                   | XML variables, usually wrapped with (&/;) characters.                                                         |
| Element     | <date>01-01-2022</date>                | The root element or any of its child elements, and its value is stored in between a start-tag and an end-tag. |
| Attribute   | version="1.0"/encoding="UTF-8"         | Optional specifications for any element that are stored in the tags, which may be used by the XML parser.     |
| Declaration | <?xml version="1.0" encoding="UTF-8"?> | Usually the first line of an XML document, and defines the XML version and encoding to use when parsing it.   |

## XML DTD

XML Document Type Definition (DTD) allows the validation of an XML document against a pre-defined document structure

<!DOCTYPE email [
  <!ELEMENT email (date, time, sender, recipients, body)>
  <!ELEMENT recipients (to, cc?)>
  <!ELEMENT cc (to*)>
  <!ELEMENT date (#PCDATA)>
  <!ELEMENT time (#PCDATA)>
  <!ELEMENT sender (#PCDATA)>
  <!ELEMENT to  (#PCDATA)>
  <!ELEMENT body (#PCDATA)>
]>

## Place XML DTD in XML declaration

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "email.dtd">

OR Reference a DTD through a URL

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "http://inlanefreight.com/email.dtd">

## XML Entities

Define custom entities (i.e. XML variables) in XML DTDs, to allow refactoring of variables and reduce repetitive data :

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>

OR Reference External XML Entities with the SYSTEM keyword :

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "http://localhost/company.txt">
  <!ENTITY signature SYSTEM "file:///var/www/html/signature.txt">
]>


--> Possible to use PUBLIC keyword instead of SYSTEM

## XXE Prevention

- Avoiding Outdated Components
- Using Safe XML Configurations
Disable referencing custom Document Type Definitions (DTDs)
Disable referencing External XML Entities
Disable Parameter Entity processing
Disable support for XInclude
Prevent Entity Reference Loops

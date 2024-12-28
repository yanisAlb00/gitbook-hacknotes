# HTTP Verb Tampering

Depending on the web server configuration, web applications may be scripted to accept certain HTTP methods for their various functionalities and perform a particular action based on the type of the request.

| Verb    | Description                                                                                       |
|---------|---------------------------------------------------------------------------------------------------|
| HEAD    | Identical to a GET request, but its response only contains the headers, without the response body |
| PUT     | Writes the request payload to the specified location                                              |
| DELETE  | Deletes the resource at the specified location                                                    |
| OPTIONS | Shows different options accepted by a web server, like accepted HTTP verbs                        |
| PATCH   | Apply partial modifications to the resource at the specified location                             |

## Insecure Configurations

<Limit GET POST>
    Require valid-user
</Limit>

## Insecure Coding

$pattern = "/^[A-Za-z\s]+$/";

if(preg_match($pattern, $_GET["code"])) {
    $query = "Select * from ports where port_code like '%" . $_REQUEST["code"] . "%'";
    ...SNIP...
}

Sanitization only on $_GET["code"] and not on $_REQUEST["code"]

Attacker could perform SQL injection using POST parameters

## Prevention 

Insecure Configuration :
<system.web>
    <authorization>
        <allow verbs="GET" roles="admin">
            <deny verbs="GET" users="*">
        </deny>
        </allow>
    </authorization>
</system.web>

Insecure Coding :
Using $_REQUEST and not $_POST and $_GET
# Intro to File Inclusions

Templating engine  : displays a page that shows the common static parts, such as the header, navigation bar, and footer

/index.php?page=about
--> dynamic content is read from a file called about.php

## Examples of Vulnerable Code

If the code does not explicitly filter and sanitize the user input :

### PHP

if (isset($_GET['language'])) {
    include($_GET['language']);
}

Other functions : include_once(), require(), require_once(), file_get_contents()

### NodeJS

if(req.query.language) {
    fs.readFile(path.join(__dirname, req.query.language), function (err, data) {
        res.write(data);
    });
}

app.get("/about/:language", function(req, res) {
    res.render(`/${req.params.language}/about.html`);
});

### Java

<c:if test="${not empty param.language}">
    <jsp:include file="<%= request.getParameter('language') %>" />
</c:if>

<c:import url= "<%= request.getParameter('language') %>"/>

### .NET

@if (!string.IsNullOrEmpty(HttpContext.Request.Query['language'])) {
    <% Response.WriteFile("<% HttpContext.Request.Query['language'] %>"); %> 
}

@Html.Partial(HttpContext.Request.Query['language'])

<!--#include file="<% HttpContext.Request.Query['language'] %>"-->

## Read vs Execute

Executing files may lead us to code execution

Function	Read Content	Execute	Remote URL

PHP			
include()/include_once()	✅	✅	✅

require()/require_once()	✅	✅	❌

file_get_contents()	✅	❌	✅

fopen()/file()	✅	❌	❌

NodeJS			

fs.readFile()	✅	❌	❌

fs.sendFile()	✅	❌	❌

res.render()	✅	✅	❌

Java			

include	✅	❌	❌

import	✅	✅	✅

.NET			

@Html.Partial()	✅	❌	❌

@Html.RemotePartial()	✅	❌	✅

Response.WriteFile()	✅	❌	❌

include	✅	✅	✅



# DNS

# Commands


## Querying: A Records for the whole domain

    **nslookup**
    export TARGET="facebook.com"
    nslookup $TARGET

    **dig**
    dig facebook.com @1.1.1.1

## Querying: A Records for a subdomain

    **nslookup**
    export TARGET=www.facebook.com
    nslookup -query=A $TARGET

    **dig**
    dig a www.facebook.com @1.1.1.1

## Querying: PTR Records for an IP Address

    **nslookup**
    nslookup -query=PTR 31.13.92.36

    **dig**
    dig -x 31.13.92.36 @1.1.1.1

## Querying: ANY Existing Records

    **nslookup**
    export TARGET="google.com"
    nslookup -query=ANY $TARGET

    **dig**
    dig any google.com @8.8.8.8
    dig any cloudflare.com @8.8.8.8

## Querying: TXT Records

    **nslookup**
    export TARGET="facebook.com"
    nslookup -query=TXT $TARGET

    **dig**
    dig txt facebook.com @1.1.1.1

## Querying: MX Records

    **nslookup**
    export TARGET="facebook.com"
    nslookup -query=MX $TARGET

    **dig**
    dig mx facebook.com @1.1.1.1

We can combine some of the results gathered via nslookup with the whois database to determine if our target organization uses hosting providers :

nslookup facebook.com

-> result : Address: 157.240.199.35

whois 157.240.199.35

# Encoding

Burp Repeater :
Convert Selection>URL>URL encode key characters
Selecting the text and [CTRL+U]

ZAP :
Should automatically URL-encode all of our request data

# Decoding

The following are some of the other types of encoders supported by both tools:

HTML
Unicode
Base64
ASCII hex

Burp : Decoder tab

ZAP : Encoder/Decoder/Hash by clicking [CTRL+E]. With these encoders, we can input any text and have it quickly encoded or decoded. 

For example cookie that is base64 encoded :
eyJ1c2VybmFtZSI6Imd1ZXN0IiwgImlzX2FkbWluIjpmYWxzZX0=

We can input the above string in Burp Decoder and select Decode as > Base64

-> Burp Inspector : In both proxy and repeater
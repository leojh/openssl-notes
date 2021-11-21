## Commands

* Generate RSA private key

```
openssl genrsa
```

* Generate encrypted private key to a file
`-aes256` flag is what encrypts the private key

```
openssl genrsa -aes256 -out private.pem
```

* Extract the public key from the private key file created above

```
openssl rsa -in private.pem -outform PEM -pubout -out public.pem
```

* Create random number represented as hex of lengh n bytes
`openssl rand -hex 16

## Create a self-signed certificate

```
openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -subj '/CN=www.example.com' -keyout server.key -out server.crt
```

`req` -  new certificate request\
`new` - new cert\
`newkey rsa:2048` - create new private key of length\
`days` - n days until expiration\
`nodes` - no encryption (noDES)\
`x509` - x509 cert file and format\
`subj` - the min cert data required\
`keyout` - the private key file\
`out` - the x509 cert file

This will create a cert file with public key a private key. The cert is self signed so the `X509v3 Subject Key Identifier` and the `X509v3 Authority Key Identifier` are the same and `Issuer` and `Subject` are the same.

It is also a Certificate Authority. `CA:TRUE`

* Reading certificate in the console to see all its properties
```
openssl x509 -text -in server.crt -noout
```

## Read a p12 file
```
#nodes will not encrypt the private key so it will be exposed
openssl pkcs12 -info -in FILE.p12 -nodes
```

`-nodes` flag to encrypt or not encrypt the private key
`-nokeys` will not extract any private keys
`-nocerts` will not extract any certs
`-out` outputs in PEM format

`pkcs12` by default exports private keys as `PKCS8`. To export as `PKCS1`, pipe export like:
```
#rsa
openssl pkcs12 -info -in FILE.p12 -nodes -nocerts | openssl rsa -out OUTFILE.key

#ecdsa
openssl pkcs12 -info -in FILE.p12 -nodes -nocerts | openssl ec -out OUTFILE.key
```

`PKCS1` will specify the algorithm on first line of file. `BEGIN RSA PRIVATE KEY`
`PKCS8` will not specify algorithm `BEGIN PRIVATE KEY` and may also be encrypted `BEGIN ENCRYPTED PRIVATE KEY`

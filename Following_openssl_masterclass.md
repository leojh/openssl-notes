Based on https://www.youtube.com/watch?v=d8OpUcHzTeg&t=921s

* Start clean by deleting all private keys `*.key`, certs `*.crt`, CSRs `*.csr`, and database items (`index`, `index.*`, and `newcerts/*`). Not starting clean will cause errors.

## Creating Root CA
```
#go to root of project / ca
cd ca
```
* Creating private keys for root CA, intermediate CA, and server
```
openssl genrsa -aes256 -out root-ca/private/ca.key 4096

openssl genrsa -aes256 -out sub-ca/private/sub.key 4096

#No encrypted key and smaller key size because for server because of high traffic. balance between performance and security.
openssl genrsa -out server/private/server.key 2048
```
* Generate public key cert from the private key with config file
```
cd root-ca
openssl req -config root-ca.conf -key private/ca.key -new -x509 -days 7500 -sha256 -extensions v3_ca -out certs/ca.crt

#Use "RootCA" as Common Name

openssl x509 -noout -in certs/ca.crt -text
```

## Create a Signing Request (CSR)
Not creating a cert but a CSR based on the private key of `sub-ca`
CSRs can be kept and reused to reissue a cert when it expires
```
cd sub-ca
openssl req -config sub-sa.conf -new -key private/sub-ca.key -sha256 -out csr/sub-ca.csr
```

## Create a Certificate from CSR (Intermediate CA)
```
cd root-ca
openssl ca -config root-ca.conf -extensions v3_intermediate_ca -days 3650 -notext -in ../sub-ca/csr/sub-ca.csr -out ../sub-ca/certs/sub-ca.crt
```

## Create CSR for Server
```
cd server
openssl req -key private/server.key -new sha256 out -csr/server.csr
cd ../
```

## Create Server Cert from CSR
```
cd sub-ca
openssl ca -config sub-ca.conf -extensions server_cert -days 365 -notext -in ../server/csr/server.csr -out ../server/certs/server.crt
```
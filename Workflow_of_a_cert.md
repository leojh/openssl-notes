Certs are normally represented in `x509` format. They contain 3 pieces of information
* The public key (derived from the private key which is kept secure)
* A digital signature (signed by the issuers's private key). The digital signature can be verified by the public key.
* Information about the Issuer, the Subject, version, validity dates, etc.

https://www.ssl.com/faqs/what-is-an-x-509-certificate/

A certificate is created by:
* Self singning it with a private key
* Creating a CSR from a private key and submitting that CSR to a CA which then signs it with a public key and creates your certificate
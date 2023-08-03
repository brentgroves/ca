https://www.golinuxcloud.com/openssl-generate-csr-create-san-certificate/
In this article we will learn the steps to create SAN Certificate using openssl generate csr with san command line and openssl sign csr with subject alternative name.

I have already written multiple articles on OpenSSL, I would recommend you to also check them for more overview on openssl examples:

Beginners guide to understand all Certificate related terminologies used with openssl
Generate openssl self-signed certificate with example
Create your own Certificate Authority and generate a certificate signed by your CA
Create certificate chain (CA bundle) using your own Root CA and Intermediate Certificates with openssl
Create server and client certificates using openssl for end to end encryption with Apache over SSL
Create SAN Certificate to protect multiple DNS, CN and IP Addresses of the server in a single certificate
 

In the previous article where we created server and client certificates using openssl. we ended up with a situation where if we use a different server name then the client server TCP handshake fails. To fix this we have two approaches

Create separate server certificates for every other IP Address or Hostname which can be expensive
Create SAN certificates (Subject Alternative Name)

Just to recap our exercise, earlier when we tried to connect to our webserver using IP Address instead of hostname then we received "curl: (51) SSL: certificate subject name 'centos8-3' does not match target host name '10.10.10.17'" because we had created our client certificate using centos8-3 as the Common Name for client.csr

curl --key private/client.key.pem  --cert certs/client.cert.pem --cacert intermediate/certs/ca-chain-bundle.cert.pem  https://10.10.10.17:8443 -v
* Rebuilt URL to: https://10.10.10.17:8443/
*   Trying 10.10.10.17...
* TCP_NODELAY set
* Connected to 10.10.10.17 (10.10.10.17) port 8443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
*   CAfile: intermediate/certs/ca-chain-bundle.cert.pem
  CApath: none
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, [no content] (0):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, [no content] (0):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, [no content] (0):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, [no content] (0):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.3 (OUT), TLS handshake, [no content] (0):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use http/1.1
* Server certificate:
*  subject: C=IN; ST=Some-State; L=BANGALORE; O=GoLinuxCloud; CN=centos8-3
*  start date: Apr  9 01:49:53 2020 GMT
*  expire date: Apr  9 01:49:53 2021 GMT
* SSL: certificate subject name 'centos8-3' does not match target host name '10.10.10.17'
* Closing connection 0
* TLSv1.3 (OUT), TLS alert, [no content] (0):
* TLSv1.3 (OUT), TLS alert, close notify (256):
curl: (51) SSL: certificate subject name 'centos8-3' does not match target host name '10.10.10.17'

What is SAN Certificate?
A (Subject Alternative Name) SAN certificate can be used on multiple domain names, for example, abc.com or xyz.com, where the domain names are completely different, but they can use the same certificate.
SAN can have multiple common names associated with the certificate.
This is useful when the server runs multiple services and therefore will use multiple names.
For example, I could have a SAN certificate for my Exchange server that holds the names mail.golinuxcloud.com and server.golinuxcloud.com.
Without the use of a SAN certificate, I would need to purchase multiple single common name certificates.
We define a list of IP Address, DNS values which will be used as Common Name for certificate validation when we create CSR using openssl.

Configure openssl x509 extension to create SAN certificate
Before we create SAN certificate we need to add some more values to our openssl x509 extensions list. We must openssl generate csr with san command line using this external configuration file.
Here we have added a new field subjectAtlName, with a key value of @alt_names.
Next under [alt_names], I will provide the complete list of IP Address and DNS name which the server certificate should resolve when validating a client request.
So with subjectAltName instead of defining single Common Name, now we can define a whole list of hostnames for which our server certificate will be valid.

cat server_cert_ext.cnf
basicConstraints = CA:FALSE
nsCertType = server
nsComment = "OpenSSL Generated Server Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer:always
keyUsage = critical, digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
[alt_names]
IP.1 = 10.10.10.13
IP.2 = 10.10.10.14
IP.3 = 10.10.10.17
IP.4 = 192.168.43.104
DNS.1 = centos8-3.example.com

# Generate server private key
You can ignore this step if you already have a private key. For the sake of demonstration I am creating a new server private key.
bash
openssl genpkey -des3 -pass file:/home/brent/src/ca/mypass.enc -algorithm RSA -out ~/src/ca/intermediateCA/private/moto.busche-cnc.com.san.key.pem                     

# Openssl Generate CSR with SAN command line
Now to create SAN certificate we must generate a new CSR i.e. Certificate Signing Request which we will use in next step with openssl generate csr with san command line.
bash
openssl req -passin file:/home/brent/src/ca/mypass.enc \
        -config ~/src/ca/openssl_intermediate.cnf \
        -key ~/src/ca/intermediateCA/private/moto.busche-cnc.com.san.key.pem \
        -new -sha256 -out ~/src/ca/intermediateCA/csr/moto2.busche-cnc.com.san.csr.pem

openssl req -new -subj "/C=GB/CN=foo" \
             -addext "subjectAltName = DNS:foo.co.uk" \
             -addext "certificatePolicies = 1.2.3.4" \
             -newkey rsa:2048 -keyout key.pem -out req.pem

## note set common name equal to a name in alt_names
[alt_names]
DNS.1 = moto.busche-cnc.com

# Openssl sign CSR with Subject Alternative Name
Next use the server.csr to sign the server certificate with -extfile <filename> using Subject Alternative Names to create SAN certificate
I am using my CA Certificate Chain and CA key from my previous article to issue the server certificate
The server certificate will be valid of 365 days and with sha256 algorithm
Since our CA key is encrypted with passphrase, I have used -passin to provide the passphrase, If I do not use this argument then the command will prompt for input passphrase.
In this command using openssl x509 we create SAN certificate server.cert.pem
bash
openssl x509 -req -in /home/brent/src/ca/intermediateCA/csr/moto2.busche-cnc.com.san.csr.pem -passin file:/home/brent/src/ca/mypass.enc -CA /home/brent/src/ca/intermediateCA/certs/ca-chain.cert.pem -CAkey /home/brent/src/ca/intermediateCA/private/intermediate.key.pem -out /home/brent/src/ca/intermediateCA/certs/moto2.busche-cnc.com.san.cert.pem -CAcreateserial -days 365 -sha256 -extfile /home/brent/src/ca/server_cert_ext.cnf

Signature ok
subject=C = US, ST = Indiana, L = Albion, O = Mobex Global, OU = Information Systems, CN = moto.busche-cnc.com, emailAddress = bgroves@mobexglobal.com
Getting CA Private Key

# Openssl verify certificate content
After you create SAN certificate, next you can check the content of your server certificate to make sure openssl sign CSR with Subject Alternative Name was successful.
bash
openssl x509 -noout -text -in ~/src/ca/intermediateCA/certs/moto.busche-cnc.com.san.cert.pem
...
        X509v3 extensions:
            X509v3 Basic Constraints: 
                CA:FALSE
            Netscape Cert Type: 
                SSL Server
            Netscape Comment: 
                OpenSSL Generated Server Certificate
            X509v3 Subject Key Identifier: 
                54:98:4A:DD:1B:35:14:5D:E5:F8:0A:99:42:1F:A7:9F:A4:FE:CF:1B
            X509v3 Authority Key Identifier: 
                keyid:A5:F6:2D:4C:41:BE:F7:E8:10:E4:C8:01:3D:C9:E9:F7:EB:1C:75:18
                DirName:/C=US/ST=Indiana/L=Albion/O=Mobex Global/OU=Information Systems/CN=Root CA
                serial:2B:F3:60:B9:F0:55:E0:76:7F:C6:87:EC:FE:A8:02:3E:13:BB:F2:9F
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication
            X509v3 Subject Alternative Name: 
                IP Address:10.1.1.83, DNS:moto.busche-cnc.com

# full chain
cat ~/src/ca/intermediateCA/certs/moto.busche-cnc.com.san.cert.pem ~/src/ca/intermediateCA/certs/intermediate.cert.pem ~/src/ca/rootCA/certs/ca.cert.pem > ~/src/ca/intermediateCA/certs/moto.busche-cnc.com.san.chain.cert.pem

https://tools.keycdn.com/ssl
No chain issues detected.
1. Subject CN: moto.busche-cnc.com > Issuer CN: Intermediate CA
2. Subject CN: Intermediate CA > Issuer CN: Root CA
3. Subject CN: Root CA > Issuer CN: Root CA

cablint	ERROR	BR certificates must include certificatePolicies
https://www.sysadmins.lv/blog-en/certificate-policies-extension-all-you-should-know-part-1.aspx
https://security.stackexchange.com/questions/252622/what-is-the-purpose-of-certificatepolicies-in-a-csr-how-should-an-oid-be-used

cablint	WARNING	Certificate does not include authorityInformationAccess. BRs require OCSP stapling for this certificate.
cablint	WARNING	Deprecated Netscape extension 2.16.840.1.113730.1.13 treated as opaque extension
cablint	WARNING	Deprecated Netscape extension 2.16.840.1.113730.1.1 treated as opaque extension
cablint	INFO	Name has deprecated attribute emailAddress
cablint	INFO	TLS Server certificate identified
x509lint	ERROR	no authorityInformationAccess extension
x509lint	ERROR	No OCSP over HTTP
x509lint	ERROR	No policy extension
x509lint	WARNING	No HTTP URL for issuing certificate
x509lint	INFO	Checking as leaf certificate
x509lint	INFO	Subject has a deprecated CommonName
x509lint	INFO	Unknown validation policy
zlint	ERROR	CAs MUST NOT issue certificates that have authority key IDs that include both the key ID and the issuer's issuer name and serial number
https://github.com/OpenVPN/easy-rsa/issues/417
zlint	ERROR	CAs SHALL NOT issue certificates with a subjectAltName extension or subject:commonName field containing a Reserved IP Address or Internal Name.
zlint	ERROR	OrganizationalUnitName is prohibited if...the certificate was issued on or after September 1, 2022
zlint	ERROR	Subscriber Certificate: authorityInformationAccess MUST be present.
zlint	ERROR	Subscriber Certificate: authorityInformationAccess MUST contain the HTTP URL of the Issuing CA's OSCP responder.
zlint	ERROR	Subscriber Certificate: certificatePolicies MUST be present and SHOULD NOT be marked critical.
zlint	ERROR	Subscriber certificates must contain at least one policy identifier that indicates adherence to CAB standards
zlint	WARNING	Subscriber certificates authorityInformationAccess extension should contain the HTTP URL of the issuing CA’s certificate
zlint	NOTICE	Check if certificate has enough embedded SCTs to meet Apple CT Policy
zlint	NOTICE	Subscriber Certificate: commonName is deprecated.


# install in trust store
https://support.securly.com/hc/en-us/articles/360036106474-How-to-install-Securly-SSL-certificate-via-Group-Policy-
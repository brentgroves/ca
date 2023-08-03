basicConstraints = CA:FALSE
nsCertType = server
nsComment = "OpenSSL Generated Server Certificate"
subjectKeyIdentifier = hash
# https://github.com/OpenVPN/easy-rsa/issues/417
# authorityKeyIdentifier = keyid,issuer                       # Authority key identifier linking the certificate to the issuer's public key.
authorityKeyIdentifier = keyid                       # Authority key identifier linking the certificate to the issuer's public key.
keyUsage = critical, digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
https://akamath32.medium.com/certificate-authority-information-access-aia-7bc56f7257fc
added AIA because of ERROR	No OCSP over HTTP
authorityInfoAccess = OCSP;URI:http://ocsp.my.host/
# Subscriber Certificate: authorityInformationAccess MUST be present
https://github.com/cert-manager/cert-manager/issues/481
authorityInfoAccess = caIssuers;URI:http://my.ca/ca.html
[alt_names]
DNS.1 = moto.busche-cnc.com
ERROR	CAs SHALL NOT issue certificates with a subjectAltName extension or subject:commonName field containing a Reserved IP Address or Internal Name.
IP.1 = 10.1.1.83

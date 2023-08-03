# ERROR	Subordinate CA Certificate: cRLDistributionPoints MUST be present and MUST NOT be marked critical.
https://stackoverflow.com/questions/11966123/howto-create-a-certificate-using-openssl-including-a-crl-distribution-point
crlDistributionPoints=URI:http://busche-cnc.com/crl.pem

# zlint	WARNING	Subordinate CA Certificate: authorityInformationAccess SHOULD also contain the HTTP URL of the Issuing CA's certificate.
# zlint	WARNING	Subordinate CA Certificate: authorityInformationAccess SHOULD be present.
authorityInfoAccess = OCSP;URI:http://ocsp.busche-cnc.com/
# Subscriber Certificate: authorityInformationAccess MUST be present
https://github.com/cert-manager/cert-manager/issues/481
authorityInfoAccess = caIssuers;URI:http://busche-cnc.com/ca.html


# zlint	NOTICE	A SubCA certificate must not have key usage that allows for both server auth and email protection, and must not use anyExtendedKeyUsage

# zlint	NOTICE	To be considered Technically Constrained, the Subordinate CA certificate MUST have extkeyUsage extension
extendedKeyUsage = serverAuth                               # Extended key usage for server authentication purposes (e.g., TLS/SSL servers).



# issues left
cablint	NOTICE	CA certificates without Digital Signature do not allow direct signing of OCSP responses
cablint	INFO	CA certificate identified
x509lint	INFO	Checking as intermediate CA certificate
zlint	NOTICE	Root and Subordinate CA Certificates that wish to use their private key for signing OCSP responses will not be able to without their digital signature set

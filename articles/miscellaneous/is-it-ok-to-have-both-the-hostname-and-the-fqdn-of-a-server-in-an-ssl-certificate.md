https://security.stackexchange.com/questions/260451/is-it-ok-to-have-both-the-hostname-and-the-fqdn-of-a-server-in-an-ssl-certific

Is it OK to have both, the hostname and the FQDN of a server, in an SSL certificate?


On a company intranet, it is often more comfortable to access an internal webserver just by its hostname instead of providing the FQDN. As long as the hostname itself does not already equal a valid name on the internet, the automatic concatenation of the hostname and a DNS suffix, provided by the network settings, will be resolved to the intranet server successfully.

To not get certificate warnings, the certificate then needs to cover both names, the hostname and the FQDN of the server.

On ServerFault, there is a question that covers the technical feasibility of having both, the hostname and the FQDN in an SSL certificate (which technically is absolutely no problem, btw.). It partially drifted into a discussion, if it is OK to have both names in a certificate, that I'd like to commence here.

The main concern, that was mentioned, was trust. It would be necessary to work with the FQDN of a machine to trust it. Assuming correctly configured DNS suffixes, how can the trust be compromised, when using just the hostnames? Is it only a problem with multiple DNS suffixes (ambiguity)?
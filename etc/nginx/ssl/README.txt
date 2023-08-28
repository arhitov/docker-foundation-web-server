How to issue a self-signed SSL certificate.


First, let's generate a private key:
$ openssl genrsa -out rootCA.key 2048

Then the certificate itself:
$ openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.pem

Create a certificate
$ ./create_certificate_for_domain.sh mysite.localhost



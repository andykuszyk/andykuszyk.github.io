# PKI certificate management
I have a rough understanding of PKI certificates, how they work, and what TLS is
in general. However, I've always struggled to understand the details,
particularly from the point of view of an operator. How do I check if a
certificate is valid? How do I check who issued it? What does it even mean to
"issue" a certificate? To make matters worse, I'm frequently confounded by the
variety of different file types used for certificates. Is it a `pem`, or a
`crt`, or a `pub`? Speaking of `pub`, what's the difference between the TLS
certificate my server uses to encrypt traffic, and the certificates I use for
SSH authentication?

As I said, I have a very *rough* understanding, but a lot of gaps.

In this post, I will try to explain:
- The different files that can be used to store certificates.
- How the files are structured, and how they differ from one another.
- How to generate new certificates, and inspect existing ones, using the 
  `openssl` command line tool.
- What a certificate chain is, and how to inspect it.
- The difference between a TLS certificate and an SSH certificate.

Having covered these basics, I'll then walk though a practical example of using
certificates for TLS via a local `nginx` proxy, modelling the client/server TLS
you often see on the web.

Hopefully by the end of the post, you'll have a clearer idea of what
certificates are, and how to interact with them.

## PKI certificate files
Most TLS certificates are in fact X.509 certificates. X.509 is a standard for
certificate structure which defines which fields are included in the
certificate. X.509 certificates can be stored in a variety of different files,
in a variety of different formats. For me, this is the main cause of confusion
about the different file types used to store certificates.

X.509 certificates are typically stored in base64 encoded ASCII files which use
the `*.pem`, `*.crt`, `*.cer`, and `*.key` file extensions interchangeably.
Whenever you see one of these files, you're looking at a base64 encoded X.509
certificate, irrespective of what the file extension might be.

Whichever file format or extension you're using, an X.509 certificate is going
to be represented by two files:
1. A public key, which will contain information on the issuer and validity of the
   certificate, as well as cryptographic material required for encryption.
2. A private key, which will contain cryptographic material required for
   decryption.

## Certificate requests
Before we dive into generating new certificates in the next section, it's worth
briefly mentioning what a certificate request is. Certificate requests require a
basic understanding of certificate authorities, which are discussed later in
this blog post.

Normally, when a new certificate is generate it is signed by a certificate
authority who vouch for its authenticity. When generating certificates in this
way, the artefacts of the certificate generation process are files which
actually represent a certificate request, and not a certificate.

The certificate request is sent to the certificate authority, who return a
signed certificate which is ready to use. When experimenting with certificate
generation locally, this certificate request and signing step can be skipped,
and a certificate can be generated directly with no signing. This is known as a
self-signed certificate, which is perfectly usable, but would fail certificate
verification checks in real life.

## Generating and inspecting certificates with `openssl`
New X.509 certificates can be generated using the `openssl` command line tool.
Parameters for certificate generation can be provided via command line
arguments, interactive responses, or via a config file.

For this example, we will start with the following config file:
```sh
$ cat << EOF > openssl.conf
[req]
distinguished_name=distinguished_name
prompt=no

[distinguished_name]
countryName=UK
localityName=London
organizationName=Form3
EOF
```

Then, we can use `openssl` to generate a new X.509 certificate with the
following:

```sh
$ openssl req -x509 -nodes -newkey rsa:4096 -keyout private.pem -out public.pem -config openssl.conf
```

Let's just examine each of the command line arguments:
- `req`: this command creates and processes certificate requests.
- `-x509`: generate an X.509 certificate that is self-signed, as opposed to a
  certificate request that would need to be signed by a certificate authority.
- `-nodes`: do not encrypt the private key.
- `-newkey rsa:4096`: indicates that a new certificate request and private key 
  should be generated, and that the RSA algorithm should be used with a key 
  length of 4096 bits.
- `-keyout private.pem`: the file to write the private key to.
- `-out public.pem`: the file to write the certificate/public key to.
- `-config openssl.conf`: the config file to use for certificate parameters.

The result of this command is two files: a private key, and a public certificate
file.

The public key looks like this:

```sh
$ cat public.pem
-----BEGIN CERTIFICATE-----
MIIE2DCCAsACCQCZfiGwlnUbgDANBgkqhkiG9w0BAQsFADAuMQswCQYDVQQGEwJV
SzEPMA0GA1UEBwwGTG9uZG9uMQ4wDAYDVQQKDAVGb3JtMzAeFw0yMjA0MjcxNTU1
...
qjgSTJpltmuAUl2qYvo8ZV9RFnhUKPk3e1ntJMWA1rvhaHaClTLUK9hUTGVuj/eL
5xNkbEKS/bwwCJQNdmgdyKeTa7ntJYdxiXMClemcJZiFQup3WBtWzEueaxI=
-----END CERTIFICATE-----
```

The private key looks like this:

```sh
$ cat private.pem
-----BEGIN ENCRYPTED PRIVATE KEY-----
MIIJnzBJBgkqhkiG9w0BBQ0wPDAbBgkqhkiG9w0BBQwwDgQI/SQlNhidF/ECAggA
MB0GCWCGSAFlAwQBKgQQ/KqcoZLt2nrZYniObOZRFgSCCVA6GDSvQpmzr7sg40GU
...
vAPV8WR/FIzEHL4hzfgFq1PHXy/1dTwgpJRW3Idfigcv9PNC4s/O980DztdUXEnp
k1v/0kZuvPGMLRpRUhhNlOOfSw==
-----END ENCRYPTED PRIVATE KEY-----
```

Now that we've successfully generated a public/private key pair, we can inspect
these keys as follows:

```sh
$ openssl x509 -in public.pem -text
Certificate:
    Data:
        Version: 1 (0x0)
        Serial Number: 11060314777190734720 (0x997e21b096751b80)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=UK, L=London, O=Form3
        Validity
            Not Before: Apr 27 15:55:58 2022 GMT
            Not After : May 27 15:55:58 2022 GMT
        Subject: C=UK, L=London, O=Form3
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (4096 bit)
                Modulus:
                    00:d1:8a:f1:90:4e:0c:26:35:ce:8a:60:f7:a2:01:
                    3a:41:6f:b4:1e:4a:9c:1d:f8:80:72:2d:a3:dd:4d:
...
```

Here you can see the issuer, validity, and algorithm of the public key.
Similarly, the private key can be inspected with:

```sh
$ openssl rsa -in private.pem -text
Enter pass phrase for key.pem:
Private-Key: (4096 bit)
modulus:
    00:ef:f1:21:a1:cb:7e:ee:c9:3d:4c:44:d7:87:10:
    dc:a1:2e:9d:1f:f4:9a:86:d5:1a:4a:5f:43:0b:7a:
...
```

Checking the private key yields less useful information (for an operator, at
least), but you can also check the consistency of the key using `openssl rsa -in
private.pem -check`.

## Certificate chains
In order to verify the authenticity of certificates, it's necessary to inspect
the issuer (or certificate authority) of a certificate. In the example above,
the distinguished name of the issuer is `C=UK, L=London, O=Form3`. This
distinguished name identifies the certificate authority, who must be trusted in
order for the authenticity of a certificate to be verified.

The certificate authority has a root certificate, which is used to generate a
signature for each certificate that it issues. Combining the signature of a
certificate with the public key of the issuer's root certificate allows a
signature to be verified. Sometimes this is as simple as combining the
signature/key of an issued certificate and a root certificate, and sometimes
there are intermediate certificates between the issued certificate you're
verifying and the ultimate root certificate.

On a Linux operating system, a list of root certificates can be found in
`/etc/ssl/certs`. Each certificate authority is represented by its own root
certificate, which can be inspected in the same way we inspected the certificate
we generated earlier.

For example, inspecting the GoDaddy root certificate looks something like this:

```sh
$ openssl x509 -in /etc/ssl/certs/Go_Daddy_Root_Certificate_Authority_-_G2.pem -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 0 (0x0)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: C = US, ST = Arizona, L = Scottsdale, O = "GoDaddy.com, Inc.", CN = Go Daddy Root Certificate Authority - G2
        Validity
            Not Before: Sep  1 00:00:00 2009 GMT
            Not After : Dec 31 23:59:59 2037 GMT
        Subject: C = US, ST = Arizona, L = Scottsdale, O = "GoDaddy.com, Inc.", CN = Go Daddy Root Certificate Authority - G2
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:bf:71:62:08:f1:fa:59:34:f7:1b:c9:18:a3:f7:
                    80:49:58:e9:22:83:13:a6:c5:20:43:01:3b:84:f1:
                    e6:85:49:9f:27:ea:f6:84:1b:4e:a0:b4:db:70:98:
...
```

We can tell this is a root certificate, because the certificate was used to sign
itself. We can verify this self-signed signature as follows:

```sh
$ openssl verify -CAFile /etc/ssl/certs/Go_Daddy_Root_Certificate_Authority_-_G2.pem /etc/ssl/certs/Go_Daddy_Root_Certificate_Authority_-_G2.pem
/etc/ssl/certs/Go_Daddy_Root_Certificate_Authority_-_G2.pem: OK
```

Similarly, if we try to verify the issuer signature for the certificate we generated, we get a verification error:

```sh
$ openssl verify -CAfile /etc/ssl/certs/Go_Daddy_Root_Certificate_Authority_-_G2.pem public.pem
C = UK, L = London, O = Form3
error 18 at 0 depth lookup: self signed certificate
error public.pem: verification failed
```

This error message indicates that the certificate we generated was
"self-signed". The GoDaddy root certificate is also self-signed, however this is
characteristic of a root certificate. The root certificate is trusted, because
it was installed by the operating system. Our certificate is less trustworthy,
because we just generated it on the fly.

TLS certificates in use on the internet are signed by one of the root
certificates installed on your computer, which allows their authenticity to be
verified.

This can be demonstrated by inspecting a certificate of a website protected by
TLS, and then verifying it with its root/issuing certificate.

The TLS certificate of a website can be inspected by using `openssl s_client`,
which normally expects input from `stdin` (hence the `echo -n |`):

```sh
$ echo -n | openssl s_client -connect www.google.com:443
CONNECTED(00000004)
depth=2 C = US, O = Google Trust Services LLC, CN = GTS Root R1
verify return:1
depth=1 C = US, O = Google Trust Services LLC, CN = GTS CA 1C3
verify return:1
depth=0 CN = www.google.com
verify return:1
---
Certificate chain
 0 s:CN = www.google.com
   i:C = US, O = Google Trust Services LLC, CN = GTS CA 1C3
 1 s:C = US, O = Google Trust Services LLC, CN = GTS CA 1C3
   i:C = US, O = Google Trust Services LLC, CN = GTS Root R1
 2 s:C = US, O = Google Trust Services LLC, CN = GTS Root R1
   i:C = BE, O = GlobalSign nv-sa, OU = Root CA, CN = GlobalSign Root CA
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIEiTCCA3GgAwIBAgIRALC2MC4uIPl4CoGx9rjcnsMwDQYJKoZIhvcNAQELBQAw
RjELMAkGA1UEBhMCVVMxIjAgBgNVBAoTGUdvb2dsZSBUcnVzdCBTZXJ2aWNlcyBM 
...
 vkJEyzGxWYiIzgWHYQ==
-----END CERTIFICATE-----
subject=CN = www.google.com

issuer=C = US, O = Google Trust Services LLC, CN = GTS CA 1C3

---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: ECDSA
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 4297 bytes and written 386 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Server public key is 256 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 0 (ok)
---
DONE
```

A certificate file can be generated for verification with:

```sh
$ echo -n | openssl s_client -connect www.google.com:443 | openssl x509 > google.pem
```

If you inspect this certificate (`openssl x509 -in google.pem -text`), you will
see that this certificate was issued by "Google Trust Services". A quick search
of your root certificate directory (`cat /etc/ssl/certs | grep oogle`) will show
that Google Trust Services is not a root certificate authority. This means that
there is a chain of issuing certificates between the one in use at google.com
and the certificate authority that signed the first certificate in the chain.

We can download the certificate chain separately using `openssl` as follows:

```sh
$ echo -n | openssl s_client -connect www.google.com:443 -showcerts > google-chain.pem
```

The original certificate can then be manually verified against its root
certificate via the certificate chain with:

```sh
$ openssl verify -CAfile google-chain.pem google.pem
google.pem: OK
```

The verification of Google's certificate against the root certificate installed
in your operating system demonstrates the difference in trust between the public
certificates in use on the internet for TLS, and the self-signed certificates
you might generate locally. The fact that the certificates in use online can be
verified against known certificates on your computer demonstrates that they have
been issued to a trustworthy server.

## TLS vs. SSH certificates
According to `man ssh-keygen`, the certificates used for SSH are a different,
and much more simple, format than X.509 certificates used for TLS. SSH
certificates are used in a similar way to the X.509 certificates used in TLS:
they consist of a private and key, but the format is different to the
certificates described in this post.

## Example: TLS for HTTPS web servers
When you connect to a server that offers TLS, the server will be configured to
send you its public certificate and will encrypt data with its private
certificate. I'm not going to delve into the details of how TLS works here, but
instead demonstrate how you might configure a simple web server with the
materials it needs to make TLS possible. I'll be using `nginx` running in a
Docker container to provide a small example.

First of all, we'll need some static content to serve as our web page:

```sh
$ cat << EOF > index.html
Hello world!

This web page is protected using TLS!
EOF
```

Next, we'll need to configure an `nginx` server to serve this web page:

```sh
$ cat << EOF > nginx.conf
events {}
http {
    server {
        root /www/;
        location / {}
    }
}
EOF
```

Then, we can package `nginx` with our web page and config as follows:

```sh
$ cat << EOF > Dockerfile
FROM nginx
COPY index.html /www/index.html
COPY nginx.conf /etc/nginx/nginx.conf
EOF
```

Now, we can build and run this image with:

```sh
$ docker build -t nginx-tls .
$ docker run -p 8080:80 nginx-tls
```

OK, now we can test our server by making a web request:

```sh
$ curl localhost:8080/
Hello world!

This web page is protected using TLS!
```

Now that we've got a functioning web server, we can try to add TLS to it. The
first thing to do is to update the `nginx` configuration with TLS details:

```sh
 $ cat << EOF > nginx.conf
events {}
http {
    server {
        root /www/;
        location / {}

        listen 443 ssl;
        ssl_certificate public.pem;
        ssl_certificate_key private.pem;
    }
}
EOF
```

This configuration uses the certificates we generated earlier. To re-cap, we
generated these files using `openssl`:

```sh
$ openssl req -x509 -nodes -newkey rsa:4096 -keyout private.pem -out public.pem -config openssl.conf
```

The certificate files will also need to be present in the Dockerfile:

 ```sh
$ cat << EOF > Dockerfile
FROM nginx
COPY index.html /www/index.html
COPY nginx.conf /etc/nginx/nginx.conf
COPY *.pem /etc/nginx/
EOF
```

We can then build and run the container in a similar way to before:

 ```sh
$ docker build -t nginx-tls --no-cache .
$ docker run -p 8080:443 nginx-tls
```

Now, if we try to get the webpage via HTTP, it fails:

```sh
$ curl localhost:8080
<html>
<head><title>400 The plain HTTP request was sent to HTTPS port</title></head>
<body>
<center><h1>400 Bad Request</h1></center>
<center>The plain HTTP request was sent to HTTPS port</center>
<hr><center>nginx/1.21.6</center>
</body>
</html>
```

And, if we try to get the webpage via HTTPS, it also fails:

```sh
$ curl https://localhost:8080
curl: (60) SSL certificate problem: self signed certificate
More details here: https://curl.haxx.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
```

This is because we're using a self-signed certificate. Web browsers, `curl`, and
most HTTP clients will fail if you try to make requests over TLS and the server
presents a self-signed certificate. In this case, there's no way to know if you
can trust the server or not. However, if we try again and tell `curl` to ignore
self-signed certificates (`-k`), we are successful:

```sh
$ curl https://localhost:8080 -k
Hello world!

This web page is protected using TLS!
```

## Summary
So there you have it! Most TLS certificates are X.509 certificates, and whether
you see them in `*.pem`, `*.crt`, or `*.key` files they're all likely to be in
the same format. Certificates can be generated, inspected, and verified using
the `openssl` command, and this applies to both self-signed certificates you
might generate for testing, as well as certificates signed by a trusted
certificate authority. Using X.509 certificates for TLS on web servers is
relatively straightforward, and easy to configure in server applications like
`nginx`.

I hope you've found this post useful, and walk away from it slightly less
confounded than I was when I started writing it!
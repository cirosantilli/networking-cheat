# OpenSSL

<http://en.wikipedia.org/wiki/OpenSSL>

<https://www.openssl.org/>

Implements a large range of Cryptographic utilities.

Library and command line tool frontend.

Apache license.

This is the library that was affected by the notorious 2014 Heartbleed vulnerability, which allowed theft of private keys <http://en.wikipedia.org/wiki/Heartbleed>, and affected around 17% of the encrypted network traffic of the world. It did not affect SSH though.

## Alternatives

[Mozilla Network Security Services (NSS)](http://en.wikipedia.org/wiki/Network_Security_Services).

## help

List all commands:

    openssl help

## version

    openssl version

Sample output:

    OpenSSL 1.0.1f 6 Jan 2014

## enc

Encode and decode with symmetric key.

List all encodings:

    openssl enc help

Many encodings have several variants with suffixes like: `cbc`, `ecb`, etc. These are well known modes which apply to several algorithms of the block cypher family: <http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Common_modes> CBC is a popular and reasonable choice in case you have no idea what to pick.

### d

Decrypts:

    printf 'a' | openssl enc -base64 | openssl enc -d -base64

Outputs the original input:

    a

### base64

Not really a encryption, but present because used in some algorithms:

    printf 'a' | openssl enc -base64

Output:

    YQ==

Let's quickly cross-check with the GNU Coreutils:

    printf 'a' | base64

Output:

    YQ==

OK, I think they've both got that right.

Now let's decrypt it:

    printf 'a' | openssl enc -base64 | openssl enc -d -base64

### aes

    printf 'a' | openssl enc -aes-256-cbc -out /tmp/openssl

Asks for you password. Let's check that it was actually encrypted:

    cat /tmp/openssl

I see trash. Good. Now let's decrypt:

    openssl enc -d -aes-256-cbc -in /tmp/openssl

Asks for password again. After that, output:

    a

## Digest

## dgst

    printf 'a' | openssl dgst -sha256

Outputs:

    ca978112ca1bbdcafac231b39a23dc4da786eff8147c4e72b9807785afee48bb

Let's cross check with GNU Coreutils:

    printf 'a' | sha256sum

Output:

    ca978112ca1bbdcafac231b39a23dc4da786eff8147c4e72b9807785afee48bb

## Public key cryptography

### Prerequisites

Basics of public key cryptography like RSA are assumed. You should understand:

- the roles of the public and private key
- the difference between encryption and signature

### Generate key

Generate a RSA public private pair with length 1024:

    openssl genrsa -out /tmp/key.pem 1024

Check it out:

    cat /tmp/key.pem

Gives:

    -----BEGIN RSA PRIVATE KEY-----
    MIICXQIBAAKBgQDVdZ+sbEhjYpUXmrtpaw9u/cwlfutGv8TICYQSq9vWl2NUdD7m
    ...
    1uBY2kGhER9rP3JSCBO/a6tmRdWTK//D88T3TWAzgq9J
    -----END RSA PRIVATE KEY-----

Get human-readable information out of it:

    openssl genrsa -in /tmp/key.pem 1024 -text

Sample output:

    Private-Key: (1024 bit)
    modulus:
        00:d5:75:9f:ac:6c:48:63:62:95:17:9a:bb:69:6b:
        ...
        93:5d:f6:18:6e:d7:32:47:09
    publicExponent: 65537 (0x10001)
    privateExponent:
        00:8b:e1:c7:e0:53:19:60:df:a2:7a:54:70:ca:15:
        ...
        ca:81:63:55:7c:2f:1a:79:31
    prime1:
        00:fe:a6:26:b1:29:8d:cd:0d:03:67:14:c0:55:5a:
        ...
        69:b4:ef:39:0d
    prime2:
        00:d6:97:88:0b:4b:c0:e6:ea:ad:c8:3b:e2:74:18:
        ...
        ee:94:9a:ce:ed
    exponent1:
        00:da:6b:7f:62:94:46:e9:ab:90:d0:98:ef:04:07:
        ...
        42:7d:0c:77:fd
    exponent2:
        69:a1:85:b2:29:c3:f6:e7:26:4c:1e:4e:46:5f:b6:
        ...
        20:92:63:b1
    coefficient:
        4b:5e:c7:78:b3:32:eb:2d:13:bb:21:63:96:f1:22:
        ...
        33:82:af:49

You should normally password encrypt that file.

Now let's generate the public key:

    openssl rsa -in key.pem -pubout | tee /tmp/pub-key.pem

Outputs:

    -----BEGIN PUBLIC KEY-----
    MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDVdZ+sbEhjYpUXmrtpaw9u/cwl
    futGv8TICYQSq9vWl2NUdD7mLDC6b6aYib2aO3KiS0HWC6AFztxom3oO7pkhFaKz
    i7isTFVNt/ZIyyTw4W88dDNNBtNMhwBmAMEp4hsbvuKeko2ErJl7l3f3hEpnQM0g
    6qmrRpNd9hhu1zJHCQIDAQAB
    -----END PUBLIC KEY-----

### Encrypt with key

Now let's use the public key to encrypt something:

    printf 'a' | openssl rsautl -encrypt -pubin -inkey /tmp/pub-key.pem | tee /tmp/enc

We must use the option `-pubin` when using the public key.

Or using the file with the private keys:

    printf 'a' | openssl rsautl -encrypt -inkey /tmp/key.pem | tee /tmp/enc

The output is all jumbled.

Now let's decrypt:

    openssl rsautl -decrypt -inkey /tmp/key.pem </tmp/enc

Output:

    a

### Sign with key

    printf 'a' | openssl rsautl -sign -inkey /tmp/key.pem | tee /tmp/sign

Outputs trash.

Let's verify only with the public key:

    openssl rsautl -verify -pubin -inkey /tmp/pub-key.pem </tmp/sign

Output:

    a

Let's try to tamper with the signed message:

    printf b >> /tmp/sign
    openssl rsautl -verify -pubin -inkey /tmp/pub-key.pem </tmp/sign
    echo $?

Output:

    RSA operation error
    1

### Get server's certificate

<http://security.stackexchange.com/questions/16085/how-to-get-public-key-of-a-secure-webpage>

    openssl s_client -connect google.com:443

    openssl s_client -connect google.com:443 | openssl x509 -pubkey -noout

## PKI

<http://users.dcc.uchile.cl/~pcamacho/tutorial/crypto/openssl/openssl_intro.html#htoc7> teaches you how to host a PKI system just for fun.

## Sources

- <http://users.dcc.uchile.cl/~pcamacho/tutorial/crypto/openssl/openssl_intro.html>

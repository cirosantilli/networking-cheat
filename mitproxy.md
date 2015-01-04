# mitmproxy

Carry out man in the middle TLS sniffs and manipulation.

## Install

    sudo aptitude install libxslt-dev
    sudo pip install mitmproxy

## Command line arguments

- `-p`: port to listen on

## R

## Forward request to given location

    mitproxy -p 8000 -R http://example.com
    curl -H 'Host: example.com' -i http://localhost:8000/

### Forward HTTPS request to given location

My best attempt was:

    curl -k -H 'Host: github.com' https://localhost:8000
    curl: (35) error:04091077:rsa routines:INT_RSA_VERIFY:wrong signature length

with `-k` to allow sessions without a certificate but it failed. Knowing that the mitmproxy generated certificates are kept under `~/.mitmproxy` might help.

## ncurses interface

- `?`: help
- `q`: quit
- `C`: clear screen

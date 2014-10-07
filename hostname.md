# Host

A host is anything able to send and receive packages over a network: this includes workstations (computers) and routers.

Can be specified by either

- an IP
- a string that will be resolved by a DNS server to an IP

TODO merge with DNS.

## Host user pair

A user may access a (system) computer from another computer using for example ssh.

To do so, he must be registered in the target computer.

This is why user/host pairs are common: the host pair says from which computer user is trying to access his account.

## Hostname

An alias for an IP, local or remote.

Must be converted into an IP via DNS.

When outside the local network, the hostname is added before the domain name, e.g. in:

    www.google.com

- hostname: `www`
- domain name: `google.com`

It is not a good idea to have a dot `.` in your domain name, since then how could its last part be distinguished from the domain name?

TODO is the hostname `www.google.com` or just `google.com`? Contradictory answers: <http://superuser.com/questions/59093/difference-between-host-name-and-domain-name>

### www

`www.google.com` and `google.com` are completely different hosts, and can lead to different IPs.

What sane companies do is choose one and redirect the other, *be consistent*.

But I have seen companies that use `www` for a different website than without, and it is possible that no redirection happens.

Browsers can store different cookies for both, so you can be logged in at `www.a.com` but not at `a.com`.

In the case of FTP, `ftp://ftp.a.com` URLs which are common, and perhaps in that case it is better to keep the `ftp` and redirect HTTP requests to `ftp.a.com` to `ftp://ftp.a.com` since FTP is less used than HTTP, allowing users to type simply `ftp.a.com` instead of `ftp://a.com`.

It is more recommended today not to use the `www` is noise: <http://stackoverflow.com/questions/1109356/www-or-not-www-what-to-choose-as-primary-site-name>.

`www` was more used in the past, so older companies may continue to use them because they are stuck with it.

As of early 2014:

- `facebook.com` redirects to `www.facebook.com`
- `google.com` redirects to `www.google.com`

## Domain name

E.g.: `google.com`, `stackoverflow.com` are commonly called domain names.

A more precise way of speaking is saying that `google` is a subdomain of `com`, and `www` is a subdomain of `google.com`.

They identify a network owned by Google. But in order to get an actual IP, you still need to add a hostname such as `www`.

Domain names may contain more than one `.`: `bbc.co.uk`.

### Subdomain

The subdomain can include a period (.) but not as the first or last character. Consecutive periods (...) are not allowed. A subdomain cannot exceed 25 characters.

### example.com

<http://example.com> is a test domain reserved by IANA.

It is a serves as a great URL placeholder on simple examples.

## Top level domain

`.com`, `.net`, `.io`, `.fr` are examples.

Every name must be under one of those.

They are controlled by IANA, and there are not that many out there except for the country ones: <http://en.wikipedia.org/wiki/List_of_Internet_top-level_domains>

To get a country TLD, it seems that you must have some link with the country.

Some TLDs are reserved for certain uses and registrars must check that you/ your organization are eligible: `.gov` for governments, `.mit` for US military.

Some interesting ones:

- `.sexy` and `.xxx`. Guess what.
- `.guru`. No suggested use. Funny.

Some country ones have become generic: `.io` is a notable example, popular amongst startups as of 2014-03. Short, sounds good, reminds of IO input output.

## hosts file

Located at:

    /etc/hosts

Tells your computer where to redirect the given names.

Takes precedence over DNS.

Big downside: you have to have one of this file on every PC.

Therefore, use a DNS server instead

    cat /etc/hosts

Redirect Wikipiedia to localhost:

    echo "127.0.0.1 www.wikipedia.org" | sudo -a /etc/hosts

Now:

    firefox www.wikipedia.org &

will go to localhost, and you will see your Apache page if you are running apache.

Undo that, its silly:

    sudo sed -i "$ d" /etc/hosts

On Windows the file is:

    C:\Windows\System32\Drivers\Etc\hosts

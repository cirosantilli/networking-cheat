# SSH

Protocol like telnet, but encrypted

For SSH to work you will need:

- a SSH server running on a machine.
- a SSH client running on another machine.

It is possible to do tests using `localhost` on a single machine.

Per user configurations for both the server and the client are contained under `~/.ssh`

## Implementations

<http://www.openssh.com/>

OpenSSH is the most important implementation on Linux.

OpenSSH used to depend on part OpenSSL, but that dependency was removed: <http://security.stackexchange.com/questions/3424/how-is-openssl-related-to-openssh>

## Server

Known as `sshd`, which stands for ssh daemon.

Must be installed and running on a machine for users from other computers to log into that machine.

The server part of ssh is called `sshd`.

Configuration:

    sudo cp  /etc/ssh/sshd_config{,.bak}
    sudo vim /etc/ssh/sshd_config

For the server to work, the following configuration is minimal:

    Host *                  #config for all hosts
    Port 22                 #open port 22
    AllowUsers user1 user2  #allow the given users

The server is often started as part of the `initrd` system.

Therefore, to get it running you will probably use:

    sudo service ssh start

and to stop it:

    sudo service ssh stop

and to check its status:

    sudo service ssh status

The default log file for the server is: `/var/log/auth.log`, which is shared by other utilities such as `sudo`. If things fail, that is where you should look! Try `sudo grep sshd /var/log/auth.log` for the relevant log lines.

## Client

The client connects to a server to give shell access on the server.

Make sure that the configuration files are correct:

    /etc/ssh/ssh_config{,.bak}
    ~/.ssh/config

Get the version of your ssh client:

    ssh -V

For local tests, use localhost and the current user:

    host=localhost
    user=`id -un`

Connect to hostname with your current username:

    ssh $host

Get debug level information if things don't work:

    ssh -v $host
    ssh -vv $host
    ssh -vvv $host

For this to work you need:

- your host (computer) is allowed. See ssh server.
- your user is allowed. See ssh server.
- your user exists as a regular user in the *server* computer. See `useradd`.

There are two main methods of connection:

-   using an authorized public RSA id, which does not require a password, unless your password is locally encrypted by a password, which is recommended.

    This method is used if the key is allowed.

-   using the same password as the user on the server has.

    This method is only used if there is not authorized key on the local machine.

Connect to hostname with the given username:

    ssh $user@$host

or:

    ssh -l $user $host

Choose port:

    p=22
    ssh -p $p $h

The default is 22 specified by IANA, so don't change it if you can avoid it.

It is *not* possible to set ports via the common URL syntax: `ssh host:22`.

### Client configuration

    man ssh_config

File:

    ~/.ssh/config

Different keys for different hosts:

    Host server1.nixcraft.com
      IdentityFile ~/backups/.ssh/id_dsa
    Host server2.nixcraft.com
      IdentityFile /backup/home/userName/.ssh/id_rsa

Different keys for different website users with the same SSH username (e.g. on GitHub every git operation is done with the user `git` for every user): <http://stackoverflow.com/questions/7927750/specify-an-ssh-key-for-git-push-without-using-ssh-config>

    Host github-user1
      HostName github.com
      User git
      IdentityFile /home/USER/.ssh/id_rsa_github_user1
      IdentitiesOnly yes

    Host github-user2
      HostName github.com
      User git
      IdentityFile /home/USER/.ssh/id_rsa_github_user2
      IdentitiesOnly yes

and then:

    git remote add alice git@gitolite-as-alice:whatever.git
    git remote add bob git@gitolite-as-bob:whatever.git

## authorized_keys

List of public keys accepted by server for login as a given user.

Each user has its own `/home/u/.ssh/authorized_keys` keys, which determine which public keys allow to login as that user. This means that to login as user `u`, the file `/home/u/.ssh/authorized_keys` must contain your public key. With that you will only be able to login into the account of user `u`.

This file is only used for RSA authentication, not password.

In addition to the allowed key, each line can also contain extra options that control the connection:

    from="ok.com",no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty ssh-rsa <key> <comment>

Non-obvious options above include:

- `command="command"`: fixes a single command to be run to restrict what uses can do. This command is crucial for GitLab's (and likely GitHub's) SSH system, where SSH can only be used for Git operations, and always calls: <https://github.com/gitlabhq/gitlab-shell/blob/cc193ea45d8d0651fe09fc93bb1417eaac50c2ff/bin/gitlab-shell>
- only connections coming from `ok.com` will be accepted.

Options are documented with the server at `man sshd` since they are only used by the server.

SSH is by default very fussy about the permissions of this file which should be:

    chmod 700 ~/.ssh
    chmod 600 ~/.ssh/authorized_keys

and not more permissive. If you really want that, you can do configure SSH to be less safe via `StrictModes no`.

## known_hosts

Located at: `.ssh/known_hosts`.

Your client will only connect to a server if its key is in known hosts. This file exists because security is useless if someone is impersonating the message receiver. If the server's public identity is not in the known hosts file, SSH will ask is you want to add it.

## ssh-keygen

Generates public and private key pairs for use with ssh.

Generate an RSA public private pair:

    ssh-keygen -f ~/.ssh/id_rsa -t rsa -C "you@email.com"

By default the keys are put under `~/.ssh` with names `id_rsa` for the private and `id_rsa.pub` and have length 1024 bits.

*Do* use a passphrase, otherwise anyone that gets his hand on your `id_rsa` file owns your identity.

*Never* share your private key! It is like a password that allows you to connect to servers.

There can be only one key per file.

When you invoke `ssh`, it will always use the same key by default, but you can configure it to use different keys in different connections.

The formats are for the public key:

    ssh-<type> <public-key> <comment>

For the private key:

    -----BEGIN RSA PRIVATE KEY-----
    <key>
    -----END RSA PRIVATE KEY-----

The actual data format a bit more involved. Discussion [here](http://stackoverflow.com/questions/12749858/rsa-public-key-format). Basically the data is Base 64 encoded, and it also contains some necessary algorithm metadata.

Often used to determine if a key is present or not is the key's fingerprint, which are just hashes of the keys.

Fingerprints are often displayed on the following format:

    43:51:43:a1:b5:fc:8b:b7:0a:3a:a9:b1:0f:66:73:a8

with the colons added for only readability.

You can get the fingerprint or all the fingerprints present on a file via:

    ssh-keygen -lf ~/.ssh/id_rsa
    ssh-keygen -lf ~/.ssh/id_rsa.pub
    ssh-keygen -lf ~/.ssh/known_hosts

## Usage

Once you log in, it is as if you had a shell on the given ssh server computer!

You cannot copy files between computer with ssh directly, but you can use `scp` or `sftp` to do it.

Note how you appear on the who list:

    who

To close your connection:

    logout

or enter `CTRL-D`.

## GUI applications

It is possible to run X applications remotely, but it may be that the default configurations don't allow you to do that.

To allow X, make sure that the line:

    ForwardX11 yes

is present and uncommented on both client and server configuration files.

Now you can do:

    firefox

and it should work.

If you forget to let `ForwardX11 yes`, you would get errors like:

    Error: can't open display
    Error: display not specified

## Protocol

TODO

## scp

`cp` via SSH.

Get a file:

    p= #path to local file
    d= #destination directory
    u=
    h=
    scp $u@$h:$p $d

Send a file to server:

    scp $p $u@$h:$d

Send recursively for directories:

    scp -r $u@$h:$p

Send multiple files or directories:

    scp -r $u@$h:"$p1" $u@$h:"$p2"

You cannot do a direct SSH, e.g. on Google Cloud you need to use `gcutil` as front-end, you can just do:

    echo 'cp /remove/path/to/file' | gcutil parameters > out

And then remove trash lines.

## sftp

FTP over SSH.

# Dokku-Alt.Git.Push.Deploy.Problem
Debugging Dokku-alt Git Push Deploy Problem

I wanted to make my public facing server __[a Dokku-alt server]__
that I could configure sites with

git push dokku master
=====================

```bash
$ git clone https://github.com/heroku/node-js-sample
$ cd node-js-sample
$ git remote add dokku dokku@dokku-alt.com:node-js-app
$ git push dokku master
Counting objects: 296, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (254/254), done.
Writing objects: 100% (296/296), 193.59 KiB, done.
Total 296 (delta 25), reused 276 (delta 13)
-----> Building node-js-app ...
       Node.js app detected
-----> Resolving engine versions

... blah blah blah ...

-----> Application deployed:
       http://node-js-app.progriumapp.com
```

But every single attempt has left me with this error
-----------------------------------------

```bash
  My-MacBook-Pro:node-js-sample marchon$ git push dokku master
  fatal: 'node-js-sample' does not appear to be a git repository
  fatal: Could not read from remote repository.

  Please make sure you have the correct access rights
  and the repository exists.

```

I have been trying to make GIT PUSH DOKKU MASTER
work for deployment on dokku-alt from

https://github.com/dokku-alt/dokku-alt

I tried both this bash command, and cloning the repo locally

```bash

$ sudo bash -c "$(curl -fsSL https://raw.githubusercontent.com/dokku-alt/dokku-alt/master/bootstrap.sh)"

```

I Really "Thought" I had done something wrong
============

After Searching the web for solutions - and not being able to make any of them work, I finally reinstalled a fresh Ubuntu Server 14.04.

__I was committed to making this work__

So... Just In Case:
-------------

* I downloaded and burnt a fresh Ubuntu 14.04 Server CD
* I reconfigured the RAID Controller to split partitions and destroy the raid configuration
* I repartitioned the Drives  
* I installed a fresh Ubuntu Server 14.04 with choosing only SSH-Server as the install type

This server config is about as vanilla as you can get.
------------------------------------------------------

after hours of making sure that it was not a "bad configuration"

I faithfully re-executed the command

```bash

$ sudo bash -c "$(curl -fsSL https://raw.githubusercontent.com/dokku-alt/dokku-alt/master/bootstrap.sh)"

```

```bash
  My-MacBook-Pro:node-js-sample marchon$ git push dokku master

  fatal: 'node-js-sample' does not appear to be a git repository
  fatal: Could not read from remote repository.

  Please make sure you have the correct access rights
  and the repository exists.

```

So would appear that the repo does not exist.

Next task - verify which RSA Identity Keys are being used.

with the command
```bash

bash-3.2# ssh -v dokku@dokku.lazarusco.in
```

We can get SSH to tell us what is happening - the key information is which private key is SSH trying to use.

debug1: Trying private key: ___/var/root/.ssh/id_rsa__
debug1: read PEM private key done: type RSA
debug1: Authentication succeeded (publickey).


```

bash-3.2# ssh -v dokku@dokku.lazarusco.in
  OpenSSH_6.2p2, OSSLShim 0.9.8r 8 Dec 2011
  debug1: Reading configuration data /etc/ssh_config
  debug1: /etc/ssh_config line 20: Applying options for *
  debug1: /etc/ssh_config line 102: Applying options for *
  debug1: Connecting to dokku.lazarusco.in [172.30.42.116] port 22.
  debug1: Connection established.
  debug1: permanently_set_uid: 0/0
  debug1: identity file /var/root/.ssh/id_rsa type -1
  debug1: identity file /var/root/.ssh/id_rsa-cert type -1
  debug1: identity file /var/root/.ssh/id_dsa type -1
  debug1: identity file /var/root/.ssh/id_dsa-cert type -1
  debug1: Enabling compatibility mode for protocol 2.0
  debug1: Local version string SSH-2.0-OpenSSH_6.2
  debug1: Remote protocol version 2.0, remote software version OpenSSH_6.6.1p1 Ubuntu-2ubuntu2
  debug1: match: OpenSSH_6.6.1p1 Ubuntu-2ubuntu2 pat OpenSSH*
  debug1: SSH2_MSG_KEXINIT sent
  debug1: SSH2_MSG_KEXINIT received
  debug1: kex: server->client aes128-ctr hmac-md5-etm@openssh.com none
  debug1: kex: client->server aes128-ctr hmac-md5-etm@openssh.com none
  debug1: SSH2_MSG_KEX_DH_GEX_REQUEST(1024<1024<8192) sent
  debug1: expecting SSH2_MSG_KEX_DH_GEX_GROUP
  debug1: SSH2_MSG_KEX_DH_GEX_INIT sent
  debug1: expecting SSH2_MSG_KEX_DH_GEX_REPLY
  debug1: Server host key: RSA 8b:c8:61:91:4b:6e:5f:13:ea:7c:14:27:69:b0:f3:c0
  debug1: Host 'dokku.lazarusco.in' is known and matches the RSA host key.
  debug1: Found key in /var/root/.ssh/known_hosts:11
  debug1: ssh_rsa_verify: signature correct
  debug1: SSH2_MSG_NEWKEYS sent
  debug1: expecting SSH2_MSG_NEWKEYS
  debug1: SSH2_MSG_NEWKEYS received
  debug1: Roaming not allowed by server
  debug1: SSH2_MSG_SERVICE_REQUEST sent
  debug1: SSH2_MSG_SERVICE_ACCEPT received
  debug1: Authentications that can continue: publickey,password
  debug1: Next authentication method: publickey

  debug1: Trying private key: /var/root/.ssh/id_rsa

  debug1: read PEM private key done: type RSA
  debug1: Authentication succeeded (publickey).
  Authenticated to dokku.lazarusco.in ([172.30.42.116]:22).
  debug1: channel 0: new [client-session]
  debug1: Requesting no-more-sessions@openssh.com
  debug1: Entering interactive session.
  debug1: Sending environment.
  debug1: Sending env LANG = en_US.UTF-8
  Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-30-generic x86_64)

   * Documentation:  https://help.ubuntu.com/

    System information as of Thu Feb 26 20:00:27 EST 2015

    System load:  0.0                Users logged in:        1
    Usage of /:   16.2% of 29.21GB   IP address for eth0:    172.30.42.116
    Memory usage: 7%                 IP address for docker0: 172.17.42.1
    Swap usage:   0%                 IP address for lxcbr0:  10.0.3.1
    Processes:    143

```

We have verified that

* SSH Connection started
* the username is correct  
* which public/private key pair is "Actually" being used

We have confirmed that it is not the ssh keys

```bash
bash-3.2# git push dokku master
  fatal: 'node-js-sample' does not appear to be a git repository
  fatal: Could not read from remote repository.

  Please make sure you have the correct access rights
  and the repository exists.
```

so lets push the public key again on the server so that there can be no confusion.

```bash

marchon@Modulo:~$ echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC9lYDU1bxZaDCRSk7yTZqBvWy1h2vqa9haS3W2OV/16MXnuhip+/qEWDMBxb/PAWjXPDDPpoFYJBCM6C5gmLkvRa1mZsFfRP98cdDZoJHCquQDDon/GqvjQghnRt/RH8dmuMPa4MGLoXjQ4ss4xGUOuWq+OcDrUHt4MjeYoLcQIaXJRwVq9tQWPjfoe4Suv488EesE39fjcefkXCXWMkYZ+kXoYoNfW/KwzPieAGU2PdbM7E8ovWFSufAcQva1cBD69UcjjLpUEc6Nx959A0yI4Zh+iuZbBG2HbeCJMxC042985eo98SlOWLt1As9G3C4Demh8UJnoAMHoGOR1Kael root@Rhondas-MacBook-Pro.local" | dokku access:add
-----> e9:96:ed:cf:eb:fc:53:2b:72:75:0f:7c:cc:ec:5f:3c added as an admin

```
One last push to confirm that I do not think it is a key problem
------------------------------


```bash
bash-3.2# git push dokku master
  fatal: 'node-js-sample' does not appear to be a git repository
  fatal: Could not read from remote repository.

  Please make sure you have the correct access rights
  and the repository exists.
```

I created the repo on the server

```bash

marchon@Modulo:~$ dokku create node-js-sample
-----> Application node-js-sample created!

```

and now we have a new error. ___   ! [remote rejected] master -> master (pre-receive hook declined)____

```bash
bash-3.2# git push dokku master
  Counting objects: 378, done.
  Delta compression using up to 8 threads.
  Compressing objects: 100% (307/307), done.
  Writing objects: 100% (378/378), 211.25 KiB | 0 bytes/s, done.
  Total 378 (delta 42), reused 378 (delta 42)
  remote: Access denied. No user supplied.
  To dokku@dokku.lazarusco.in:node-js-sample
   ! [remote rejected] master -> master (pre-receive hook declined)
  error: failed to push some refs to 'dokku@dokku.lazarusco.in:node-js-sample'


```

at the suggestion of http://progrium.viewdocs.io/dokku/troubleshooting

I have added to /home/dokku/dokkurc
```
export DOKKU_TRACE=1
```

And now we have more debug information:

```
bash-3.2# git push dokku master
  Counting objects: 378, done.
  Delta compression using up to 8 threads.
  Compressing objects: 100% (307/307), done.
  Writing objects: 100% (378/378), 211.25 KiB | 0 bytes/s, done.
  Total 378 (delta 42), reused 378 (delta 42)
  remote: +++ [[ -z /home/dokku ]]
  remote: +++++ dirname /var/lib/dokku-alt/plugins/acl/verify-command
  remote: ++++ cd /var/lib/dokku-alt/plugins/acl
  remote: ++++ pwd
  remote: +++ CWD=/var/lib/dokku-alt/plugins/acl
  remote: ++ AUTHORIZED_KEYS=/home/dokku/.ssh/authorized_keys
  remote: ++ DEPLOY_ALLOWED=DEPLOY_ALLOWED
  remote: + cat
  remote: + [[ -z '' ]]
  remote: ++ id -un
  remote: + [[ dokku != \r\o\o\t ]]
  remote: + fail 'Access denied. No user supplied.'
  remote: + echo 'Access denied. No user supplied.'
  remote: Access denied. No user supplied.
  remote: + exit 1
  To dokku@dokku.lazarusco.in:node-js-sample
   ! [remote rejected] master -> master (pre-receive hook declined)
  error: failed to push some refs to 'dokku@dokku.lazarusco.in:node-js-sample'
```

and now I had to learn about __SSHCOMMAND__

The documentation is not very clear - and it took me a while to wrap my head around it - but:

SSHCOMMAND is used to create a USERPROFILE on the server that will execute remote commands when authorized by a public/private key pair that has been authorized to execute that command.

This works because your Public/Private key pair is not specific to your username - but in fact will work for any user name on a machine that is willing to accept it as credentials.

so if we want to run a command we are going to remotely alias as LS

we use the following command on the server

```
sshcommand create [NAME_OF_NEW_REMOTE_COMMAND] [SYNTAX]
sshcommand create LS                           "/bin/ls -lR /"
```
the actual command syntax for ___sshcommand create___
------

```bash
root@Modulo:~# sshcommand create LS "/bin/ls -lR /"  
```
when we use this sshcommand create it has the effect of:

* define a new user - called LS
* and makes the server execute the command syntax when that user logs in

Next sshcommand needs to link authorized users to these remote commands via Public/Private key pair


```bash

root@Modulo:~# cat id_rsa.pub  |  sshcommand acl-add LS boo
  e9:96:ed:cf:eb:fc:53:2b:72:75:0f:7c:cc:ec:5f:3c
```

when ssh connects as user LS with a valid key pair the command is executed.

on macbook this command will login to the Server as user LS and my private key. It will then execute command and the results will stream back over the SSH chonnection

```

ssh -v LS@dokku.lazarusco.in

```

it would appear that the sshcommand bindings around the git push are incorrect.


But solving that is a problem for tomorrow.  

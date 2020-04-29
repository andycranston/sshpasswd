# sshpasswd

An expect script to ssh into a host as a user and change that users password.

The `sshpasswd` expect script for UNIX/Linux systems automates the `ssh`
command and logs into a host as a user. Once logged in it issues the
`passwd` command and changes the user password. It then logs out and the
`ssh` command exits.

## Pre-requisites

This is what you will need:

+ A UNIX/Linux system with expect installed
+ `/usr/local/bin/expect` is either the expect executable or a link to it

## Demonstration video

Here is a short (two minutes and twenty seconds) video
demonstrating the `sshpasswd` expect script:

[Changing Linux passwords on multiple hosts using an expect script](https://www.youtube.com/watch?v=fXHX0gGu7XQ)

## Example run

Copy the `sshpasswd.exp` file from the repository to a UNIX/Linux system
that has expect installed and `/usr/local/bin/expect` is either the expect
executable or a link to it.

Make the `sshpasswd.exp` file executable by all:

```
chmod u=rwx,go=rx sshpasswd.exp
```

For this example run we will login to a host called `zappy` as user
`localadm`.  The current password is `secret2020` and the new password
will be `longerpass7+`. Note I have never used these passwords for
anything and never will. This is for example purposes only!

First set the environment variable `CPASS` to the current password:

```
CPASS=secret2020
export CPASS
```

Next set the environment variable `NPASS` to the new password:

```
NPASS=longerpass7+
export NPASS
```

Now run the following command:

```
./sshpasswd.exp zappy localadm
```

Here is example output of a successful run:

```
### PROGRAM sshpasswd.exp STARTING ###
spawn ssh localadm@zappy
localadm@zappy's password:
localadm@zappy:~$ passwd
Changing password for localadm.
(current) UNIX password:
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
localadm@zappy:~$ exit
logout
Connection to zappy closed.
### PASSWORD CHANGE SUCCESSFUL ###
```

## It didn't work when I ran it!

Several things can go wrong. Here are some of the more common causes.

### The format of the shell prompt is "non standard"

The `sshpasswd` expect script assumes the shell prompt
will have the format:

```
username@hostname:-~
```

after a successful login where `username` is the username the
script logged in with and hostname is the hostname of the
system. If your system displays a different prompt (even just a one
character difference) then the `sshpasswd` expect script will not
work.

However, this is easy to fix. For example if the shell prompt is:

```
dns $
```

then run the `sshpasswd` expect script as:

```
./sshpasswd.exp -p 'dns $' zappy localadm
```

### The ssh command asks questions before starting the login process

If the ssh command asks questions such as:

```
The authenticity of host 'zappy (10.1.1.201)' can't be established.
ECDSA key fingerprint is SHA256:YhjajsdY67TyejasjhYThsdjh9JHjasgdh0sdjhryr2.
Are you sure you want to continue connecting (yes/no)?
```

then the `sshpasswd` expect script will fail. This is by design. It could
just, in the above example, answer `yes` for you but this is likely
to be the wrong answer and could have serious consequences.

In a case like this manually login to the host and answer any questions
according to your choice and local security policy, logout and then
try the `sshpasswd` expect script again.

### I get `timeout` and/or `end of file` errors

The `sshpasswd` expect script relies on certain prompts being displayed
in order to work correctly. Your implementation of the `ssh` command,
the shell on the remote system and the `passwd` command on the
remote system might have slightly different prompts to those
coded into the `sshpasswd` script. Have a closer look at what is
displayed and the text in the relevant parts of the `sshpasswd`
expect script. All you might need to do is make a slight change.

If all else fails drop me an email.

## The `allchange.sh` shell script

Also in the repository is a shell script called:

```
allchange.sh
```

It is a simple example of how you could change a user password on
several hosts at once. Note that for such a simple solutuion to work
the user must have the same current password on each system.

If you have to change user passwords on multiple systems then
I recommend you build something with more error checking
for use in your own environments.

## Why do I have to set the CPASS and NPASS environment variables?

Passing passwords in plain text to programs such as the `sshpasswd` expect
script is always going make you have to think about security.  I use
environment variables because I can set them with my `setpw` utility:

[Set Windows/UNIX/Linux environment variables with a password but keep the password hidden](https://github.com/andycranston/setpw)

without them being displayed on the terminal screen or being kept in shell
history files like `.bash_history`. Also when my login session finishes the values of the
environment varables are no longer accessible.

You might have a better way to store passwords and other credentials. If
so please use it and share the product/solution with me as well please!

## With great power ...

If you have got the `sshpasswd` expect script running in your environment
and have been changing several passwords at a time please remember the
age old saying:

> With great power comes great responsibility.

DO NOT be the person who changed the root or other critical password on
all your production servers and then forgot what the new password was!!!

## Contact details

You can contact me via email:

    andy [at] cranstonhub [dot] com

and you can find me on linkedin.com:

[Andy Cranston on Linkedin](https://www.linkedin.com/in/andy-cranston-innovation)

I would like to hear your feedback (positive or negative) on the
`sshpasswd` expect script.

------------------------------------------------

End of README.md

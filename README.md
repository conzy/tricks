
# tricks
Some handy tips and tricks hopefully

##SSH Helper Aliases

I log into servers often where I use a user account that is not my name e.g 

`ssh systemuser@54.53.52.51`

Seems trivial but I have probably typed `ssh systemuser@` thousands of times. The solution is to create a simple function and alias in bash

```bash
function ssh-helper {
        ssh systemuser@$1
}
​
alias sshh='ssh-helper'
```

You can name the alias anything you like. In this case I used sshh 

Another common issue is contacting AWS EC2 instances in a VPC, they may only have a private IP and may only be accessible via some jump host. Can we connect to these more easily? 

Yes

```bash
function ssh-jump-helper {
        ssh -o ProxyCommand="ssh your.jumphost.com -W %h:%p" systemuser@$1
}
​
alias sshj='ssh-jump-helper'
```

What is going on here? Mad hax

`ProxyCommand` Specifies the command to use to connect to the server. 

`-W` Requests that standard input and output on the client be forwarded to host `%h` on port `%p` over the secure channel.

Using this technique we can connect to private RFC1918 addressed within a VPC

`sshj 172.16.1.1`

Its a good idea to obfuscate the port on your jump host. You can specify that in the command also using the `-p` option.  The following code connects to the jumphost on port 4567

`ssh -o ProxyCommand="ssh -p 4567 your.jumphost.com -W %h:%p" systemuser@$1`

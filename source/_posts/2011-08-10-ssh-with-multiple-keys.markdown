---
layout: post
title: "SSH With Multiple Keys"
date: 2011-08-10 21:11
comments: true
categories:
---
I help manage a handfull of servers at work, and I have gotten tired of
typing SSH passwords all the time. Of course, I could just copy my
public key over to each server's `~/.ssh/authorized_keys` file, but I
have to maintain that on every server. Too much work.

Luckily, all of the servers already have a key in their authorized_keys
as part of their build process (for other purposes), and I know the
associated private key. We'll call these the "work" key pair. I don't
want to overwrite my keys with this pair because I use my keys for
personal stuff. It sure would be nice if I could use the work key pair just
for these servers. It turns out I can.

I copied the "work" key pair into my local `.ssh` folder as `work_rsa` and
`work_rsa.pub`. Now I can ssh into the server using:

{% codeblock %}
  $ ssh -i ~/.ssh/work_rsa server1.work_domain.com
{% endcodeblock %}

Yay, no password! However, now my command is ugly. That won't do. We can
fix this using the `~/.ssh/config` file to set individual options for a
specific host. Since all of my work servers are on the same domain I can
set up a wildcard to set the option for all machines on that domain:

{% codeblock SSH Config %}
# Contents of ~/.ssh/config
Host *.work_domain.com
  IdentityFile ~/.ssh/work_rsa
{% endcodeblock %}

{% codeblock %}
  $ ssh server1.work_domain.com
{% endcodeblock %}

Much better!

## Lazy? Me too!

In my ongoing effort to reduce key strokes I set up a bash function for
my most common SSH command:

{% codeblock bash_profile.sh %}
  function sshw() {
    ssh user@$1.work_domain.com
  }
{% endcodeblock %}

Now all I have to do is type the name of the machine without the domain
or user:

{% codeblock %}
  $ sshw server1
{% endcodeblock %}

I hope my diligence in pursuing laziness is helpful to you.


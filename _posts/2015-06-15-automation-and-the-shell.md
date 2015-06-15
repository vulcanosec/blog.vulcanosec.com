---
layout: post
title: "Automation and the Shell"
date:   2015-06-15 21:00:00
image:
    url: /assets/article_images/2015-06-15-automation-and-the-shell/snail.m.jpeg
video: false
comments: true
theme_color: 302F2D

author: 'Dominik Richter'
author_image: "https://avatars0.githubusercontent.com/u/1307529?s=400"
author_link: "https://twitter.com/arlimus"
---

Managing your nodes has seen a wonderful change with the rise of DevOps and its newly found tools. Whether it's written in Python or Ruby or runs on a custom language, we have come far from the olden days of shell-scripting your environment. Under the hood, however, in most cases, we are still true to our trusty shell. It may have become covered and deeply hidden in our modern automation languages, but it's still there. At the end of the day, it is often the most convenient way to talk to your operating system.

Security automation profits heavily from DevOps, while compliance scanning has not yet reached this field. Many people still rely on shell scripts (directly or through through commands run by a scanner) to cover this area. VulcanoSec is changing this field, and bringing the knowledge of development to security testing. Similar to DevOps tools, however, it uses the shell wherever convenient under the hood.

As we will see in this article, there are many cases where this approach make our lives easier. In the end, it all boils down to command execution. We will take a look at the different styles of running commands on your nodes and the consequences they have.

## Remote Execution (case 1)

This is probably one of the oldest cases for automating commands in your environment: A server delivers shell commands via (hopefully) secure channel to the node, which then executes them and reports back the results.

For a simple example, take a server which connects to a client machine via SSH and runs some shell commands:

<pre><code class="bash"
>pssh -h ips.txt -o /tmp/installs apt-get install apache2
</code></pre>

This will install Apache on all your Debian and Ubuntu nodes.

There are some obvious limitations here, as our example shows. What about running this on RedHat, Fedora, SuSe, Windows? It won't yield the desired results.

However, these issues can be solved, if your server knows the target system and can adjust the command it will run. All you have to do, is to tell your server to install:

<pre><code class="ruby"
>package "apache"
</code></pre>

and it would run the appropriate command on each node:

<pre><code class="bash"
># for Ubuntu/Debian:
apt-get install apache2

# for RedHat/CentOS:
yum install httpd

...
</code></pre>

This method doesn't require any additional component running on the client, except for a remote access with shell execution.

## Lokal Agent (case 2)

While the first method sends shell commands to the node, this approach instead creates and runs the commands on the target directly. Usually it involves installing an additional component on your clients, which is called the agent. It will end up running commands just once or regularly throughout its lifetime.

Similar to the example above, the commands may be written in the targeted shell script directly (`apt-get install ...`), or may be embedded in a convenient language (`package 'apache'`). In any case, the common layer often translates them into shell commands, which are then executed.

The source of these commands is also different. Depending on your needs, you may like to control all nodes from a central server. This would allow the agent to retrieve all commands it requires from the server and run them on the local node. Once again, it may retrieves raw shell commands from the server, or a script written in a different language which is then interpreted and translated on the node.

The other approach is a standalone execution of commands. In this model, all commands are pre-installed with the agent and may be udpated by e.g. deployment scripts. In the end, the agent takes these commands, translates them if necessary, and executes everything on the node.

## Alternate execution (case 3)

Taking everything into account we have seen so far, we can mix and combine these solutions to achieve the right fit for our environment. As long as we are able to reach the shell, as a common layer of execution, we are likely able to alter the way in which commands are delivered and executed.

Let's construct a small example: We want to run a security check on a machine. Regularly we would use our server to log into the node and run all commands on the client's shell (case 1). However, some machines may not grant such access. We can offer to install an agent instead, which gets its commands from a central server (case 2). But what if the client doesn't want to install our agent? This may be the case for critical infrastructure, which must be treated carefully.

In this case, we could just hand all shell-commands our server would transfer in case 1, and provide them to the client directly. As this is the least common layer, there is no installation of additional components on the client and the operator could retain full control over what is transferred to the server. If you look at it closely, however, this is just one variant of case 2: You use the client's shell directly and run a different proxy for the work your agent would do. In principle, however, nothing changes.

The real challenge of this case, is to provide the most common layer which requires the least adjustments over time.

## Summary

Managing your nodes remotely has seen different mechanisms, often running on top of the operating system's shell (or equivalent). These may provide their own language, to help operators focus on their goals instead of low-level issues.

While many security professionals are still often scripting on the shell layer directly, there are solutions that take the power and simplicity from development and bringing it to the realm of security and compliance.

At VulcanoSec, we provide a security and compliance scanner, that covers all three models seen above: Remote command execution, local agents, and an alternate execution layer. You can choose your favorite approach based on your environment and infrastructure requirements. If you are already familiar with DevOps solutions, you will feel right at home with this DevSec solution.
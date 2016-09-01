# Add the Agent

## Open a Second Window

We will need to perform actions on both the puppet master and the agent \(www\) node.

## Install Puppet[^1]

1. `pkg install puppet`

  > pkg: Unable to set locale 'en\_US.UTF-8'; locale package may be broken or
  > not installed.  Reverting to C locale.
  > No updates necessary for this image.

2. Optionally check the version of puppet
  `puppet -V`

  > 3.8.6


## Modify SMF config\/server variable for puppet:agent

$$WWW-1$$

1. Wait, you said we need to use the FQDN?  I absolutely did say that. You should absolutely do that outside of the lab. For the sake of simplicity we are using an alt-name for the puppet master certificate.

  `svccfg -s puppet:agent setprop config/server=puppet`




## Refresh puppet:agent

$$WWW-2$$

1. Refreshing puppet:agent loads the configuration changes we just made in SMF
  `svcadm refresh puppet:agent`


## Enable puppet:agent

$$WWW-3$$

1. Remeber, enabling the agent writes the puppet.conf file
  `svcadm enable puppet:agent`


## Mark puppet:agent maintenance

$$WWW-4$$

1. Disable the puppet agent daemon. We want to manually execute the agent for the duration of the lab.

  `svcadm mark maintenance puppet:agent`




## Test agent connection

$$WWW-5$$

1. Test puppet connection, wait 120s for the certificate to be signed on the master
  `puppet agent --test -w 120`




## Sign the agent certificate \(on master\)

1. Get the list of certs waiting to be signed

  $$Command-20$$

  `puppet cert list`

  > "www-0.us.oracle.com" \(SHA256\) 42:77:38:C8:C0:7F:0B:9B:4E:90:F7:EA:2C:76:99:48:CE:63:6B:1D:9D:DA:67:46:06:A3:AB:50:16:3E:CC:23



2. Sign the cert you should only have one certifcate to sign

  $$Command-21$$

  `puppet cert sign www-0.us.oracle.com`

  > Notice: Signed certificate request for www-0.us.oracle.com




## What just happened, again?

\(and why does your screenshot look different than my output\)

\[www-5.1\]

After the certificate is signed the puppet agent will be able to connect to the master. When the first connection is made pluginsync copies all the plugins from the master to the agent.
**Note:** If your agent timed out or you don't feel like waiting you can just run it again\/kill it and run it again.

## Why aren't you using the same prompt on the agent?

It's all about the examples; we'll be addressing that in the next step.

# Review

In this section we took the following steps

1. Installed puppet on the agent
2. Configured, refreshed, enabled and stopped the puppet:agent service
3. Tested the agent connection and waited for the signed cert to become available
4. Signed the cert on the puppet master

[^1]: Completed prior to lab


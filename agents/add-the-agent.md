# Add the Agent

## Open a Second Window

We will need to perform actions on both the puppet master and the agent \(www\) node.

## Install Puppet[^1]


$$
WWW-AGENT-0
$$


1. Install puppet

  `pkg install puppet`

  > pkg: Unable to set locale 'en\_US.UTF-8'; locale package may be broken or
  > not installed.  Reverting to C locale.
  > No updates necessary for this image.

2. Optionally check the version of puppet
  `puppet -V`

  > 3.8.6

![](/assets/AGENT-WWW-000.0.png)


## Modify SMF config\/server variable for puppet:agent


$$
WWW-AGENT-1
$$


1. Wait, you said we need to use the FQDN?  I absolutely did say that. You should absolutely do that outside of the lab. For the sake of simplicity we are using an alt-name for the puppet master certificate.

  `svccfg -s puppet:agent setprop config/server=puppet`

![](/assets/AGENT-WWW-001.0.png)


## Refresh puppet:agent


$$
WWW-AGENT-2
$$


1. Refreshing puppet:agent loads the configuration changes we just made in SMF
  `svcadm refresh puppet:agent`
  ![](/assets/AGENT-WWW-002.0.png)

## Enable puppet:agent


$$
WWW-AGENT-3
$$


1. Remember, enabling the agent writes the puppet.conf file
  `svcadm enable puppet:agent`
  ![](/assets/AGENT-WWW-003.0.png)

## Mark puppet:agent maintenance


$$
WWW-AGENT-4
$$


1. Disable the puppet agent daemon. We want to manually execute the agent for the duration of the lab.

  `svcadm mark maintenance puppet:agent`

  ![](/assets/AGENT-WWW-004.0.png)


## Test agent connection


$$
WWW-AGENT-5
$$


1. Test puppet connection, if there is no signed cert wait 120s and try again for the certificate to be signed on the master[^3]
  `puppet agent --test -w 120`
  ![](/assets/AGENT-WWW-005.0.png)

## Sign the agent certificate \(on master\)


$$
PUP-AGENT-1
$$


1. Get the list of certs waiting to be signed
  `puppet cert list`

  > "www.oracle.lab" \(SHA256\) 42:77:38:C8:C0:7F:0B:9B:4E:90:F7:EA:2C:76:99:48:CE:63:6B:1D:9D:DA:67:46:06:A3:AB:50:16:3E:CC:23[^2]


![](/assets/AGENT-PUP-001.0.png)


$$
PUP-AGENT-2
$$


1. Sign the cert you should only have one certificate to sign
  `puppet cert sign www.oracle.lab`

  > Notice: Signed certificate request for www.oracle.lab

![](/assets/AGENT-PUP-002.0.png)

## What just happened, again?

![](/assets/AGENT-WWW-005.1.png)

After the certificate is signed the puppet agent will be able to connect to the master. When the first connection is made pluginsync copies all the plugins from the master to the agent. I pre-connected the agent then disconnected it, removed some of the files and ran through the steps again. Puppet only copies the files that are missing or changed.
**Note:** If your agent timed out or you don't feel like waiting you can just run it again\/kill it and run it again.

## Why aren't we using the same prompt on the agent?

It's all about the examples

# Review

In this section we took the following steps

1. Installed puppet on the agent
2. Configured, refreshed, enabled and stopped the puppet:agent service
3. Tested the agent connection and waited for the signed cert to become available
4. Signed the cert on the puppet master

[^1]: Completed prior to lab

[^2]: Your SHA256 hash will vary

[^3]: The agent will wait this entire time without a periodic retry


# Add the Agent

## Open a Second Window

We will need to perform actions on both the puppet master and the agent \(www\) node.

## Install the Puppet[^1] Package


$$
WWW-0
$$


1. Install the puppet package:

  `pkg install puppet`

  > pkg: Unable to set locale 'en\_US.UTF-8'; locale package may be broken or
  > not installed.  Reverting to C locale.
  > No updates necessary for this image.

2. Optionally check the version of Puppet:
 
 `puppet -V`

  > 3.8.6

  ![](/images/ADD01-WWW-000-install-puppet.png)


## Modify SMF config\/server variable for puppet:agent


$$
WWW-1
$$


1. Wait, you said we need to use the FQDN?  I absolutely did say that. You should absolutely do that outside of the lab. For the sake of simplicity we are using an alt-name for the puppet master certificate.

  `svccfg -s puppet:agent setprop config/server=puppet`

  ![](/images/ADD01-WWW-001-svccfg.png)


## Refresh the Puppet Agent Service


$$
WWW-2
$$


1. Refreshing puppet:agent loads the configuration changes we just made in SMF:

  `svcadm refresh puppet:agent`
  ![](/images/ADD01-WWW-002-svcadm-refresh.png)

## Enable the Puppet Agent Service


$$
WWW-3
$$


1. Remember, enabling the agent writes the puppet.conf file:

  `svcadm enable puppet:agent`
  ![](/images/ADD01-WWW-003-svcadm-enable.png)

## Put Puppet Agent Service in Maintenance


$$
WWW-4
$$


1. Disable the puppet agent daemon. We want to manually execute the agent for the duration of the lab.

  `svcadm mark maintenance puppet:agent`

  ![](/images/ADD01-WWW-004-svcadm-mark.png)


## Test Puppet Agent Connection


$$
WWW-5
$$


1. Test puppet connection, if there is no signed cert wait 120s and try again for the certificate to be signed on the master[^3]
  `puppet agent --test -w 120`
  ![](/images/ADD01-WWW-005.0-puppet-agent.png)

## Sign the Agent Certificate the Puppet Master


$$
PUP-20
$$


1. Get the list of certs waiting to be signed.

  `puppet cert list`

  > "www.oracle.lab" \(SHA256\) 42:77:38:C8:C0:7F:0B:9B:4E:90:F7:EA:2C:76:99:48:CE:63:6B:1D:9D:DA:67:46:06:A3:AB:50:16:3E:CC:23[^2]


![](/images/ADD01-PUP-020-cert-list.png)


$$
PUP-21
$$


1. Sign the cert you should only have one certificate to sign:


  `puppet cert sign www.oracle.lab`

  > Notice: Signed certificate request for www.oracle.lab.


![](/images/ADD01-PUP-021-cert-sign.png)

## What Just Happened, Again?

\[pluginsync\]

After the certificate is signed, the puppet agent is able to connect to the master. When the first connection is made, pluginsync copies all the plugins from the master to the agent. I pre-connected the agent then disconnected it, removed some of the files and ran through the steps again. Puppet only copies the files that are missing or changed.
**Note:** If your agent timed out or you don't feel like waiting, you can just run it again\/kill it and run it again.

## Why Aren't We Using the Same Prompt on the Agent?

It's all about the examples.

# Review

In this section, we took the following steps:

1. Installed puppet on the agent
2. Configured, refreshed, enabled and stopped the puppet:agent service
3. Tested the agent connection and waited for the signed cert to become available
4. Signed the cert on the puppet master

[^1]: Completed prior to lab

[^2]: Your SHA256 hash will vary

[^3]: The agent will wait this entire time without a periodic retry


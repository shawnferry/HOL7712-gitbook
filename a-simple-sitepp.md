# Creating a very simple site.pp

In the Adding an Agent section we observed a difference in the prompts between the two systems. We will use puppet to distribute a .dotfile to all it's agents.

## Copy the manifest to \/etc\/puppet\/manifests\/site.pp

Command-22

1. Even though this is a simple manifest it is not especially easy to type. We will copy it for this first example.
  `cp /root/HOL7712-Solaris-Puppet/manifests/001-simple-site.pp /etc/puppet/manifests/site.pp`
  ![](/images/SIMPLE01-PUP-022-cp-001.png)

## Execute the agent on WWW server

WWW-6

1. With site.pp on the master running the agent will compile the catalog and apply the desired changes.
  `puppet agent -t`
  ![](/images/SIMPLE01-WWW-006-agent.png)

## Execute puppet agent on the puppet master

Command-23

1. With site.pp on the master running the agent will compile the catalog and apply the desired changes.

  When puppet is executed on the master it is also using site.pp. The content of .zshrc in site.pp does not exactly match the content of file copied by setup.pp.
  `puppet apply -t`

  ![](/images/SIMPLE01-PUP-023-puppet-agent.png)


## Exec zsh to get the new prompt

WWW-7

1. Now that we have created a \/root\/.zshrc run zsh
  `exec zsh`
  ![](/images/SIMPLE01-WWW-007-prompt.png)

## Edit \/etc\/puppet\/manifests\/site.pp on the master

Command-24

1. Wait you said this was simple! Puppet lint shows you an error about variables in sigle quoted strings! The example in 001-simple-site.pp uses the `content` parameter to the file type.  It is technically simple but it isn't a very good implementation.
  ![](/images/SIMPLE01-PUP-024.0-vi-sitepp.png)![](/images/SIMPLE01-PUP-024.1-vi-sitepp.png)

# Simplyfying site.pp by using a module to distribute a file

Writing detailed modules is beyond the scope of this lab. However, we will be utilizing a stub of a module to take advantage of puppet's [file serving capabilities](https://docs.puppet.com/puppet/latest/reference/modules_fundamentals.html#files). Puppet provides the ability to access files from the special path puppet:\/\/\/modules\/&lt;module&gt;\/&lt;filename&gt;. We will use this method to truly simplify the example.

In normal use you might generate a module with `puppet module generate <module-name>`and use that module to store all your custom manifests. We will instead create only a subset of the module contents for these steps.

## Create the partial module directory structure

Command-25

1. We are creating the minimum viable path to achieve this step
  `mkdir -p /etc/puppet/modules/lab/files`
  ![](/images/SIMPLE01-PUP-025-mkdir.png)

## Copy zshrc to the module's files directory

Command-26

1. This will make the file available to agents via the puppet file server at puppet:\/\/\/modules\/lab\/zshrc
  `cp /root/HOL7712-Solaris-Puppet/labfiles/zshrc /etc/puppet/modules/lab/files`
  ![](/images/SIMPLE01-PUP-026-cp-zshrc.png)

## Update site.pp to copy the file

Command-27

1. We will be removing the `$content` definition and `content` parameter and replacing them with a `source` parameter.
  ![](/images/SIMPLE01-PUP-027.0-vi-sitepp.png)
2. Replace `content => $content;`

  with

  `source => 'puppet:///modules/lab/zshrc';`



  ![](/images/SIMPLE01-PUP-027.1-vi-sitepp.png)

3. When you are done your file should look almost identical to this![](/images/SIMPLE01-PUP-027.2-vi-sitepp.png)


## Execute puppet agent on the node

WWW-8

1. When you apply puppet now there will be a change to .zshrc, the file we are copying from is slightly different than the one inlined in 001-simple-site.pp.
  `puppet apply -t`
  ![](/images/SIMPLE01-WWW-008-agent.png)

## Execute puppet agent on the master

Command-28

1. When you apply puppet on the master there will also be a change to .zshrc 
  `puppet apply -t`
  ![](/images/SIMPLE01-PUP-028-agent.png)

## Why did .zshrc change on the master too?

Site.pp applies to all agents of the master including the master if it is configured to use itself as a server.  We will cover basic node segregation in the next sections.

# Review

1. We configured puppet:agent on www 
  1. set config\/server
  2. refresh the service
  3. enabled the service
  4. stopped but did not disable the service

2. Distributed .zshrc from a single manifest

3. Created just enough of a private \(lab\) puppet module to server files

4. Updated site.pp to copy .zshrc to agents from the lab module

5. Noticed that changes in  site.pp affect the agent running on the master and on www


# Configuring Apache

This Solaris release includes an installation of Apache by default. We modify the configuration of the existing install using the puppetlabs-apache Puppet module.

## Step Overview

1. Install puppetlabs-apache
2. Create and publish a solaris repository
  1. We have pre-mirrored a portion of the solaris publisher
  2. Publish the repository to the local environment

3. Enable Apache, Break, then fix the Package Server
  1. Notice that our publisher is no longer working
  2. Reconfigure the package server 
  3. Setup a virtual host and reverse proxy in apache


## 

## Note

We mainly use the `lab_copy`function for these steps. Lab\_copy assembles site.pp from fragments which are discussed individually. Remember that lab\_copy is configured with tab completion. Changes to \/etc\/puppet\/manifests\/site.pp will be lost from step to step.

`lab_copy e002_better`

As you are now more familiar with the steps, we are taking the number of screenshots showing things like simple command execution are reduced.


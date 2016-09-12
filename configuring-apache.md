# Configuring Apache

Solaris 12 includes an installation of apache by default. We will be modifying the configuration of the existing install using the puppetlabs-apache puppet module.

## Step Overview

1. Install puppetlabs-apache
2. Create and publish a solaris repository
  1. Mirror a portion of the solaris publisher
  2. Publish the repository to the local environment

3. Enable Apache, Break, then fix the Pacakge Server
  1. Notice that our publisher is no longer working
  2. Reconfigure the package server 
  3. Setup a virtual host and reverse proxy in apache




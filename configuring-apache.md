# Configuring Apache

Solaris 12 includes an installation of apache by default. We will be modifying the configuration of the existing install using the puppetlabs-apache puppet module.

## Step Overview

1. Install puppetlabs-apache
2. Create a virtual host and serve lab materials
3. Share a local lab IPS repo

### NOTE

Commands in this section will use the additional `--tags` option to `puppet agent`if puppet is run without tags it will continue and perform multiple steps.


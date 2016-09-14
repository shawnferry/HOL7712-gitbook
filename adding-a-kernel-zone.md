# Adding a Kernel Zone

We return to our lab module to provide the needed zone configuration files.

## Update the Lab Module

1. Make a directory to hold the zone configuration
  `mkdir -p /root/oracle-lab/files/zones`
2. Copy the zone configuration and profile to `/root/oracle-lab/files/zones`
  `cp -r /root/HOL7712-Solaris-Puppet/labfiles/zones /root/oracle-lab/files/zones`
3. Build and deploy the lab module. Remember that lab\_build automates `puppet module build`and `puppet module install`
  `lab_build`

## Copy Lab Files

1. Update site.pp from Example 7
  `lab_copy e007_www_zone`
2. Review site.pp changes
  We introduce some simple node classification here. To create the zone only on hosts named `www.*`we add a node classification.

  ```ruby
  ######
  # e007_www_zone/site.pp
  ######

  node /www.*/ {
    zone { 'www-kz01':
         ensure         => 'running',
         zonecfg_export => 'puppet:///modules/lab/zones/www-kz.zcfg',
         config_profile => 'puppet:///modules/lab/zones/www-kz01.xml'
    }
  }
  }
  ```

3. In a separate window run the agent on WWW.
  `puppet agent -t -d`

4. Execution may take some time[^1]


[^1]: Continue with the lab


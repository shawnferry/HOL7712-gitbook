# The Better Way

In the previous example we edited site.pp to copy a file instead of including it in the configuration. We will do that again here and install the `puppetlabs-apache` module IPS package.

1. Copy the lab files to the manifests directory
  `lab_copy e002_better`

2. Run puppet agent to apply the configuration
  `puppet agent -t`
3. Edit \/etc\/puppet\/manifests\/site.pp

  `vi /etc/puppet/manifests/site.pp`

The first resource is a slightly modified version of previous example. We have added file ownership and 
```ruby
######
# e002_better/site.pp
######

# Copy zshrc from the lab module instead of using the 'content' parameter
file { '/root/.zshrc':
  ensure => present,
  owner  => 'root',
  group  => 'root',
  source => 'puppet:///modules/lab/zshrc';
}

# Install the puppet labs apache module, we need it later
package { 'puppetlabs-apache':
  ensure => present
}
```

1. 


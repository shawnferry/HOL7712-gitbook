# Simple Node Classification

Up to this point we have been working with all nodes configured identically.  We will take the combined manifests from the previous steps and group them into logical units.

We also integrate some simple use of hiera for configuration data.

This example is not representative of a well formed manifest. Class and node designations will not be defined in a single in production usage.

A review of the site.pp for Eaxmple 6. Some repetetive sections will not be explored in depth.

Set variables from hiera data

```ruby
$lab_pkg = hiera('lab::pkg',undef)
$lab_homedir = hiera('lab::homedir', '/root')
$lab_sources = hiera('lab::sources')
```

Define a class for resources we want to apply to all hosts. Note the use of variables instead of hard coded strings everywhere.
```ruby
class lab::common {
  # Distribute .zshrc to all systems
  file { "${::lab_homedir}/.zshrc":
...
  }
```

lab::pkg is defined as a hash in the hiera config.
```yaml
lab::pkg: 
  solaris: 
    publisher: solaris
    origin: http://ipkg.us.oracle.com/solaris12/minidev/
```

Instead of hard coding values in manifests the data is stored in
hiera where it can be applied based on node classification.
```ruby
  pkg_publisher { $::lab_pkg['solaris']['publisher']:
    origin => $::lab_pkg['solaris']['origin']
  }
}
```

We need to assign the repo host differently on agents.
```ruby
# All the puppet agents get these resources
class lab::agents {
  host { 'puppet':
    ip           => $::serverip,
    host_aliases => ['repo']
  }
}
```

We only need to install puppetlabs-apache on the master. Pluginsync will copy the module files to the nodes. 
```ruby
# resources only for the master server
class lab::master {
  # Install the apache puppet module on the master
  package { 'puppetlabs-apache':
    ensure => present
  }

  # Create and mount the repo filesystem and mount it
  zfs { 'rpool/repositories':
    ensure     => present,
    mountpoint => '/repositories';
  }
}
```

A subset of the apache configuration described in [Depot Server Apache Configuration](https://docs.oracle.com/cd/E23824_01/html/E21803/apache-config.html) has been implemented via functions of the puppetlabs-apache module.
```ruby
  # configuration for the package server
  class lab::pkg_server {
    # Include webserver to get our baseline webserver
    # configuration.
    include lab::webserver

    # Configure pkg.repod to serve our partial copy of the repo
    # By default pkg/server:default runs on port 80
    svccfg {
      # See svc:/application/pkg/mirror:default
      # for an automated service to maintain a true local mirror
      'svc:/application/pkg/server:default/:properties/pkg/inst_root':
        value   => '/repositories/publisher/solaris',
        require => Pkg_publisher['solaris'],
        notify  => Service['svc:/application/pkg/server:default'];
    }

    # Start the service
    service { 'svc:/application/pkg/server:default':
      ensure => running
    }

    svccfg {
      # Set the port for pkg/server:default to 8080
      'svc:/application/pkg/server:default/:properties/pkg/port':
        # See svc:/application/pkg/mirror:default
        value   => '8080',
        require => Pkg_publisher['solaris'],
        notify  => Service['svc:/application/pkg/server:default'];

      # Set the proxy_base
      'svc:/application/pkg/server:default/:properties/pkg/proxy_base':
        # See svc:/application/pkg/mirror:default
        value   => 'http://repo:8080/solaris',
        require => Pkg_publisher['solaris'],
        notify  => Service['svc:/application/pkg/server:default'];
    }

    # Create htdocs
    file { '/var/apache2/2.4/repo-htdocs':
      ensure => directory,
      before => Apache::Vhost['repo'],
    }

    # Add a very basic index.html
    file { '/var/apache2/2.4/repo-htdocs/index.html':
      content => "It's a Repo!"
    }

    # Set the host_alias to add the 'repo' host
    host { 'puppet-labs.oracle.lab':
      # Where did ipaddres come from ... facter
      ip           => $::ipaddress,
      host_aliases => ['puppet-lab', 'puppet', 'repo']
    }

    # See Oracle Docs for 'Depot Server Apache Configuration'
    apache::vhost { 'repo':
      docroot               => '/var/apache2/2.4/repo-htdocs',
      redirect_source       => ['/solaris'],
      redirect_dest         => ['http://localhost:8080/solaris/'],
      allow_encoded_slashes => 'nodecode',
      filters               => [
        'FilterDeclare  COMPRESS',
        'FilterProvider COMPRESS DEFLATE "%{Content_Type} = \'text/html\'"',
        'FilterProvider COMPRESS DEFLATE "%{Content_Type} = \'application/javascript\'"',
        'FilterProvider COMPRESS DEFLATE "%{Content_Type} = \'text/css\'"',
        'FilterProvider COMPRESS DEFLATE "%{Content_Type} = \'text/plain\'"',
      ],
      proxy_pass            => [
        {
          'path'     => '/solaris',
          'url'      => 'http://localhost:8080',
          'params'   => {'max'                   => '200'},
          'keywords' => ['nocanon']
        }
      ],

    }

  }
```
The following settings will be applied to any node that includes the webserver class.

**WARNING**: Configurations not managed by Puppet will be purged.
```ruby
  # basic configuration for webservers
  class lab::webserver {
    service { 'svc:/network/http:apache24':
      ensure                 => running,
    }
    # We have previously installed the puppetlabs-apache module
    # WARNING: Configurations not managed by Puppet will be purged.
    class { 'apache':
      keepalive              => 'on',
      max_keepalive_requests => '10000',
    }

    # View our webserver content
    # links http://localhost -dump
    }
```

Defining classes doesn't apply the resources to any nodes. After moving *all* of the resources into classes the manifest will be effectively empty. We need to classify the nodes and apply the desired classes. See [Node Definitions](https://docs.puppet.com/puppet/latest/reference/lang_node_definitions.html)

```ruby
    # Set the default node behavior. In conjunction with the common class and no
    # additional nodes this is identical to the previous configurations. i.e. all
    # nodes have the same resources applied
    node default {
      include lab::common
    }

    node /puppet-lab.*/ {
      include lab::common
    }

    node /www.*/ {
      include lab::common
      include lab::webserver
    }
``` 
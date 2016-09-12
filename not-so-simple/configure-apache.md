# Configure Apache

This will be undertaken in two steps.

1. Enable and Configure Apache
2. Create a reverse proxy for our pkg\/server instance

## Enable Apache

1. Copy the lab files
  `lab_copy e004_webserver`
2. Apply the changes. Notice that there are a fair number of changes reported by puppet.
  `puppet agent -t`
3. Check that apache is running
  `links -dump http://localhost`
  \[PUP-E004.3\]
4. The resources for this step are mostly familiar to us already

  We install a text based browser to make testing our apache instance easier

  ```ruby
  ######
  # e004_webserver/site.pp
  ######

  # Install links character based browser
  package { 'web/browser/links': ensure => present }

  service { 'svc:/network/http:apache24':
  ensure                 => running,
  }
  ```

  We call the `apache` class and pass some simple configuration parameters.

5. ```ruby
  # We have previously installed the puppetlabs-apache module
  # WARNING: Configurations not managed by Puppet will be purged.
  class { 'apache':
  keepalive              => 'on',
  max_keepalive_requests => '10000',
  }

  # View our webserver content
  # links http://localhost -dump
  ```

6. In the previous step we found that the list of packages was available from `pkgrepo -s http://puppet/solaris list`What about now?


## Add a reverse proxy for pkg\/server

Since we broke access to our shiny new package server in the previous example how do we fix that?

We could change the port `pkg/server:default`is listening on. But then we would need to make sure everyone uses the correct port. What if we wanted to serve multiple publishers? We would need to add multiple ports and remember them all. Instead we will set up a reverse proxy in Apache. **Note:** Although this is recommended for performance reasons we are configuring only a subset of the recommended configuration.

1. Copy the lab files
  `lab_copy e005_reverse_proxy`
2. Run the puppet agent. Notice again that there are a fair number of changes reported by puppet.
  `puppet agent -t`
3. These steps are a bit more complicated than our previous examples
  1. We modify the configuration of pkg\/server and change the port to 8080 so we no longer clash with Apache's default port and set the proxy\_base. This lays the groundwork for serving multiple repos from a single server[^1]

  ```ruby
   ######
   # e005_reverse_proxy/site.pp
   #####

   # In our previous examples we have enabled a package server and apache
   # both of which use port 80 by default. We will change the port on
   # pkg/server:default and create a proxy in Apache

   svccfg {
     # Set the port for pkg/server:default to 8080
     'svc:/application/pkg/server:default/:properties/pkg/port':
     # See svc:/application/pkg/mirror:default
       value   => '8080',
       require => Pkg_publisher['solaris'],
       notify  => Service['svc:/application/pkg/server:default'];

     'svc:/application/pkg/server:default/:properties/pkg/proxy_base':
     # See svc:/application/pkg/mirror:default
       value   => 'http://repo/publisher',
       require => Pkg_publisher['solaris'],
       notify  => Service['svc:/application/pkg/server:default'];
   }
  ```

  We create htdocs so we can show our own it work's page at the top level of the virtual host.

  ```ruby
   # Create htdocs
   file { '/var/apache2/2.4/repo-htdocs':
     ensure => directory,
     before => Apache::Vhost['repo'],
   }

   # Add an index.html
   file { '/var/apache2/2.4/repo-htdocs/index.html':
     content => "It's a Repo!"
   }

   host { 'puppet-labs.oracle.lab':
     # Where did ipaddres come from ... facter
     ip           => $::ipaddress,
     host_aliases => ['puppet-lab', 'puppet', 'repo']
   }
  ```


Add the virtual host to serve the repo. The `redirect_source`, `redirect_dest`, and `proxy_pass` parameters send the requests to the desired destination.

```ruby

    # See Oracle Docs for 'Depot Server Apache Configuration'
    apache::vhost { 'repo':
      docroot               => '/var/apache2/2.4/repo-htdocs',
      redirect_source       => ['/publisher'],
      redirect_dest         => ['http://localhost:8080/publisher'],
      allow_encoded_slashes => 'nodecode',
      filters               => [
        'FilterDeclare  COMPRESS',
        'FilterProvider COMPRESS DEFLATE "%{Content_Type} = \'text/html\'"',
        'FilterProvider COMPRESS DEFLATE "%{Content_Type} = \'application/javascript\'"',
        'FilterProvider COMPRESS DEFLATE "%{Content_Type} = \'text/css\'"',
        'FilterProvider COMPRESS DEFLATE "%{Content_Type} = \'text/plain\'"',
      ],
      proxy_pass            => [
        { 'path'     => '/publisher',
          'url'      => 'http://localhost:8080',
          'params'   => {'max'                         => '200'},
          'keywords' => ['nocanon']
        }
      ],

    }
```

---

[^1] An exercise left to the reader


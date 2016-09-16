# Create A Repository

1. Copy the lab files, we can see two fragments concatenated into site.pp

  1. fragments/10_e002_better

  2. fragments/10_e003_publisher



`lab_copy e003_publisher`

> Notice: Scope(Class[main]): Building and copying lab files for
> 
> Notice: Compiled catalog for puppet-lab.oracle.lab in environment production in 1.70 seconds
> 
> Notice: /Stage[main]/E003_publisher/Concat::Fragment[e003_publisher]/File[/var/lib/puppet/concat/e003_publisher/fragments/10_e003_publisher]/content: content changed '{md5}1f96e3442afda580f8aef1257fd1469c' to '{md5}76b88c7f177b65b8dd9a682e2960bd19'
> 
> Notice: /Stage[main]/Main/Rsync::Get[e003_publisher]/Exec[rsync e003_publisher]/returns: executed successfully
> 
> Notice: /Stage[main]/E002_better/Concat::Fragment[e002_better]/File[/var/lib/puppet/concat/e003_publisher/fragments/10_e002_better]/content: content changed '{md5}727bfa4a21d9f2642d7fc6dfbe18cb92' to '{md5}60b6f41f91148d10ea03e565bba32ccc'
> 
> Notice: /Stage[main]/Main/Concat[e003_publisher]/Exec[concat_e003_publisher]/returns: executed successfully
> 
> Notice: /Stage[main]/Main/Concat[e003_publisher]/Exec[concat_e003_publisher]: Triggered 'refresh' from 2 events
> 
> Notice: /Stage[main]/Main/Concat[e003_publisher]/File[e003_publisher]/content: content changed '{md5}60b6f41f91148d10ea03e565bba32ccc' to '{md5}cedf8b080dc479760185262800995944'
> 
> Notice: Finished catalog run in 2.73 seconds

1. Apply the updated manifest

  `puppet agent -t`

2. Verify the repository has packages[^1] both the list from the filesystem and the list from `pkg/server:default `should return the same results.
  `pkgrepo -s /repositories/publisher/solaris list`

  `pkgrepo -s http://puppet/solaris list`
  > ```asciidoc
  > PUBLISHER NAME                                          O VERSION
  > solaris   developer/versioning/git                        2.7.4-5.12.0.0.0.106.0:20160821T234149Z
  > solaris   editor/vim                                      7.4-5.12.0.0.0.106.0:20160821T234636Z
  > solaris   library/libevent                                2.0.22-5.12.0.0.0.107.0:20160906T170331Z
  > solaris   network/rsync                                   3.1.2-5.12.0.0.0.106.0:20160822T001642Z
  > solaris   service/network/load-balancer/ilb               5.12-5.12.0.0.0.106.3:20160822T004826Z
  > solaris   system/management/puppet/puppetlabs-apache      1.8.1-5.12.0.0.0.106.0:20160822T002846Z
  > solaris   web/browser/links                               2.12-5.12.0.0.0.106.0:20160822T003120Z
  > ```

3. We can look at either `/root/HOL7712-Solaris-Puppet/manifests/e003_publisher/site.pp`
  or `/etc/puppet/manifests/site.pp` . We will focus on the latter as it contains only the new resources for this step.


We modify the pre-existing zfs filesystem /rpool/repositories and mount it at /repositories

```ruby
######
# e003_publisher/site.pp
######

# Create the repositories filesystem and mount it at /repositories
# In the lab /rpool/repositories will already exist this will only change the
# mountpoint. We have also pre-cached a small number of packages
zfs { 'rpool/repositories':
  ensure     => present,
  mountpoint => '/repositories';
}
```

Use the repository we just create as a source for our packages

```ruby
$lab_pkg = hiera('lab::pkg',undef)
# Configure the publisher for the lab
pkg_publisher { 'solaris':
  origin  => [
    '/repositories/publisher/solaris',
    $lab_pkg['solaris']['origin']
  ],
  require => Zfs['rpool/repositories'];
}
```

Tell pkg/server where the repo we want to share lives and start sharing it.

```ruby
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

# View the list of packages in the local repository
# pkgrepo -s http://localhost/solaris list
```

[^1]: We already know the repository is valid. pkg set-publisher won't let you add a repository that doesn't contain valid repository information.


# Solaris and Puppet

IPS Packages for Puppet in Solaris

Package management in Solaris is via the Image Packaging System.
Oracle patches, builds, and ships a number of packages for Puppet. Puppet is
installed via `pkg install puppet`and a minimal subset of providers are
automatically installed.

The full list of **pre-packaged** and **patched** Puppet modules available in
Solaris 12:

* puppet\/nanliu-staging[^1]
* puppet\/openstack-cinder
* puppet\/openstack-glance
* puppet\/openstack-heat
* puppet\/openstack-horizon
* puppet\/openstack-ironic
* puppet\/openstack-keystone
* puppet\/openstack-neutron
* puppet\/openstack-nova
* puppet\/openstack-openstacklib
* puppet\/openstack-swift
* puppet\/openstack-vswitch
* puppet\/oracle-solaris\_providers[^1]
* puppet\/puppetlabs-apache[^1]
* puppet\/puppetlabs-concat[^1]
* puppet\/puppetlabs-inifile[^1]
* puppet\/puppetlabs-mysql
* puppet\/puppetlabs-ntp[^1]
* puppet\/puppetlabs-rabbitmq
* puppet\/puppetlabs-rsync[^1]
* puppet\/puppetlabs-stdlib[^1]
* puppet\/saz-memcached

## Puppet Support for Solaris

Puppet has had support for Solaris since Solaris 10 including the following types:

* zfs - Manage zfs. Create, destroy, and set properties on zfs instances.
* zone - Manage zones
* zpool - Manage zpools. Create and delete zpools.
* package - Install and remove packages
* service - enable and disable services

Oracle has extended that support with additional types for Solaris in
the [oracle-solaris\_providers ](https://github.com/oracle/puppet-solaris_providers)Puppet module. As of this writing, the module is not available on the Puppet
Forge.

## Oracle Additions to Puppet

Types provided by oracle-solaris\_providers[^2]:

* Boot Environments via beadm

  * [boot\_environment](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/boot_environment.md)

* Naming Services via svccfg, svcprop

  * [dns](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/dns.md)
  * [ldap](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ldap.md)
  * [nis](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/nis.md)
  * [nsswitch](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/nsswitch.md)

* Image Pacakging System \(IPS\) configuration via pkg

  * [pkg\_facet](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/pkg_facet.md)
  * [pkg\_mediator](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/pkg_mediator.md)
  * [pkg\_publisher](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/pkg_publisher.md)
  * [pkg\_variant](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/pkg_variant.md)

* Solaris Integrated Load Balancer \(ILB\) via ilbadm

  * [ilb\_server](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ilb_server.md)
  * [ilb\_servergroup](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ilb_servergroup.md)
  * [ilb\_healthcheck](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ilb_healthcheck.md)
  * [ilb\_rule](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ilb_rule.md)

* Solaris Elastic Virtual Switch \(EVS\) via evsadm

  * [evs](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/evs.md)
  * [evs\_ipnet](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/evs_ipnet.md)
  * [evs\_properties](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/evs_properties.md)
  * [evs\_vport](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/evs_vport.md)

* Service Management Facility \(SMF\) Properties via svccfg, svcprop

  * [svccfg](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/svccfg.md)

* IP Interface Configuration via ipadm

  * [address\_object](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/address_object.md)
  * [address\_properties](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/address_properties.md)
  * [interface\_properties](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/interface_properties.md)
  * [ip\_interface](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ip_interface.md)
  * [ipmp\_interface](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ipmp_interface.md)
  * [protocol\_properties](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/protocol_properties.md)
  * [vni\_interface](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/vni_interface.md)

* Datalink Management via dladm

  * [etherstub](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/etherstub.md)
  * [ip\_tunnel](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ip_tunnel.md)
  * [link\_aggregation](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/link_aggregation.md)
  * [link\_properties](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/link_properties.md)
  * [solaris\_vlan](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/solaris_vlan.md)
  * [vnic](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/vnic.md)

* ZFS via chmod

  * [zfs\_acl](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/zfs_acl.md)
  * [system\_attributes](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/system_attributes.md)


Oracle Solaris Providers override the core Puppet providers for:

* Zones via zoneadm, zonecfg
  * [zone](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/zone.md)


## Access to Oracle Providers

The available IPS oracle-solaris\_providers package will lag behind the github repository and Puppet Forge modules. While those are available for you to use Oracle support channels extend only to packages shipped via IPS.

[^1]: Installed as a dependency of the puppet IPS package

[^2]: See https:\/\/github.com\/oracle\/puppet-solaris\_providers\/blob\/1.2.x\/README.md for the most up to date list of providers. Links here are to the current development branch.


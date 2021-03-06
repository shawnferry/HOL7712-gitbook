# Solaris and Puppet

IPS Packages for Puppet in Solaris

Package management in Solaris is via the Image Packaging System.
Oracle patches, builds, and ships a number of packages for Puppet. Puppet is
installed via `pkg install puppet`and a minimal subset of providers are
automatically installed.

The full list of **pre-packaged** and **patched** Puppet modules available in
Solaris 12:

| Automatically Installed Dependency  |                               |
| ----------------------------------- | ----------------------------- |
| puppet/oracle-solaris_providers[^1] | puppet/openstack-cinder       |
| puppet/nanliu-staging[^1]           | puppet/openstack-glance       |
| puppet/puppetlabs-concat[^1]        | puppet/openstack-heat         |
| puppet/puppetlabs-inifile[^1]       | puppet/openstack-horizon      |
| puppet/puppetlabs-ntp[^1]           | puppet/openstack-ironic       |
| puppet/puppetlabs-stdlib[^1]        | puppet/openstack-keystone     |
| puppet/puppetlabs-apache            | puppet/openstack-neutron      |
| puppet/puppetlabs-mysql             | puppet/openstack-nova         |
| puppet/puppetlabs-rabbitmq          | puppet/openstack-openstacklib |
| puppet/puppetlabs-rsync             | puppet/openstack-swift        |
| puppet/saz-memcached                | puppet/openstack-vswitch      |

## Puppet Support for Solaris

Puppet has had support for Solaris since Solaris 10 and currently includes the
following types:

* zfs - Manage zfs. Create, destroy, and set properties on zfs instances.
* zone - Manage zones
* zpool - Manage zpools. Create and delete zpools.
* package - Install and remove packages
* service - enable and disable services

Oracle has extended that support with additional types for Solaris in the
[oracle-solaris_providers ](https://github.com/oracle/puppet-solaris_providers)
Puppet module. As of this writing, the module is not available on the Puppet
Forge.

## Oracle Additions to Puppet

Types provided by oracle-solaris_providers[^2]:

#### Boot Environments via beadm
| docs |
| ---------------------------------------- |
| [boot_environment](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/boot_environment.md) |

#### Naming Services via svccfg, svcprop
| docs | docs |
| ---------------------------------------- | ---------------------------------------- |
| [dns](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/dns.md) | [nis](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/nis.md) |
| [ldap](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ldap.md) | [nsswitch](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/nsswitch.md) |

#### Image Pacakging System (IPS) configuration via pkg
| docs | docs |
| ---------------------------------------- | ---------------------------------------- |
| [pkg_facet](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/pkg_facet.md) | [pkg_mediator](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/pkg_mediator.md) |
| [pkg_publisher](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/pkg_publisher.md) | [pkg_variant](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/pkg_variant.md) |

#### Solaris Integrated Load Balancer (ILB) via ilbadm
| docs | docs |
| ---------------------------------------- | ---------------------------------------- |
| [ilb_server](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ilb_server.md) | [ilb_servergroup](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ilb_servergroup.md) |
| [ilb_healthcheck](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ilb_healthcheck.md) | [ilb_rule](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ilb_rule.md) |

#### Solaris Elastic Virtual Switch (EVS) via evsadm
| docs | docs |
| ---------------------------------------- | ---------------------------------------- |
| [evs](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/evs.md) | [evs_ipnet](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/evs_ipnet.md) |
| [evs_properties](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/evs_properties.md) | [evs_vport](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/evs_vport.md) |

#### Service Management Facility (SMF) Properties via svccfg, svcprop
| docs |
| ---------------------------------------- |
| [svccfg](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/svccfg.md) |

#### IP Interface Configuration via ipadm
| docs | docs |
| ---------------------------------------- | ---------------------------------------- |
| [address_object](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/address_object.md) | [address_properties](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/address_properties.md) |
| [interface_properties](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/interface_properties.md) | [ip_interface](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ip_interface.md) |
| [ipmp_interface](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ipmp_interface.md) | [protocol_properties](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/protocol_properties.md) |
| [vni_interface](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/vni_interface.md) |                                          |

#### Datalink Management via dladm
| docs | docs |
| ---------------------------------------- | ---------------------------------------- |
| [etherstub](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/etherstub.md) | [ip_tunnel](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/ip_tunnel.md) |
| [link_aggregation](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/link_aggregation.md) | [link_properties](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/link_properties.md) |
| [solaris_vlan](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/solaris_vlan.md) | [vnic](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/vnic.md) |

#### ZFS via chmod
| docs | docs |
| ---------------------------------------- | ---- |
| [zfs_acl](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/zfs_acl.md) | [system_attributes](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/system_attributes.md)|


Oracle Solaris Providers override the core Puppet providers for:

#### Zones via zoneadm, zonecfg
| docs |
| ---------------------------------------- |
| [zone](https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/doc/zone.md) |


## Access to Oracle Providers

The available IPS oracle-solaris_providers package will lag behind the github
repository and Puppet Forge modules. While those are available for you to use
Oracle support channels extend only to packages shipped via IPS.

<!-- Footnotes must be one contiguous line to render properly -->
[^1]: Installed as a dependency of the puppet IPS package

[^2]: See https://github.com/oracle/puppet-solaris_providers/blob/1.2.x/README.md for the most up to date list of providers. Links here are to the current development branch.


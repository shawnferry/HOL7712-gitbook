# Setup

Due to constraints on time and variability in the physical lab, some setup steps have been completed on the lab VMs. The steps in this chapter perform the same pre-work in a fresh VM. During the lab, Puppet will skip the portions that have been completed.

### Steps Overview

1. Login to puppet server
2. Install puppet[^1]
3. Examine HOL7712-Solaris-Puppet\/setup.pp
4. Apply HOL7712-Solaris-Puppet\/setup.pp[^2]
5. Examine HOL7712-Solaris-Puppet\/setup.pp
6. Validate invalid.pp manifest
7. Style Check invalid.pp
8. Fix invalid.pp
9. Configure puppet:master and puppet:agent
10. Enable puppet:master and puppet:agent
11. Review puppet.conf
12. Put puppet:agent into maintenance mode
13. Test puppet agent
14. Correct configuration
15. Test puppet agent

**NOTE:**
If you are trying this outside the lab and need proxies, you must export your http\/https\_proxy in your shell before trying to install a gem with the gem package provider.

```
$env = [
  # HOME needs to be defined for Vundle install
  "HOME=${lab_homedir}",
  # Proxies must be exported in your shell
  # for gem install
  'http_proxy=your.proxy.com:80',
  'https_proxy=your.proxy.com:443'
]
```

[^1]: Completed prior to lab start

[^2]: Portions completed prior to lab start


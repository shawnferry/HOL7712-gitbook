# Puppet is (should be) Idempotent

When you are executing Puppet manifests, nothing should change on repeated runs of the agent or application of the manifest.

## Why Was There a Change Just Now?

![](assets/IDEMP-PUP-000.0.png)

1. Apply setup.pp again:

   'puppet apply HOL7712-Solaris-Puppet/setup.pp'

When you applied setup.pp, why was there a change the second time but not the third or Nth time? If you updated invalid.pp in the previous steps, it will have been overwritten with the original file content.

After the system is in the state described in the setup.pp manifest, additional manifest applications will not change anything.

## Why Wasn't There a Change This Time?

All the resources in setup.pp are in the desired state, no changes are made by Puppet.
![](assets/IDEMP-PUP-000.1.png)

## Where isn't Puppet Idempotent?

If you write your manifests correctly, puppet is always idempotent[^1]. If you are using the exec type look to the creates and unless parameters to keep puppet from _making changes_ on every manifest application.

### This resource make changes until the Vundle.vim directory is created

```ruby
exec { 'vundle install':
command     => "/usr/bin/git clone 
  https://github.com/VundleVim/Vundle.vim.git 
  ${lab_homedir}/.vim/bundle/Vundle.vim",
creates     => "${lab_homedir}/.vim/bundle/Vundle.vim",
environment => $env,
before      => Exec['vundle plugins'];
}
```

### This resource reports a _change_ on every execution

```ruby
exec { 'foo':
  command => 'touch /tmp/foo'
}

```

![](assets/IDEMP-PUP-000.2.png)

### This resource only executes the command if /tmp/foo is absent

```ruby

exec { 'foo':
 command => 'touch /tmp/foo',
 creates => '/tmp/foo';
}
```

![](assets/IDEMP-PUP-000.3.png)

## Why Should You Care?

For now,  you don't care. You can however apply manifests as many times as you want and it shouldn't make changes after the system is in the expected state.

Later, when you are managing your multi-server environment, you will see reports of changes being made on every run.

See also: [Introduction to Puppet](https://docs.puppet.com/guides/introduction.html) and [Puppet Type Reference](https://docs.puppet.com/puppet/latest/reference/type.html)

[^1]: You may see an example in this lab where a provider is unexpectedly not idempotent.


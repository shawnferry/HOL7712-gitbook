# Getting to know the environment

We will be working in terminal for this lab using the Vim editor. The demo steps will be performed in a terminal that very closely matches the screenshots used in this document. The appearance of your system should be close to those shown here.

## Change to root's home directory

1. All commands in the setup steps are executed from root's home directory. \/root

  `cd /root`


## View setup.pp


$$
PUP-1
$$


1. We are not making any changes to setup.pp, briefly note the appearance of the file at this time. This fairly simple manifest contains a set of resource definitions used to bring a fresh system into the starting state for the lab.
  `vi HOL7712-Solaris-Puppet/setup.pp`

![](/images/SETUP-001-vi-setup.png)![](/images/SETUP-002-setup-before.png)

## Apply the setup.pp manifest


$$
PUP-2
$$


1. Puppet informs us of the changes it is making as it goes.

  `puppet apply -t HOL7712-Solaris-Puppet/setup.pp`


![](/images/SETUP-002-apply-setup.png)

### What does setup.pp do? What did apply do?

1. Set variables

  ```ruby
  $lab_homedir = '/root'
  $labdir = "${lab_homedir}/HOL7712-Solaris-Puppet"
  ```

2. Install packages via the package resource using Solaris pkg and a gem via the gem provider

  ```ruby
  $packages = [ 'git', 'editor/vim' ]
  package { $packages:
  ensure => present
  }

  # Install the puppet-lint gem via the package type with the gem provider
  package { 'puppet-lint':
  ensure   => present,
  provider => 'gem';
  }
  ```

3. Modify and copy .dotfiles, create various directories

  ```ruby
  # Make puppet-lint available on our path
  file_line { 'ruby_bin_path':
  path => "${lab_homedir}/.profile",
  line => 'export PATH=$PATH:/usr/ruby/2.1/bin';
  }

  # Copy the lab .vimrc to /root
  file {
  "${lab_homedir}/.vimrc":
  ensure => present,
  source => "${labdir}/labfiles/vimrc";
  }
  ```

4. Install a vim plugin manager and a number of plugins

  ```ruby
  exec { 'vundle install':
  command     => "/usr/bin/git clone \
  https://github.com/VundleVim/Vundle.vim.git \
  ${lab_homedir}/.vim/bundle/Vundle.vim",
  creates     => "${lab_homedir}/.vim/bundle/Vundle.vim",
  environment => $env,
  before      => Exec['vundle plugins'];
  }

  # Install Vundle Plugins
  # This is not a good example
  exec { 'vundle plugins':
  command     => '/usr/bin/vim -i NONE -c VundleInstall -c quitall',
  creates     => "${lab_homedir}/.vim/bundle/vim-puppet",
  environment => $env;
  }
  ```


### Restart your Shell

1. We have modified the PATH and PROMPT for root's shell. We want these changes to take effect now.
  `exec zsh`



## View setup.pp


$$
PUP-3
$$


1. Once setup.pp has be applied differences will be apparent in vim. The setup.pp file content is identical but the display now includes line numbers, a column indicator for 80 columns, and syntax highlighting.

  `vi HOL7712-Solaris-Puppet/setup.pp`



![](/images/SETUP-003-vi-setup.png)![](/images/SETUP-003-setup-after.png)

## Let Puppet Help You

### Syntax Validation


$$
PUP-4
$$


Setup.pp delivered a file into \/root called invalid.pp but how do we know that it is invalid?

1. You can attempt to apply the manifest directly
  `puppet apply invalid.pp`

> Error: Could not parse for environment production: Syntax error at 'ensure'; expected '}' at \/root\/invalid.pp:10 on node puppet-0.us.oracle.com
> 
> Error: Could not parse for environment production: Syntax error at 'ensure'; expected '}' at \/root\/invalid.pp:10 on node puppet-0.us.oracle.com

![](/images/SETUP-004-apply-invalid.png)


$$
PUP-5
$$


Attempting to apply the manifest may only make sense on a subset of your nodes.

1.  There is a better way!
  `puppet parser validate invalid.pp`

> Error: Could not parse for environment production: Syntax error at 'ensure'; expected '}' at \/root\/invalid.pp:10 on node puppet-0.us.oracle.com
> 
> Error: Could not parse for environment production: Syntax error at 'ensure'; expected '}' at \/root\/invalid.pp:10 on node puppet-0.us.oracle.com

![](/images/SETUP-005-parser-validate.png)

Why is this better?

* Catch errors before they are promoted to production

* Breaking the manifest on the master will prevent agents from running


### Puppet Lint


$$
PUP-6
$$


[**Puppet lint**](http://puppet-lint.com/) checks the manifest against [**The Puppet Language Style Guide**](https://docs.puppet.com/guides/style_guide.html "Puppet Style Guide"), to ensure readability and uniformity. The puppet-lint gem installed by setup.pp makes the command `puppet-lint`available on the system.

![](/images/SETUP-006-puppet-lint.png)

### Is there a better way to validate and lint my puppet code?


$$
PUP-7
$$


Yes! and I'm glad you asked.

Setup.pp has added inline validation and linting via syntastic.

## ![](/images/SETUP-007-vi-invalid.png)Fix the syntax error

Syntastic highlights the error at line 10 of invalid.pp after running `puppet parser --validate` automatically for you. The text of the error is provided in the error window at the bottom of the screen.

1. To fix the error add the missing ':' after 'git' on line 9 in the screenshot.

  ```ruby
  package { 'git':
  ```

2. Write the file `ESC :w`

3. The error indicator is cleared

  XXX: Update this image for new invalid.pp


![](/images/SETUP-006.1-lint-before.png)

### Fix the puppet-lint warnings

Syntastic highlights the warnings at lines 17 and 18 from automatically executing puppet-lint. The puppet-vim and tabular plugins help maintain code which adheres to the style guide.

1. To clear the warnings add a new parameter on the last line of the resource

  `creates => '/tmp/foo',`

2. As you type =&gt; the whole block of code should be aligned for you automatically.

3. Write the file `ESC :w`

4. The warning indicators are cleared

  XXX: update image


![](/images/SETUP-006.2-lint-after.png)


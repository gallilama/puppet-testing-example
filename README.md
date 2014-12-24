puppet-testing-example
======================

Create a Puppet module `puppet module generate`, and test it with `puppetlabs_spec_helper`.

# Ruby Environment
Using RVM to control Ruby version, and a custom gemset to keep things tidy.

I had previously installed Ruby 2.1.5, here's what I see

```
$ rvm list

rvm rubies

=* ruby-2.1.5 [ x86_64 ]

# => - current
# =* - current && default
#  * - default
```

Create a gemset where we can install our Ruby Gems

```
$ rvm gemset create puppet

$ rvm gemset list
gemsets for ruby-2.1.5 (found in /Users/cgalli/.rvm/gems/ruby-2.1.5)
=> (default)
   global
   puppet
   ...
```

Use the installed Ruby and the gemset together

```
$ rvm use ruby-2.1.5@puppet
```


# Create Module
Use puppet's module generator to bootstrap the directories and files.

You can run `puppet help module generate` for usage info.

```
// cd into this repo, but you could do this anywhere.
$ puppet module generate --skip-interview gallilama-hello

Notice: Generating module at /Users/cgalli/projects/puppet-testing-example/gallilama-hello...
Notice: Populating templates...
Finished; module generated in gallilama-hello.
gallilama-hello/Gemfile
gallilama-hello/manifests
gallilama-hello/manifests/init.pp
gallilama-hello/metadata.json
gallilama-hello/Rakefile
gallilama-hello/README.md
gallilama-hello/spec
gallilama-hello/spec/classes
gallilama-hello/spec/classes/init_spec.rb
gallilama-hello/spec/spec_helper.rb
gallilama-hello/tests
gallilama-hello/tests/init.pp
```

## Bundle

```
cd gallilama-hello
bundle install
```

## Kick the Tires
We've got some useful things at this point

```
rake
rake lint
// etc
```

## First gotcha: .fixutres.yml
But `rake spec` can't find our module.

```
rake spec

...

Failures:

  1) hello with defaults for all parameters
     Failure/Error: it { should contain_class('hello') }
     Puppet::Error:
       Could not find class hello for <blah> on node <blah>

...
```

The boilerplate is missing a critical file, `.fixtures.yml`, so create that file.

```
fixtures:
  repositories:
    # you can put external code dependancies here
    #'stdlib': 'git://github.com/puppetlabs/puppetlabs-stdlib'
  symlinks:
    # This is the missing magic for rake spec.
    # Change 'hello' to the second part of the name you gave when generating your module
    # e.g. puppet module generate gallilama-hello
    # Don't modify "#{source_dir}"
    'hello': "#{source_dir}"
```

## Addressing deprecation
With `.fixtures.yml` in place, `rake spec` should run the boilerplate spec, but I receive several deprecation warnings.

TODO: Track down, and address warnings.

## More Testing
TODO: Add more complexity to module, and specs.


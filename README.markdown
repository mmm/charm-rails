
# juju charm to deploy a user-defined rails app

This is an example 
[juju](http://juju.ubuntu.com)
charm to deploy a user-defined rails app
directly from revision control.


# Using this charm

First, copy a `etc/sample-config.yaml` over to something like `~/myapp.yaml` to add info about your specific app.

Then deploy a stack

    $ juju deploy mysql
    $ juju deploy --repository ~/charms --config ~/myapp.yaml local:rails myapp
    $ juju deploy haproxy

relate them

    $ juju add-relation mysql myapp
    $ juju add-relation myapp haproxy

scale up your app if you'd like

    $ juju add-unit -n3 myapp

open it up to the outside world

    $ juju expose haproxy

Find the haproxy instance's public URL from 

    $ juju status


## What the charm does

During the `install` hook,

- installs ruby et al
- clones your rails app from the repo specified in `app_repo`
- runs `rvm` and `bundler` to install rails and friends according to your `Gemfile`
- writes out passenger config
- sits back and waits to be joined to a database

when related to a `mysql` service, the charm

- configures db access 
- starts passenger


## charm configuration

Configurable aspects of the charm are listed in `config.yaml`
and can be set by either editing the default values directly
in the yaml file or passing a `myapp.yaml` configuration
file during deployment

    $ juju deploy --repository ~/charms --config ~/myapp.yaml local:rails myapp


# TODO

- split this into `apache-passenger` primary and a `rails` subordinate charms.  The `rails` part should swap out `website-relation-` hooks and instead write a handful of `passenger-relation-` and `unicorn-relation-` hooks... so it can run against various different frontends.

- this could probably easily generalize into a `rack-app` charm that'd run rails, sinatra, etc... anything with a `Gemfile` that hooks up to a rack-aware primary service.

- write charm `/tests` for this

- also add default `Gemfile` so a deployment with no config can be used as a rails development environment


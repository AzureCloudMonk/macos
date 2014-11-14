# Packer templates for Mac OS X
[![Build Status](https://box-cutter.ci.cloudbees.com/buildStatus/icon?job=osx-vm)](https://box-cutter.ci.cloudbees.com/job/osx-vm/)

### Overview

This repository contains templates for Mac OS X that can create
Vagrant boxes using Packer.

## Current Boxes

* osx1010 - Yosemite headless, VMware 7.4GB
* osx109 - Mavericks headless, VMware 8.7GB
* osx108 - Mountain Lion headless, VMware 7.4GB
* osx107 - Lion headless, VMware 8.6GB

* osx1010 - Yosemite desktop, VMware 7.4GB
* osx109 - Mavericks desktop, VMware 8.1GB
* osx108 - Mountain Lion desktop, VMware 7.4GB
* osx107 - Lion desktop, VMware 8.5GB

## Building the Vagrant boxes

To build all the boxes, you will need both VMware Fusion and VirtualBox  installed.

A GNU Make `Makefile` drives the process via the following targets:

    make        # Build all the box types (VMware & VirtualBox)
    make test   # Run tests against all the boxes
    make list   # Print out individual targets
    make clean  # Clean up build detritus
    
### Tests

The tests are written in [Serverspec](http://serverspec.org) and require the
`vagrant-serverspec` plugin to be installed with:

    vagrant plugin install vagrant-serverspec
    
The `Makefile` has individual targets for each box type with the prefix
`test-*` should you wish to run tests individually for each box.

Similarly there are targets with the prefix `ssh-*` for registering a
newly-built box with vagrant and for logging in using just one command to
do exploratory testing.  For example, to do exploratory testing
on the VirtualBox training environmnet, run the following command:

    make ssh-box/virtualbox/osx109-nocm.box
    
Upon logout `make ssh-*` will automatically de-register the box as well.

### Makefile.local override

You can create a `Makefile.local` file alongside the `Makefile` to override
some of the default settings.  It is most commonly used to override the
default configuration management tool, for example with Chef:

    # Makefile.local
    CM := chef

Changing the value of the `CM` variable changes the target suffixes for
the output of `make list` accordingly.

Possible values for the CM variable are:

* `nocm` - No configuration management tool
* `chef` - Install Chef
* `chefdk` - Install Chef Development Kit
* `puppet` - Install Puppet
* `salt`  - Install Salt

You can also specify a variable `CM_VERSION`, if supported by the
configuration management tool, to override the default of `latest`.
The value of `CM_VERSION` should have the form `x.y` or `x.y.z`,
such as `CM_VERSION := 11.12.4`

Another use for `Makefile.local` is to override the default locations
for the Mac OS X installer files.

For Mac OS X, the installer path variables are:

* `MAC_OSX_10_7_LION_INSTALLER`
* `MAC_OSX_10_8_MOUNTAIN_LION_INSTALLER`
* `MAC_OSX_10_9_MAVERICKS_INSTALLER`
* `MAC_OSX_10_10_YOSEMITE_INSTALLER`

You can also override these setting, such as with
`MAC_OSX_10_9_MAVERICKS_INSTALLER := file:///Volumes/macosx/OS\ X\ Mavericks/Install\ OS\ X\ Mavericks.app`

## Contributing

1. Fork and clone the repo.
2. Create a new branch, please don't work in your `master` branch directly.
3. Add new [Serverspec](http://serverspec.org/) or [Bats](https://blog.engineyard.com/2014/bats-test-command-line-tools) tests in the `test/` subtree for the change you want to make.  Run `make test` on a relevant template to see the tests fail (like `make test-vmware/osx109`).
4. Fix stuff.  Use `make ssh` to interactively test your box (like `make ssh-vmware/osx109`).
5. Run `make test` on a relevant template (like `make test-vmware/osx109`) to see if the tests pass.  Repeat steps 3-5 until done.
6. Update `README.md` and `AUTHORS` to reflect any changes.
7. If you have a large change in mind, it is still preferred that you split them into small commits.  Good commit messages are important.  The git documentatproject has some nice guidelines on [writing descriptive commit messages](http://git-scm.com/book/ch5-2.html#Commit-Guidelines).
8. Push to your fork and submit a pull request.
9. Once submitted, a full `make test` run will be performed against your change in the build farm.  You will be notified if the test suite fails.

## Acknowledgments

[CloudBees](http://www.cloudbees.com) is providing a hosted [Jenkins master](http://box-cutter.ci.cloudbees.com/) through their CloudBees FOSS program. Their [On-Premise Executor](https://developer.cloudbees.com/bin/view/DEV/On-Premise+Executors) feature is used to connect physical machines as build slaves running VirtualBox, VMware Fusion, VMware Workstation, VMware ESXi/vSphere and Hyper-V.

![Powered By CloudBees](http://www.cloudbees.com/sites/default/files/Button-Powered-by-CB.png "Powered By CloudBees")![Built On DEV@Cloud](http://www.cloudbees.com/sites/default/files/Button-Built-on-CB-1.png "Built On DEV@Cloud")

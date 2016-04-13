# Cacao Bad Dog Pew

Think of a boy pretending his finger is a laser yelling at his dog named Cacao:
"Cacao, bad dog! PEW!".

Cacao Bad Dog Pew is an anagram of the initials of **D**rupal **E**ight **G**it
**A**nd **C**omposer **B**ased **W**orkflow **P**roof **O**f **C**oncept **O**h
**A**nd **A**lso **D**ocker. The purpose of the project is to illustrate a
workflow for managing Drupal 8 with git, composer, and docker.

The imagined use case for this is a family of similar websites.

## Requirements

[Ansible](http://docs.ansible.com/ansible/intro_installation.html) and
[Vagrant](https://www.vagrantup.com/downloads.html).

## Overview

The general idea here is to leverage the power of:

1. Drupal 8 to independently address code, configuration and content.
2. Git to manage code and configuration separately.
3. Composer to limit unnecessary files from version control.
4. Docker to bring the pieces together into a website.

### Drupal 8

Drupal 8 allows configuration to be managed with YAML files. While we won't be
working with strictly file based configuration management
([see the wiki][wiki-file-based-config] to read more about why), we will be
maintaining our configuration as seperate independent projects in syncable YAML
files.

Seperating the configuration from the code allows us to use a **single code
base** for all drupal sites. All differences between sites are a matter of
configuration.

Creating a website is a matter is bringing together generic code, specific
configuration and optional data.

[wiki-file-based-config]: https://github.com/hcpss-banderson/cacao-bad-dog-pew/wiki/File-Based-Configuration

### Git

I have not really figured this part out yet. What I do know is that each site's
configuration should be in a seperate repository. I also believe that Github can
aid us in deploying configuration.

But I am unsure of how best to handle the Drupal source code itself. Should it
be in this repository with a VM? Or should it be seperated because development
happening on the dev environment VM is not pertinent to the code itself? I think
I just talked myself into keeping it out of this repo. I don't want to see a
commit like `Configure the token module` next to one like `Increase the memory
of the VM to avoid crashing`.

### Composer

With Drupal 8 we now have the option to manage dependencies with either composer
or drush make. I chose composer kind of arbitrarily. Both allow us to remove
unneccessary code from version control.

### Docker

Where Drupal 8, Composer and Git allow us to segment our websites more, Docker
is what bring the pieces back together again:

```
generic code + bses config                 = empty bses website
             + rbes config                 = empty rbes website
             + rhhs config + rhhs database = full rhhs website
             + cres config + cres database = full cres website
```

## Structure

```
ansible/
src/
  config/
    bses/
    rbes/
    ...
  databases/
    bses.sql.gz
    rbes.sql.gz
    ...
  docroot/
    Dockerfile
    drupal/
      composer.json
      ...
```

### ansible

The *ansible* directory is obviously for provisioning. It contains a playbook
called *provision.yml*. Inspecting it and the contained roles will tell you
exactly how the VM works.

### src

Contains everything not related to the VM.

#### config

This directory is empty in the repo and ignored by git. It is where you will
put website configurations. Choose an identifier for the site and place the
configuration in a directory named after it.

#### databases

Optionally, if you place database dumps here named
`<identifier-you-chose-above>.sql.gz` they will be imported when provisioning
the vm. If there is no database dump for a corrosponding configuration, then the
site will be installed fresh using only the configuration.

#### docroot

Optional. This can either be blank or it can contain the drupal source code. If
it is blank the source code from the producion docker image will be used. If it
contains drupal source code it will be mounted to the production docker image so
that it will be used instead.

Dependencies will be installed durring provisioning so it is not necessary to do
that first.

## Usage

Copy parameters.dist to parameters.yml and edit it to your taste.

Run:

```
$ vagrant up
```

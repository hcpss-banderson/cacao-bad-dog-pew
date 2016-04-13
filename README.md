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

## Usage

### Provisioning

1. Copy parameters.dist to parameters.yml and edit it to your taste.
2. Place drupal configuration yaml files in `src/config/<site-identifier>/`.
3. Optionally, put database dumps in `src/databases/<site-identifier>.sql.gz`.
4. Optionaly place drupal project source code in `src/docroot/` so that it's
composer file is at `src/docroot/drupal/composer.json`.
5. Run `$ vagrant up`

### Commiting Changes to Config

Making changes to the configuration is easy, you can just make your changes in
the Drupal UI. When you are ready to commit them you can export the changes with
this command:

```
drush @<site-identifier> cex -y
```

Then you will see the changes in your git repository. You should then commit
and push them fro there.

## Misc

### Drush

Drush is configured to run within a docker container but be completly
transparent. Pseudo aliases work on the fly to target your various sites.

So, imagine that we had a site called `bses`. Running this command would rebuild
the cache:

```
$ drush @bses cache-rebuild
```

For more information see:

[ansible/roles/drupal-scripts/scripts/drush][drush-source]

[drush-source]: ansible/roles/drupal-scripts/scripts/drush

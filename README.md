About this repo
===============

This is a fork of the original odoo docker repository. This project bring a lot of new
improvements such as a more uniform configuration for all odoo projects.

It tries to support version from 8.0 to the latest versions of odoo. The recent changes
added a script to generate the Docker files with templating to support python2 and python3
odoo.

How to use
==========

pull the images with the following command for odoo 12.0:

    docker pull llacroix/odoo:12.0-ubuntu

And run with:

    docker run -it llacroix/odoo:12.0-ubuntu
    

Features:
=========

Automatic MasterPassword Generation
-----------------------------------

By default, odoo open the web manager to anyone by not setting a master password. This can
be dangerous in a way that a new instance is opening your database to everyone by default.
For that reason, the entrypoint of this image will define a default password if none is
provided.

MasterPassword through secrets
------------------------------

It's possible to set a master password using docker swarm secrets. When the instance boots,
odoo check for a master_password file in the secrets folder and extract its content as the
value of the password. If the password is not encrypted, it will automatically encrypt it
and save it into the ODOO_RC file path. Encryption is only supported by version of odoo higher
than 10 because encryption wasn't supported with version prior. 

Extra Addons supports
---------------------

This image support extra addons in the folder `/addons`. Upon container creation, it will
lookup for requirements.txt file in folders located into `/addons/*` and install them using
`pip` for the current python interpreter.

It will also try to install any deb packages defined in apt-packages.txt file it can find in
the same locations to install debian package available to the ubuntu:bionic distribution.

Warning
=======

This project is still in heavy development, and updating the image may result in breaking
things so consider yourself warned if something stop suddenly working when updating the image.

Until the development gets more stable, consider trying it out in test before pulling new images.


How to build new images:
========================

Run the script `build.py` to generate the Dockerfile based on the versions.toml file.

TODO:
=====

- [x] Uniform setup for odoo 8 to 13, make odoo work transparently regardless of the openerp 
      name for versions with openerp instead of odoo.
- [x] Run entrypoint as odoo user with a sudo entrypoint that remove itself from sudoers when 
      done with pre configuration process.
- [x] Add some way to expose deployment area/zone which can be used later for monitoring the odoo
      servers based on their deployment status.. canari/staging/production/tests etc
- [x] Define Postgres Environment variable to make it easier to connect to postgres from within the
      container.
- [x] Use a secret to setup a `.pgpass` file for postgres
- [x] Fix dependencies for ubuntu bionic
- [ ] Automatically build addons path parameters based on folders in /addons/* and environment 
      variables.
- [ ] Automatically detect modules that should get update on boot. For example, you want to start
      odoo and always update a certain set of modules or a certain set of addons repository.
- [ ] Automatically detect server wide modules

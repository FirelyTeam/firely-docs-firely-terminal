.. _managing_packages:

Managing packages
=================

This page walks through the package commands of Firely Terminal, roughly in the order you need them.
See the :ref:`command_reference` for the full list of parameters of each command.

Making your folder a FHIR project
---------------------------------

A folder becomes a FHIR project as soon as it has a package manifest (``package.json``). Set the FHIR
version first, then initialize:

.. code-block:: Bash

   > fhir spec R4
   > fhir init

``init`` creates the manifest, names the package after the folder, and adds a dependency on the FHIR
core package of the selected version. You can override name and version:

.. code-block:: Bash

   > fhir init xyz.myprofiles 0.2.0

Installing packages
-------------------

The ``install`` command adds a package as a dependency of your project. It downloads the package into
your package cache, adds it to ``package.json``, and then runs a restore so that all deep dependencies
are resolved as well.

.. code-block:: Bash

   > fhir install xyz.myprofiles          #latest version
   > fhir install xyz.myprofiles 2.3      #a specific version
   > fhir install xyz.myprofiles 2.x      #the latest within a range

Installing from a file
~~~~~~~~~~~~~~~~~~~~~~

If you have a package file (``.tgz``) that is not on a package server — for example one you just built
yourself and want to test — install it from disk with the ``--file`` (``-f``) flag:

.. code-block:: Bash

   > fhir install thisproject.tgz --file

Removing a package
~~~~~~~~~~~~~~~~~~

.. code-block:: Bash

   > fhir remove xyz.myprofiles

This removes the reference from ``package.json`` and updates the lock file ``fhirpkg.lock.json``. The
package itself stays in your package cache, so a reinstall does not need a download. ``fhir uninstall``
does the same thing.

Restoring
~~~~~~~~~

.. code-block:: Bash

   > fhir restore

``restore`` rebuilds the full dependency closure of your project: it downloads every package referenced
in ``package.json``, plus everything those packages depend on, and writes the result to
``fhirpkg.lock.json``. It runs automatically after ``install`` and ``remove``.

Run it manually after cloning an existing project from Git, after editing ``package.json`` by hand, or
after :ref:`changing the package feed <private_package_feeds>`.

.. note::
   In case of version collisions the *closest-and-latest* strategy is used: a dependency closer to the
   root of the tree wins over a deep dependency, and a higher version wins over a lower one.

   There is no need to commit ``fhirpkg.lock.json`` to your repository; ``restore`` regenerates it.

Inspecting your project
-----------------------

.. code-block:: Bash

   > fhir deps           #your direct dependencies (short for 'fhir dependencies')
   > fhir scope          #every package in scope, direct and deep
   > fhir closure        #the dependency tree, showing where each package comes from

``scope`` reads the lock file, so it tells you what a validation or resolve will actually see. If it
reports that no closure was found, run ``fhir restore`` first.

To look inside a package rather than at the tree:

.. code-block:: Bash

   > fhir contents xyz.myprofiles.tgz    #files and dependencies of a package file
   > fhir canonicals xyz.myprofiles      #canonical urls in an installed package
   > fhir canonicals                     #canonical urls in the whole current scope

``canonicals`` is the quickest way to find out whether a profile you expect is really in scope, and
which package it comes from.

Finding packages and versions
-----------------------------

Search the package server by name or by canonical:

.. code-block:: Bash

   > fhir find nictiz                                   #search by (partial) name
   > fhir find http://fhir.nl/fhir                      #search by canonical
   > fhir find --name nictiz --spec R4                  #filter on FHIR version

List all published versions of a package. A star marks the versions you already have in your cache:

.. code-block:: Bash

   > fhir versions simplifier.core.stu3

Add ``--raw`` to see the raw package listing document as the server returns it.

Version ranges resolve according to SemVer. Use ``semver`` to check what a range in your manifest
actually points to — if ``xyz.myprofiles`` has versions 3.1.0, 3.2.0 and 4.0.0, then ``3.x`` resolves to
3.2.0:

.. code-block:: Bash

   > fhir semver xyz.myprofiles 3.x
   > fhir semver xyz.myprofiles 3.x --list    #all versions, with an arrow at the match

The package cache
-----------------

Downloaded packages are stored in a package cache, so that they are downloaded only once and can be
shared between all your FHIR projects. By default this is the global cache of your machine, in your
user profile.

.. code-block:: Bash

   > fhir cache list                #all packages in the cache used by this project
   > fhir cache list --global       #force listing of the global cache
   > fhir cache location            #tells you whether you use the global or a local cache
   > fhir cache location --path     #the folder itself

You can download a package into the cache without adding it as a dependency of your project:

.. code-block:: Bash

   > fhir cache install hl7.fhir.r4.core 4.0.1

Using a local cache
~~~~~~~~~~~~~~~~~~~

Sometimes you want a project to have its own cache instead of the shared global one — for example on a
build agent, or to keep private packages separate from your other work:

.. code-block:: Bash

   > fhir cache use-local           #creates a cache folder inside this project
   > fhir cache use-global          #removes it and switches back to the global cache

``use-global`` refuses to delete a local cache that still has content unless you add ``--confirm``.

.. note::
   The ``--here`` flag of ``fhir install`` has been removed. Use ``fhir cache use-local`` instead.

If you just want the *contents* of a package on disk to look at, rather than a cache, use:

.. code-block:: Bash

   > fhir extract-package xyz.myprofiles                    #unpacks under the current folder
   > fhir extract-package xyz.myprofiles --dependencies     #including its dependencies

Snapshots and expansions
------------------------

All Firely tools generate snapshots and expansions on the fly. Some other tooling cannot, and can only
work with packages that already contain them. If you do need them, generate them at packaging time with a ``package.bake.yaml`` script. Besides snapshots and
expansions, bake can select a subset of resources, generate an ``.index.json`` and transform FHIR
Shorthand files. The syntax is described in :ref:`the bake documentation <simplifier_docs:bake>`.

For packages that already exist, ``fhir inflate`` adds snapshots and expansions after the
fact - to a package in your cache, to your project, or to its dependencies. See
:ref:`snapshotting_a_project`.

Creating and publishing a package
---------------------------------

``pack`` builds a package file (a tarball) from your project:

.. code-block:: Bash

   > fhir pack
   > fhir pack --name mypackage.tgz

If a ``package.bake.yaml`` is present, bake runs as part of ``pack`` and its output goes straight into
the package. Use ``fhir bake`` to preview that same output in a ``.bake`` folder without building a
package. Bake is a licensed feature.

Publishing a package to the package server requires a license and a Simplifier login:

.. code-block:: Bash

   > fhir login
   > fhir publish-package mypackage.tgz --first

The ``--first`` flag is required for the very first version of a package, to prevent accidental
publishing of a mistyped package name. If your project has a feed configured, the package is published
to that feed — see :ref:`private_package_feeds`.

.. warning::
   Once a package version is published it cannot be removed from the package server. Run your
   :ref:`quality control <quality_control>` before you publish.

Changing the package server
---------------------------

By default Firely Terminal resolves packages from ``https://packages.simplifier.net``, because it is the
most complete FHIR package server. To see or change that:

.. code-block:: Bash

   > fhir source                                    #shows the current package server
   > fhir source https://mypackageserver.org        #sets a different one
   > fhir source https://myserver.org --npm         #for a plain NPM server

This is a machine-wide setting. To consume *private* packages from Simplifier you do not change the
source: use a feed instead, which is a per-project setting. See :ref:`private_package_feeds`.

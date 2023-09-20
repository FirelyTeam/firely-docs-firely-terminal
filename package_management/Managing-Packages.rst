Package Management
===================

Below you will find more information on the available commands for package management within Firely Terminal

Expansions and Snapshots
------------------------
All the Firely tools will create snapshots and expansions on the fly. Some tooling does not have this capability and can only use packages that already contain snapshots and/or expansions. We do not recommend using snapshots in a package since this may result in out dated snapshots in packages where by definition the content does not change. With the introduction of the bake pipeline we allow customization at package creation! You can start this easily on Simplifier by creating a ``package.bake.yaml`` file or create you own yaml file. 

The sytax for creating your bake file is explained `here in our documentation <https://simplifier.net/docs/bake>`_. As you can see in the documentation there are a variety of options available. Besides the snapsthots and expansions, there are options for selecting a subset of resources, creating an .index.json file and transforming FHIR Shorthand files are available in the bake pipeline. 

It is also possible to create snapshots and expand valuesets for already existing packages by using the ``fhir inflate`` command. Inflate creates snapshots and/or expansions for the current project (or a specific package).

**WARNING** ``fhir inflate`` changes the content of your current profiles and ValueSets permanently. If you want to do the opposite you can use ``fhir deflate`` to remove snapshots and expansions from you current project (or a specific package). 

Versions
--------
Lists all available versions of the package on the server

.. code-block:: Bash

   > fhir versions simplifier.core.stu3

This shows you all the available versions of the package on the package
server. What it does is that it fetches the *package listing* of that
package from the server, which is a json document. To see that document,
use the ``--raw`` flag. Example:

.. code-block:: Bash

   > fhir versions simplifier.core.stu3 --raw


Cache
-----

The cache commands lists all the packages in the global package cache on
your machine.

.. code-block:: Bash

   > fhir cache 

If you want to know the folder location of the FHIR package cache, add
the ``--location`` (or ``-l``)parameter:

.. code-block:: Bash

   > fhir cache --location

Semver
------
The semver command allows you to test if a version range is what you
intended. It will show you the version of a package the range actually
resolves to. If the package ``xyz.myprofiles`` has three versions:
3.1.0, 3.2.0 and 4.0.0, the range ``3.x`` points to ``3.2.0`` Usage:

.. code-block:: Bash

   > fhir semver xyz.myprofiles 3.x


Install
--------
The install command can be used to install any FHIR package from the
package server. It will install the package on your local machine and
add a reference from your current folder to that package, by adding it
to the ``package.json`` file (also known as a package manifest). You can
provide just the package name, to get the latest.

.. code-block:: Bash

   > fhir install xyz.myprofiles 

Or you can specify a specific version or range.

.. code-block:: Bash

   > fhir install xyz.myprofiles 2.3

Folder install
~~~~~~~~~~~~~~

By default a package is installed in the global package cache of your
machine. But you can specify to install it in the current folder with
the ``--here`` (``-r``) flag.

.. code-block:: Bash

   > fhir install simplifier.core.stu3 --here

This will install the latest version of package ``simplifier.core.stu3``
as a subfolder of your current folder. In most scenario’s you should not
need this option.

Install a file
~~~~~~~~~~~~~~

If you have a package file that is not on the package server, for
example you created it locally and want to test it, you cana install a
package file (.tgz) from your current folder into the global package
cache of your machine. For this you can use the ``--file`` (``-f``)
flag.

.. code-block:: Bash

   > fhir install thisproject.tgz --file


Remove
-------
If you have installed a package in your current project (folder), you
can remove it. For a package called ‘xyz.myprofiles’ the command would
be:

.. code-block:: Bash

   > fhir remove xyz.myprofiles

This will not remove your package from your global packages cache, but
it is no longer part of your current project. The command will update
the manifest (package.json) and your lock file ``fhirpkg.lock.json`` ).

Scope 
-----
Lists all the packages that are in scope for this folder context.

.. code-block:: Bash

   > fhir scope

If you only want to know your direct dependencies, use:

.. code-block:: Bash

   > fhir dependencies

or the short form:

.. code-block:: Bash

   > fhir deps 


Other Commands
--------------

.. code-block:: Bash

  > fhir contents       #Displays the content from a packages
  > fhir pack           #Creates a FHIR package from a folder
  > fhir init           #Generates a FHIR package manifest 
  > fhir restore        #Restores all packages referenced in this folders package file 
  > fhir canonicals     #Lists canonicals from resources in a package or scope 
  > fhir find           #Searches for package(s) by name or canonical
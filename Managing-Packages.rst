Packages
========

Firely Terminal is a FHIR package manager. The following commands are available to manage them.

Packages are the core distribution mechanism for sets of profiles. Profiles and other conformance 
resources rarely live in isolation. Together they form a use case, and they usually need other resources 
from a national initiative, and they certainly need the FHIR core base profiles.

In FHIR these sets are distributed according to the `FHIR package standard <https://confluence.hl7.org/display/FHIR/NPM+Package+Specification>`_, 
which conforms largely with the `NPM package standard <https://www.npmjs.com/>`_. There are a few additions and a 
few restrictions.

One of the core problems when distributing profiles, is choosing the right version. And since profiles often 
reference eachother, you need all the ones that are of the same release. For that, packages are ideal.

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
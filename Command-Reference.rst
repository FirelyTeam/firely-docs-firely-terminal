.. _command_reference:

====================================
Firely Terminal -- Command Reference
====================================

This is a complete reference of all available commands in Firely Terminal **v3.5.0**.
You can also view this information from the command line by running ``fhir -h`` or ``fhir --help``.
For help on a specific command, use ``fhir ? <command>``.

.. contents::
   :local:
   :depth: 2

Archive
=======

``fhir unzip``
~~~~~~~~~~~~~~

**Description:** Extracts zip to the stack or disk

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<path>``
     - string
     - Yes
     - The path and filename of the zip file
   * - ``--disk``
     - flag
     - No
     - Set this flag if you want to extract to disk instead of to the stack.

FHIR resources are often exchanged using a ZIP archive. In order to avoid the need
to install multiple tools, we included a zip and unzip command in this tool.

You can unzip the resources from the given zip file to the internal stack,
or as a set of separate resource files on disk (current folder).

----

``fhir zip``
~~~~~~~~~~~~

**Description:** Saves all stack entries to a zip file

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<files>``
     - string
     - No
     - The files (like \*.xml) instead of the stack to add to the zip
   * - ``--output``
     - flag with value
     - No
     - The format to store the files in.
   * - ``--shape``
     - flag with value
     - No
     - The shape of files to include (like \*.xml)
   * - ``--zipname``
     - flag with value
     - No
     - The file name of the zip archive to create

FHIR resources are often exchanged using a ZIP archive. In order to avoid the need
to install multiple tools, we included a zip and unzip command in this tool.

You can zip the resources in the internal stack, or a set of resources on disk matching a filter.

Comparing
=========

``fhir diff``
~~~~~~~~~~~~~

**Description:** Compares the files of 2 packages (experimental)

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<pkg1>``
     - string
     - Yes
     - 
   * - ``<pkg2>``
     - string
     - Yes
     - 

----

``fhir diff``
~~~~~~~~~~~~~

**Description:** Compares a specific file change between 2 packages (experimental)

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<pkg1>``
     - string
     - Yes
     - 
   * - ``<pkg2>``
     - string
     - Yes
     - 
   * - ``<canonical>``
     - string
     - Yes
     - 
   * - ``--unchanged``
     - flag
     - No
     - 

----

``fhir path-list``
~~~~~~~~~~~~~~~~~~

**Description:** Shows the snapshot element paths of a structure definition

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<canonical>``
     - string
     - No
     - If provided: the canonical of the StructureDefinition to display

Takes all the paths from elements in de snapshot of a resource and displays them for quick reference.
By default: take the resource on the top of the stack.

Configuration
=============

``fhir logging``
~~~~~~~~~~~~~~~~

**Aliases:** ``fhir output``

**Description:** Shows or sets the log level of this app

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<level>``
     - string
     - No
     - The log level to set
   * - ``--levels``
     - flag
     - No
     - List the available log levels
   * - ``--monochrome``
     - flag
     - No
     - All output is without colors
   * - ``--colors``
     - flag
     - No
     - Use colors when writing to console

With the log command you define how much output is generated.
Silent: generates no output except for commands that have output as their goal.
Brief: only generates output when absolutely necessary.
Normal: generates regular output
Detailed: generates additional information, like resolved canonical urls
Debug: generates output that may help figuring out what went wrong in the system.

Without parameters, the current log level is displayed

----

``fhir spec``
~~~~~~~~~~~~~

**Aliases:** ``fhir specification``

**Description:** Select the FHIR specification version to work with

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<value>``
     - string
     - No
     - The FHIR specification version
   * - ``--project``
     - flag
     - No
     - Inside a project, change the FHIR version of a project
   * - ``--list``
     - flag
     - No
     - List implemented specifications to choose from

With this command you can read or set the FHIR standard specification version that is
used for all FHIR related commands.

----

``fhir config inflate``
~~~~~~~~~~~~~~~~~~~~~~~

**Description:** Configure auto inflate settings

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<value>``
     - string
     - No
     - Possible values: All, Snapshots, ValueSets, Off

You can configure whether and what to inflate when a package is installed. When StructureDefinitions get
snapshots and ValueSets are expanded, you get more reliable results of what the author intended to convey,
but inflation might fail when models are incomplete. And inflation might also interfere with other tools
using the package contents, since they are altered.

----

``fhir config regenerate``
~~~~~~~~~~~~~~~~~~~~~~~~~~

**Description:** Inflate regeneration settings

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<value>``
     - string
     - No
     - on/off

Determines whether existing snapshots will be regenerated or that only
snapshots will be generated when none is present in the StructureDefinition.

----

``fhir config validate``
~~~~~~~~~~~~~~~~~~~~~~~~

**Description:** Validator engine flavor

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<flavor>``
     - string
     - No
     - Possible values: firely, netsdk

Firely Terminal has two validators: the "Firely" validator and the "netsdk" (legacy) validator.
The legacy validator is an open source validator from the FHIR .NET SDK. The Firely validator
is the validator used in Firely Products. This one is actively maintained and has more advanced validation.

FHIR Client
===========

``fhir get``
~~~~~~~~~~~~

**Description:** Does a FHIR http get on a URL

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<url>``
     - string
     - Yes
     - The FHIR endpoint that the GET will be sent to.

The get command does a http GET directly to a FHIR endpoint url, and pushes the results on the stack.
The exceptions are: a bundle will first be split into its individual entries which are pushed on the stack.
Operation outcomes are printed instead of pushed to the stack.

----

``fhir post``
~~~~~~~~~~~~~

**Aliases:** ``fhir create``

**Description:** Posts a resource to the given server

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<source>``
     - string
     - Yes
     - A known alias or Simplifier project.

The Post command pops the resource from the top of the stack and does an Http POST to a FHIR server
given by the source parameter. Any resulting operation outcome will be printed to the output.

----

``fhir put``
~~~~~~~~~~~~

**Aliases:** ``fhir update``

**Description:** Updates a resource to the given server

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<source>``
     - string
     - Yes
     - A known alias or Simplifier project
   * - ``<file>``
     - string
     - No
     - Instead of the top of the stack, you can provide a file name that will be posted.

The Put command pops the resource from the top of the stack and does a http PUT to a FHIR server
given by the source parameter. The resulting operation will be printed to the output.

----

``fhir read``
~~~~~~~~~~~~~

**Description:** Reads a resource from the given server

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<source>``
     - string
     - No
     - A known server alias or Simplifier project key
   * - ``<record>``
     - string
     - Yes
     - Provide [resource-name]/[id] pattern

The read command Reads a resource from a FHIR server. The resulting resource will be pushed
on to the stack.

----

``fhir search``
~~~~~~~~~~~~~~~

**Description:** Searches for resources on the given server. 

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<source>``
     - string
     - Yes
     - A known server alias or Simplifier project
   * - ``<type>``
     - string
     - Yes
     - The resource type. Remember that resources start with a capital letter.
   * - ``--pages``
     - flag with value
     - No
     - The amount of pages to retrieve: choose a number or 'all'. Default is 1.
   * - ``--split``
     - flag
     - No
     - Split the bundles into separate resources before pushing them onto the stack
   * - ``<arguments>``
     - arguments
     - No
     - Zero or more FHIR search parameters of the format a=b.

The search command does a FHIR search according to the FHIR REST specification. The bundle returned from the server
is pushed on the stack. With the --split command the entries from the bundle are extracted and pushed on the stack.
All search parameters in the form of a=b will be passed to the server.
For example \_id=f201 or url=http://hl7.org/fhir/StructureDefinition/Patient.

Feeds
=====

``fhir feed``
~~~~~~~~~~~~~

**Description:** Sets, list or clears the package feed

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<feed>``
     - string
     - Yes
     - Sets a new feed using the URLkey of that feed
   * - ``--clear``
     - flag
     - No
     - Clears the feed

Feeds are a way to organize private packages in Simplifier.
This command gives you access to a feed.
Use it to read, set or clear the feed for the current project.

Make sure that all packages that you need, including the public packages
are available in your feed.

In this first version, we limit it to 1 feed only. We might add multiple later.

Inflation
=========

``fhir deflate``
~~~~~~~~~~~~~~~~

**Description:** Remove snapshots/expansions for project or package

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``--package``
     - flag with value
     - No
     - Deflate the given package instead of the current project

Removes snapshots and expansions from the current project.

WARNING this changes the content of your current profiles and ValueSets permanently.

----

``fhir expand``
~~~~~~~~~~~~~~~

**Description:** Expands a ValueSet

The Expand command takes a ValueSet from the top of the stack, and expands that ValueSet.
The result is placed back on the stack. The expand will go to 1 000 000 codes.
Beware that large expansions can be slow. For the expansion to work, all references have
to be found in the current project scope.

----

``fhir inflate``
~~~~~~~~~~~~~~~~

**Description:** Creates snapshots/expands ValueSets for a project or package

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``--here``
     - flag
     - No
     - Inflate the current folder. (Licensed)
   * - ``--package``
     - flag with value
     - No
     - Inflate the given package instead of the current project
   * - ``--snapshot``
     - flag
     - No
     - Whether to create snapshots (true by default)
   * - ``--expand``
     - flag
     - No
     - Whether to expand valuesets (true by default)
   * - ``--force``
     - flag
     - No
     - Re-inflate if inflate has already been done.
   * - ``--dependencies``
     - flag
     - No
     - First expand dependencies

Inflate creates snapshots and/or expansions for the current project (or a specific package)

WARNING this changes the content of your current profiles and ValueSets permanently.

----

``fhir inflate-cache``
~~~~~~~~~~~~~~~~~~~~~~

**Description:** Creates snapshots/expansions in all packages in the current cache

Inflates all packages currently in the cache. This is useful if the cache has been filled
by tools that are not capable of creating snapshots.

----

``fhir snapshot``
~~~~~~~~~~~~~~~~~

**Description:** Creates a snapshot from a StructureDefinition

The snapshot command takes the resource from the top of the stack. This must be a StructureDefinition
It adds or replaces the snapshot of that resource and places the resource back on the stack.

Packages
========

``fhir canonicals``
~~~~~~~~~~~~~~~~~~~

**Description:** Lists canonicals from resources in a package or scope

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<pkgname>``
     - string
     - No
     - The name of the package to list.
   * - ``<version>``
     - string
     - No
     - The version of the package. If not provided, it defaults to latest.

The canonicals command lists the canonical uris from all conformance resources in package provided in the parameter.
If no package is provided, this command will list all canonicals in the full scope of the current project.

----

``fhir closure``
~~~~~~~~~~~~~~~~

**Description:** Analyse packages dependencies (experimental)

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<package>``
     - string
     - No
     - The package for which to show the closure (optional)
   * - ``<version>``
     - string
     - No
     - The version of that package

Experimental feature: Print the closure analysis of the current package dependencies.
This does recalculate the current closure of dependencies using only
packages currently in the cache.

If package name is provided it will show the closure of that.

----

``fhir contents``
~~~~~~~~~~~~~~~~~

**Description:** Displays the contents of a package

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<filename>``
     - string
     - Yes
     - The filename of the package to list

To find what the contents of a package are, use this command. It displays all files and the dependencies
set in the package.json manifest file.

----

``fhir dependencies``
~~~~~~~~~~~~~~~~~~~~~

**Aliases:** ``fhir deps``

**Description:** Show the package dependencies of current scope

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``--direct``
     - flag
     - No
     - 

Where the scope command shows the full scope of the current project, the dependencies command only shows
the direct dependencies - the installed packages.
<param name="direct">Only show direct dependencies</param>

----

``fhir extract-package``
~~~~~~~~~~~~~~~~~~~~~~~~

**Description:** Extracts a package under the current folder

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<package>``
     - string
     - Yes
     - The package to extract
   * - ``<version>``
     - string
     - No
     - The version of the package to extract
   * - ``--dependencies``
     - flag
     - No
     - Also install and extract all dependencies

This command installs a package and optionally it's dependencies in subfolders of the current folder.
This is not for regular package dependency consumption, but for when you want to collect the
contents of these packages.

----

``fhir find``
~~~~~~~~~~~~~

**Aliases:** ``fhir find-package``

**Description:** Searches for packages(s) by name or canonical

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<value>``
     - string
     - No
     - A partial package name or canonical.
   * - ``--spec``
     - flag with value
     - No
     - The Fhir specification to filter on
   * - ``--name``
     - flag with value
     - No
     - The package name to search for
   * - ``--canonical``
     - flag with value
     - No
     - The canonical to find

The find command searches for package names and canonicals in the package server. If will list
all packages that have a match. It will indicate with a star if a package is already installed in
the global package cache of your machine.

----

``fhir init``
~~~~~~~~~~~~~

**Description:** Generates a FHIR package manifest

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<pkgname>``
     - string
     - No
     - The package id of this project
   * - ``<version>``
     - string
     - No
     - The version of the package. The default is 0.1

The init command transforms the current directory in to a FHIR project, by adding a package.json file.
A dependency to the FHIR core package will be added. Make sure you have set the FHIR version correctly, using
the 'spec' command before you run init.

----

``fhir install``
~~~~~~~~~~~~~~~~

**Description:** Downloads and installs a package

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<package>``
     - string
     - Yes
     - The package name
   * - ``<version>``
     - string
     - No
     - The version (range) of the package, this can be a floating reference, like 1.x
   * - ``--here``
     - flag
     - No
     - To install the package in a subfolder of the current project instead of the global cache.
   * - ``--file``
     - flag
     - No
     - Install the package from a file instead of the server. In this case provide the file name instead of the package name.
   * - ``--authenticate``
     - flag
     - No
     - Set this flag to access private packages
   * - ``--private``
     - flag
     - No
     - Deprecated. Use --authenticate

The install command adds a FHIR package to your current project (folder). It does this in several steps:
1. It installs the package from a FHIR package server into your package cache, if it's not there yet.
2. It creates a new package.json file if it's not there yet.
3. It adds a reference to that package in the package.json file of your project.
4. It does a restore on your project. This makes sure that all dependencies and deep dependencies are
also installed and part of your project scope.

----

``fhir publish-package``
~~~~~~~~~~~~~~~~~~~~~~~~

**Description:** Publish a package to the package server (licensed)

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<filename>``
     - string
     - Yes
     - The package file to publish
   * - ``--team``
     - flag with value
     - No
     - specify the team name under which the package will be published
   * - ``--first``
     - flag
     - No
     - Specify that this is the first version of a package, to prevent accidental publishing.

The publish-package command publishes a package file (.tgz) to the package server.
You need to have a license for this.

Make sure you have done proper quality control and checked the content is right,
before you publish a package. Once a package is published it can not be removed from the
package server.

----

``fhir restore``
~~~~~~~~~~~~~~~~

**Description:** Restores all packages referenced in this folders package file

The restore command rebuilds the full list of all dependencies that this project (the current folder)
relies on. That means all package dependencies provided in the package.json file, added with the install command,
and all dependencies that those packages in turn depend on. A restore is automatically run after an install
or remove command, but can be very useful when downloading an existing project

The restore command will install all missing packages, including deep dependencies, in your machine.

The restore command creates a file called fhirpkg.lock.json with the full list of dependencies.
When using with git, there is no need to include this file in your repo.

In case of collisions, the closest-and-latest strategy is followed
A dependency closer to the root wins over a deep dependency, a higher version of a package wins over a lower version

----

``fhir semver``
~~~~~~~~~~~~~~~

**Description:** Tests if the specific version can be resolved

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<pkgname>``
     - string
     - Yes
     - The package name (you may include @version)
   * - ``<pkgversion>``
     - string
     - No
     - The package version range
   * - ``--list``
     - flag
     - No
     - Lists all version with an arrow to the resolved version

With the semver command you can find out if a package dependency or reference actually
points to a package. This is particularly useful if the reference points to a range, like
3.1.x or 3.x or 3.1, where the reference points to the latest within that range.

If you do not provide a version, the latest version will be resolved
See documentation on SEMVER and NPM for mor details on versions and package ranges.

----

``fhir source``
~~~~~~~~~~~~~~~

**Description:** Shows or sets the package server url

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<url>``
     - string
     - No
     - A package server url. If provided, sets the default source to this url
   * - ``--npm``
     - flag
     - No
     - If the server is not a FHIR package server, but an NPM server
   * - ``--insecure``
     - flag
     - No
     - Accepts servers without a valid security certificate. For testing purposes.

By default this tool will use the packages.simplifier.net as the source of packages
Because it's the most complete FHIR package server.
If you want to use another source or a local package server instead you can change this
default.

----

``fhir uninstall``
~~~~~~~~~~~~~~~~~~

**Aliases:** ``fhir remove``

**Description:** Removes a package from the local context (package.json)

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<pkgname>``
     - string
     - Yes
     - The name of the package to remove

The remove command removes a package from the current context - it removes any reference from the package.json folder
in the current project. The package will not be removed from the global package cache.
After the removal, a restore is done, to make sure all deeper dependencies are also removed.

----

``fhir versions``
~~~~~~~~~~~~~~~~~

**Description:** Lists all available versions of the package on the server

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<pkgname>``
     - string
     - Yes
     - The name of the FHIR package
   * - ``--raw``
     - flag
     - No
     - Use this flag to get the raw JSon output from the server

With this statement you can retrieve a list of all published versions
of a FHIR package. A star before the version indicates that the file is already installed
in the local package cache.

Publishing
==========

``fhir bake``
~~~~~~~~~~~~~

**Description:** Publishes to a given folder

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<script>``
     - string
     - No
     - Specify the script to run. Default is 'package.bake.yaml'
   * - ``--input``
     - flag with value
     - No
     - Folder to run bake from. Default is current folder
   * - ``--output``
     - flag with value
     - No
     - Folder to save the output to. Default is '.bake'

The bake command runs a .bake.yaml script to generate published content. For the syntax of the
bake scripts go to https://simplifier.net/docs/bake

By default Bake saves the output of your bake to a folder called '.bake'.
Bake runs automatically when you create a package using the 'pack' command.
But then the output will then go directly into your package.

Note that bake is a licensed feature.

----

``fhir pack``
~~~~~~~~~~~~~

**Description:** Creates a FHIR package from a folder

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<folder>``
     - string
     - No
     - The folder to pack
   * - ``--name``
     - flag with value
     - No
     - The file name of the saved package

The pack command creates a package from the contents of the current project.
The resulting package file is a tarball (the FHIR package file format).
The package file will be saved in the current folder.

If you have a license, you can write a 'package.bake.yaml' script file to
customize the way your packages are built.

For details, see: https://simplifier.net/docs/bake.

Using the 'bake' command you can produce the same contents as 'pack', but
it will output to a '.bake' folder instead, so that you can preview the results.

----

``fhir template``
~~~~~~~~~~~~~~~~~

**Description:** Generates a script from a template

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<name>``
     - string
     - No
     - The name of the file
   * - ``--bake``
     - flag
     - No
     - Generates a bake script
   * - ``--check``
     - flag
     - No
     - Generate a Quality Control rules file

Generates a default script to give you a start.
After generation, you can modify this script to your own needs.

Either choose the template with one of the flags, or just provide a
file name with the correct extension.

Quality Control
===============

``fhir check``
~~~~~~~~~~~~~~

**Description:** Checks the quality of your project (licensed)

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<series>``
     - string
     - No
     - The name of the rule series to run
   * - ``--list``
     - flag
     - No
     - List the rules, without executing them
   * - ``--details``
     - flag
     - No
     - Show details when listing
   * - ``--push``
     - flag
     - No
     - Adds the resulting issues on the stack as a bundle of OperationOutcomes

Check does a Quality Control check of your project. By default the "minimal" rule set is run.
A more extended set of rules: "recommended" is also included with Firely Terminal by default.

You can also write your own rule sets. You can do that by creating a file that starts
with a series name and ends with '.rules.yaml'. For example: mychecks.rules.yaml

For more information see https://docs.fire.ly/projects/Simplifier/develop/quality-control.html

----

``fhir validate``
~~~~~~~~~~~~~~~~~

**Description:** Validates a FHIR resource

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<file>``
     - string
     - No
     - The file to validate against instead of the resource on the stack
   * - ``--profile``
     - flag with value
     - No
     - The url of a profile to validate against, besides the base and claims
   * - ``--push``
     - flag
     - No
     - Pushing the  operation outcome to the stack

The validate command validates the resource on the top of the stack. The result is a set of issues that
are printed to the output. An issue can be information, warning, error or fatal. The validation will check
if the resource conforms to its base profile, but also to the profile claims of meta.profile.

You can switch validator engine, with 'fhir config validator [engine]'

Resource Generation
===================

``fhir yaml``
~~~~~~~~~~~~~

**Aliases:** ``fhir gen``, ``fhir yamlgen``, ``fhir yaml-gen``

**Description:** Generate resources with YAML. 

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<file>``
     - string
     - Yes
     - The file containing yaml definitions
   * - ``--types``
     - flag
     - No
     - Display the types of values when rendered to the output
   * - ``--output``
     - flag with value
     - No
     - The format to render the results (instead of pushing them onto the stack).

Generates FHIR resources from a yaml definition. By default the resources will be pushed to the stack.

For information about the Yaml-Gen, visit https://simplifier.net/docs/yamlgen

Scope
=====

``fhir read``
~~~~~~~~~~~~~

**Description:** Reads a resource by providing type/id

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<record>``
     - string
     - Yes
     - type/id. Reminder: FHIR resource types start with a capital letter

Gets a resource as you would request it from a Fhir Server. Use the FHIR record format
of resource/id. For example: Patient/f201.

----

``fhir resolve``
~~~~~~~~~~~~~~~~

**Description:** Resolves a canonical url to a resource and pushes it on the stack

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<canonical>``
     - string
     - Yes
     - The canonical of the resource to search for

The resolve command tries to find a resource with the given canonical in the current scope.
If it is found, the resource is fetched and put on top of the resource stack.

----

``fhir scope``
~~~~~~~~~~~~~~

**Description:** Lists packages in scope for this folder context

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<pkgname>``
     - string
     - No
     - A package as root scope instead of the current folder
   * - ``<version>``
     - string
     - No
     - The version of the package

Lists all direct and deep package dependencies of the current scope (the closure).
With this you can check if the restore has been done.

Scripting
=========

``fhir path``
~~~~~~~~~~~~~

Path commands manipulate the contents of a fhir resource at the location provided by a FHIRpath expression.

----

``fhir predicate``
~~~~~~~~~~~~~~~~~~

**Description:** Evaluates a FHIRPath expression (true/false) for the top of the stack

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<expression>``
     - string
     - Yes
     - The fhirpath expression to be evaluated

Evaluates a FHIRPath predicate against the top of the stack. It will return either true or false.

----

``fhir query``
~~~~~~~~~~~~~~

**Description:** Runs an FQL script

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<script>``
     - string
     - No
     - The script to run.
   * - ``--file``
     - flag with value
     - No
     - Instead of a literal script, provide a file
   * - ``--output``
     - flag with value
     - No
     - Chooses the output format
   * - ``--source``
     - flag with value
     - No
     - Choose project, stack, scope, [url] or a Simplifier project as a datasource.

The query command runs an FQL Script over a set of resources and produces a table. By default the
query is run over the current scope. But you can provide a different source, like a FHIR server or a
package.

Example: fhir query "from Patient select name.given[0], name.family"


In order to prevent parsing conflicts with your Windows or Linux console, place the query between double
quotes.

You can choose between four output formats: JSON, XML, Table or HTML. The table is the default for the
command line. The HTML output is useful for embedding in a web page.

As a source, by default your project (current folder) is used. But you can choose a different scope:
With the source option set to scope, you get your project and all dependencies as a source.
With stack you get access to all resources on the stack. You can also provide any known server alias,
or you can provide the URL of a FHIR server. You can also choose any Simplifier project.

For information of the FQL language, visit https://simplifier.net/docs/fql

----

``fhir select``
~~~~~~~~~~~~~~~

**Description:** Prints the values from the fhirpath expression

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<fhirpath>``
     - string
     - Yes
     - The FHIRpath expression to run
   * - ``--file``
     - flag with value
     - No
     - Parse a file instead of the resource on top of the stack.
   * - ``--output``
     - flag with value
     - No
     - The format in which the output is printed
   * - ``--types``
     - flag
     - No
     - In case of a tree output, show the datatype of each node

The select command runs a FHIRpath expression on a resource, and prints the resulting
value(s) to the console output. By default this is done with the resource on top of
the stack, but you can select a file instead with the --file option.

----

``fhir path delete``
~~~~~~~~~~~~~~~~~~~~

**Aliases:** ``fhir path del``

**Description:** Deletes nodes from a FHIR resource (experimental)

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<path>``
     - string
     - Yes
     - A FHIRpath expression
   * - ``--all``
     - flag
     - No
     - If provided the delete will be applied to all resources on the stack.

The path delete command deletes the element nodes the provided FHIRpath points to of the resource at the
top of the stack. This is an experimental feature. We plan to add insertion and updates later.

Simplifier
==========

``fhir doc``
~~~~~~~~~~~~

**Description:** Brings you to the documentation page of the resource with that canonical

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<canonical>``
     - string
     - Yes
     - The canonical URL of the resource to find

Simplifier.net keeps track of the documentation location of all conformance resources known.
The doc command tries to find a resource that matches the given canonical, and spins up a browser
with the page containing the documentation of that resource.

----

``fhir license``
~~~~~~~~~~~~~~~~

**Description:** List the features in your license

Shows which commercial license features are available to you. You must be logged in to

----

``fhir login``
~~~~~~~~~~~~~~

**Description:** (Re)authorizes you in Simplifier.net

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``email=<value>``
     - assignment
     - Yes
     - The email of your Simplifier account.
   * - ``password=<value>``
     - assignment
     - Yes
     - The password of your Simplifier account.
   * - ``--agent``
     - flag with value
     - No
     - Allow an additional agent string to be registered by the server

The login command logs you in into Simplifier.net, with your username and password. Once logged in,
you have access to all your data and projects in Simplifier. This is specifically needed for actions
like synchronizing private projects to your local disk.

Once you are logged in, a token will be saved, so that you will be logged in automatically on
consequent communication with Simplifier.net.

If you do not provide your credentials as parameters, they will be asked. In this case,
your password will not become visible.

----

``fhir logout``
~~~~~~~~~~~~~~~

**Description:** Clears the credentials for Simplifier.net

The logout command removes any credentials and authentication tokens from your local machine.
You will have to log in on the next request to Simplifier.net.

----

``fhir projects``
~~~~~~~~~~~~~~~~~

**Description:** Shows your Simplifier.net projects

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``--details``
     - flag
     - No
     - Also list the fhir version, title and description of each project

The projects command lists all projects in Simplifier.net where you are a member of. It groups them
per FHIR version.

----

``fhir project archive``
~~~~~~~~~~~~~~~~~~~~~~~~

**Description:** Creates an archive from the current project

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``--output``
     - flag with value
     - No
     - The format of the output package.

Creates a generic package from your project.

----

``fhir project clone``
~~~~~~~~~~~~~~~~~~~~~~

**Description:** Clones a project from Simplifier.net in a sub folder

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<urlkey>``
     - string
     - Yes
     - The URL key of the project on Simplifier.net
   * - ``<name>``
     - string
     - No
     - Optional name for the sub folder

The clone command creates a folder with the full contents of a project on Simplifier.net. It is very similar to
git clone.

For this command you need to have an account on Simplifier.net.

----

``fhir project info``
~~~~~~~~~~~~~~~~~~~~~

**Description:** Gets the Simplifier project key for your folder

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``--details``
     - flag
     - No
     - Show additional details of the project.

The info command displays the url key for the project on Simplifier that this folder is connected to.

----

``fhir project link``
~~~~~~~~~~~~~~~~~~~~~

**Description:** Links your folder with a project on Simplifier.net

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<urlkey>``
     - string
     - Yes
     - The URL key of the project on Simplifier.
   * - ``--strategy``
     - flag with value
     - No
     - The strategy to use when there are file conflicts. The default strategy is TakeLocal.

The link command links an existing (non-empty) folder to a project on Simplifier.

----

``fhir project pull``
~~~~~~~~~~~~~~~~~~~~~

**Aliases:** ``fhir project download``, ``fhir project fetch``

**Description:** Pulls all changes from the project on Simplifier.net to your folder

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``--strategy``
     - flag with value
     - No
     - The optional strategy to use when there are file conflicts. The default is to leave conflicts unresolved.

The pull command synchronizes your local project with the state of your project on Simplifier.net.
You need to be an author in the project you want to sync.

----

``fhir project push``
~~~~~~~~~~~~~~~~~~~~~

**Aliases:** ``fhir project upload``

**Description:** Pushes all changes from your folder to the project on Simplifier.net

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``--strategy``
     - flag with value
     - No
     - The optional strategy to use when there are file conflicts. The default is to leave conflicts unresolved.

The push command sends all changes of your local folder to your project on Simplifier.net.
You need to be an author in the project you want to sync.

----

``fhir project repair``
~~~~~~~~~~~~~~~~~~~~~~~

**Description:** Repairs the link between your folder and a project on Simplifier.net

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<urlkey>``
     - string
     - Yes
     - The URL key of the project on Simplifier.

The repair command repairs the link between a folder and a project on Simplifier.net.

----

``fhir project status``
~~~~~~~~~~~~~~~~~~~~~~~

**Description:** Shows the status of the project

The project status command shows the current status of the files in the project.
Which  files are new or have changed and need to be uploaded.
Any files and folders that will be ignored when uploading.
And which files are missing or have changed and need to be downloaded.

----

``fhir project sync``
~~~~~~~~~~~~~~~~~~~~~

**Aliases:** ``fhir project update``

**Description:** Synchronizes your folder with the project on Simplifier.net

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``--strategy``
     - flag with value
     - No
     - The optional strategy to use when there are file conflicts. The default is to leave conflicts unresolved.

The sync command synchronizes your local project with the state of your project on Simplifier.net.
For this command you need to have set up the project with 'clone' or 'link'.
You need to be an author in the project you want to sync.

----

``fhir project unlink``
~~~~~~~~~~~~~~~~~~~~~~~

**Description:** Removes the link between your folder and the project on Simplifier.net

The unlink command removes the link between a folder and the project on Simplifier.net.

Stack
=====

``fhir bundle``
~~~~~~~~~~~~~~~

**Description:** Takes all resources on the stack and puts them into a single bundle

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<files>``
     - string
     - No
     - Use set of file names instead of using the stack. You can use wildcards like: \*.xml
   * - ``--take``
     - flag with value
     - No
     - Limit the amount of resources to take from the stack, Consumption starts from the top.

The Bundle command takes all resources on the stack constructs a FHIR bundle from them
and places the resulting bundle back onto the stack.

----

``fhir clear``
~~~~~~~~~~~~~~

**Description:** Clears the stack

The clear command removes all resources from the resource stack.

If you want to remove just one resource, you can use the pop command.

----

``fhir dup``
~~~~~~~~~~~~

**Description:** Duplicates the top resource from the stack

The dup operation, duplicates the resource on top of the stack. The top two entries
will now contain the same resource. This is useful if you work with a command that consumes
the top of the stack, or it allows you to keep the original, in case an operation transforms
the top resource.

----

``fhir filter``
~~~~~~~~~~~~~~~

**Description:** Filters the stack on matching FhirPath expressions

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<expression>``
     - string
     - No
     - A FHIRpath expression
   * - ``--category``
     - flag with value
     - No
     - Filter based on category

The filter command takes a FHIRpath expression and matches all resources on the stack
against that expression. All resources that do not match, will be thrown away.

FHIRpath has tokens that can collide with your Windows or Linux console language. In order
to avoid these collisions, put your FHIRpath expression between double quotes:

Example: filter "meta.profile.exists()"

When filtering on category, the category can be a super or subset of resource types,
like Extension, Profile or Terminology. It can also be any resource type.

----

``fhir grab``
~~~~~~~~~~~~~

**Description:** Grabs a resource from inside a package and pushes it on the stack

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<scope>``
     - string
     - Yes
     - The package name (and optionally version, separated by '@')
   * - ``<reference>``
     - string
     - Yes
     - A canonical or a filename

The Grab command can get a resource from inside a package. The package must be installed
on the local computer. If the resource is found, it's pushed on the stack.

If the package is not installed, use 'fhir install' first.

----

``fhir ndjson``
~~~~~~~~~~~~~~~

**Description:** Pushes NDJson file resources on the stack

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<source>``
     - string
     - Yes
     - A filename or url
   * - ``--bundle``
     - flag
     - No
     - wether to save the result as a single bundle or separate resources

Fetches an NDJson file and pushes the resources in that file onto the stack.
Note that you can usually just use the push command for this. The NDJson command is only
needed if the file cannot be recognized by its extension: .ndjson.

----

``fhir peek``
~~~~~~~~~~~~~

**Description:** Displays the top item on the stack

The peek command tells you the type and id of the resource on the top of the stack,
without removing it.

----

``fhir pop``
~~~~~~~~~~~~

**Aliases:** ``fhir drop``

**Description:** Drops the top resource from the stack

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<count>``
     - string
     - No
     - If you want to remove more than one resource, give a number

This command removes the top of the stack and throws it away. Alternatively you can use
both drop and pop as a command, since they are both common in stack implementations.

If you want to remove all resources from the stack, use 'clear' instead.

----

``fhir push``
~~~~~~~~~~~~~

**Description:** Pushes matching files or server resources on the stack

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<files>``
     - string
     - No
     - The file(s) to push. You can use wild cards (like \*.xml)
   * - ``--filter``
     - flag with value
     - No
     - A fhir path expression to filter matching resources
   * - ``--ignore``
     - flag
     - No
     - Ignore files that can't be parsed

The push command puts a resource or a set of resources on the top of the resource stack.
This allows you to perform consecutive commands on that resource (or those resources).

----

``fhir reverse``
~~~~~~~~~~~~~~~~

**Description:** Reverses the order of all resources on the stack

The reverse command reverses the order in which all resources are placed on the stack.
So the bottom becomes the top and vice versa.

----

``fhir save``
~~~~~~~~~~~~~

**Description:** Saves resource(s) on stack to disk

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<filename>``
     - string
     - No
     - The filename pattern to save the file to.
   * - ``--all``
     - flag
     - No
     - Use this flag to save the whole stack as separate resources
   * - ``--json``
     - flag
     - No
     - Save the file(s) as JSON
   * - ``--xml``
     - flag
     - No
     - Save the file(s) as XML
   * - ``--pretty``
     - flag
     - No
     - Whether to format the JSON/XML to make it readable.

The save command is the inverse of the push command - it saves one or more resources from
the top of the resource stack to the current folder on disk. It gives you control over the
serialization format (xml or json) and the file name.

You can use any FHIRpath expression between brackets {}. Examples are:
{type}-{id}-{name.given[0]}.xml

Next to FHIRpath, you can also use the {type} and {hash} variables. The hash will create an
alphanumeric sequence unique to the resource. Do make sure the value is actually present in
the resource.

if an extension is given, the resource will automatically be saved to that format.

----

``fhir show``
~~~~~~~~~~~~~

**Description:** Displays a resource tree from a file or the top of the stack

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<path>``
     - string
     - No
     - Define a file instead of the the resource on top of the stack
   * - ``--types``
     - flag
     - No
     - Show the type of each element
   * - ``--output``
     - flag with value
     - No
     - The type of rendering.

The show command prints the full contents of the resource on top of the stack.
It will show the name and value of each element in the resource.
This can be done as tree, XML or JSON.

----

``fhir sort``
~~~~~~~~~~~~~

**Description:** Sorts the stack based on the expression

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<expression>``
     - string
     - Yes
     - The FHIR path expression that selects the value to compare
   * - ``--descending``
     - flag
     - No
     - Sort in descending order

The sort commands sorts all resources on the stack. The sort compares the values that are extracted using
the provided FHIRpath expression.

You can use the following statement to check the result:
> fhir stack "from Resource select [expression]"
Where [expression] is the expression you used for sorting.

----

``fhir split``
~~~~~~~~~~~~~~

**Description:** Splits the bundle on the top of the stack into separate resources

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``--all``
     - flag
     - No
     - 

The split command takes a bundle from the top of the stack, and places the individual resources from
that bundle back onto the stack. If the resource is not a bundle, nothing happens.

----

``fhir stack``
~~~~~~~~~~~~~~

**Description:** Lists the items on the stack

.. list-table::
   :header-rows: 1
   :widths: 20 15 10 55

   * - Parameter
     - Type
     - Required
     - Description
   * - ``<fql>``
     - string
     - No
     - 

The stack command lists the resources currently on the stack by type and id.
You can optionally add an FQL query, if you want different data listed for this.
For FQL see: https://simplifier.net/docs/FQL
Example: stack "from Resource select type(), id, meta.profile"

----

``fhir swap``
~~~~~~~~~~~~~

**Description:** Swaps the top 2 elements on the stack

The swap command swaps the top two values on the stack, so that the second resource
comes on the top.

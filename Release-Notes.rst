Release Notes
=============

This page contains the release notes of the current major version of Firely Terminal (v3).


Current Release
~~~~~~~~~~~~~~~

Release 3.2.0-beta-1, September 19th, 2023
------------------------------------------

Features
^^^^^^^^

#. Bake: We upgraded the ``bake`` engine.
#. Quality Control: We upgraded the Quality Control engine.
#. Inflate: We improved the behavior of the ``inflate`` function. If snapshots are already present in a resource these will no longer be regenerated.  We also added an option to turn off inflate completely, using ``config inflate``.
#. NDJSON: Firely Terminal can now read NDJSON from files or from a URL, and push it on the stack or write it to disk.
#. Monochrome  output: We have added support for monochrome output, which is especially usefull for our Mac users.
#. We have added several commands to compare two files: ``diff`` and ``contentdiff``. These are still experimental features.

Bugfixes
^^^^^^^^

#. The ``bundle`` command had a bug where the stack was cleared if you would set a number of resources higher than those present in the stack to the ``--take`` parameter. This is now fixed.
#. Fixed an issue with the ``deflate`` command not working properly.
#. Fixed an issue for r4b resources where an error would pop up after running ``fhir spec r4b`` and next ``fhir init``. 
#. The ``inflate`` command only worked for packages, but not for projects. This is now fixed.
#. When using the ``search`` command any additional parameters would not be taken into account when performing the search. this is now fixed.
#. Fixed an issue with the ``yaml`` command that was caused by Firely Terminal not providing a scope for resolving resources.
 

Previous releases
~~~~~~~~~~~~~~~~~

Release 3.1.0, November 21th, 2022
----------------------------------

Features
^^^^^^^^

#. The command ``project pack`` has been renamed to ``project archive``. The ``project archive`` command creates a zipped folder from the current project.
#. The original ``bake`` command has been renamed to ``inflate``. The ``inflate`` command creates snapshots and expands ValueSets for a project or package. When installing packages an inflate happens in the background for that package and for all dependencies.
#. The original ``unbake`` command has been renamed to ``deflate``. The ``deflate`` command removes snapshots/expansions for a project or package.
#. For publishing packages you can now use ``bake`` to publish your package to a given folder. The output folder is located by default in the subfolder /.bake. The ``pack`` command can be used to create a FHIR package .tgz from a folder.


Bugfixes
^^^^^^^^

#. UTF-8 with BOM encoded files: The old ``bake`` command was found to skip files with UTF-8 with BOM encoding. The ``bake`` and ``pack`` commands now correctly handle files with the following encodings: UTF-8, UTF-8 with BOM, ANSI, UTF-16 LE and UTF-16 BE.
#. Indexing: In order to fix duplicate entries and exclude source files in the .index.json file, the ``bake`` command now utilizes a default output subfolder: /.bake. 
#. In the past users experienced warnings from the terminology service when their project referred to other projects containing large terminology sets. The maximum size for terminology sets has been increased to 100.000 to prevent these warnings.


Release 3.0.0, July 22nd, 2022
------------------------------

This is first release that is based on .NET 6, until now Firely Terminal worked with .NET 3.1.

Features
^^^^^^^^

1. Firely Terminal was upgraded to .NET 6, meaning that it is necessary to install .NET 6 in order to upgrade the new Firely Terminal.
2. Bake: This release brings the first version of the Bake Pipeline. It is only available as a licensed feature.
3. Packages: You can now install a package from a package (tgz) file. Firely Terminal can now use a local local cache inside of your project folder using the ``--here`` parameter.
4. Quality Control: You can now specify the level of reporting for performing QC.
5. Simplifier synchronization: It is now possible to synchronize between a folder and a project on simplifier. Commands include ``project link`` and ``project unlink``.
6. Several commands were added to Firely Terminal, including:
     - Filter: with the ``filter`` command it is now possible to filter resources in the resource stack, or a folder on disk, using FHIRpath.
     - YAML Gen: We have added the ``yaml-gen`` command, A YAML resource parser for generating FHIR example resources. The engine is integrated with an agnostic version of our prior automatic example generator, so that you can now mix and match. This tooling is also available in the Simplifier playground.
     - Sorting: ``sort`` was added to sort the order of a stack.
     - Reverse: the ``reverse`` command has been added to reverse the order of the stack.
     - File deletion: with ``file delete`` you can delete resources in your folder based on a fhirpath expression.
     - Expand: ``expand`` was added to expand a valueset.
     - Predicate: The ``predicate`` gives the result (true/false) of FHIRpath predicate on a resource.
7. FQL4: This version contains the first experimental version of FQL 4, with the following features:
     - A select only statement. In the past, to select all resources in scope, you had to start with ``from Resource``. Now you can just write ``select`` immediatly, giving consise scripts.
     - Our table renderer now recognizes links as an ITypedElement node that contains an ``href`` field.
     - You can use the ``typename()`` function to get the type of the current node.
     - Fetch Queries: you have access to resources that are referenced in the current selection. The fetch operator jumps from a reference node to the resource it references.


Release 2.5.0-beta-1, November 24th, 2021
-----------------------------------------

Features
^^^^^^^^

1. Synchronization: With this release, we have more advanced synchronization logic that compares changes with the server. This resuls in less accidental overwrites. Many of the fhir project <command> paths have changed in the way they work. This is also the main reason this version is released as beta first.
2. Remote: Until now a fhir project folder had to have the same name as the url key of the project in Simplifier. This is no longer necessary. 
   You can configure the remote project link with::

     > fhir project remote <urlkey>

   If you wish to know the details of the current remote, use::

     > fhir project remote --details

3. Canonical Claims: With our release of Simplifier 28.2, we have matured canonical claims - the base(s) canonical that all canonicals in a project or package has to start with (conform to). This is an important driver to ensure the quality of conformance resources in projects and packages, and as such will have a significant impact on the ranking in search results etc. To help you with this, we have also added canonical claim checking to the Quality Control mechanism of Simplifier and Firely Terminal. 
   In Firely Terminal, add a file called project.yaml, that looks like this to add your canonical claims::

     canonical-claims:
        - http://myorg.net/fhir
        - http://mystandard.org/fhir/Structure/

   When running quality control, you will get the warning messages for any resource that has a canonical that does not conform to any of the listed canonical claims.
   If you have your own custom Quality Control rules, (a ``<name>.rules.yaml`` file), you can enable canonical-claim checking with this rule::

        - action: claims
          status: Checking if all conformance resources adhere to your canonical claims # or write your own text here.
4. Suite Validator: We have been working on a new validator in the past year which is part of our component suite. It's a validator that is more flexible and has been built in close alignment with HL7. As a result it does not only meets the quality standards that we think are important, but also the latest requirements by HL7. This validator has now been build into Firely Terminal. It is still in try-out (beta) feature. 
   So it's not enabled by default. In order to use it, use the ``--suite flag``::

     > fhir push mypatient.xml
     > fhir validate --suite
     
   When this feature has matured, you can use it in any part of Firely Terminal where validation is involved.


Bugfixes
^^^^^^^^

#. Snapshots: Since 3.4, we bake packages during install, and manually using fhir bake. But the snapshots generated by this bake were incorrect, due to the bake command not generating the proper element id's. This is fixed in this release.
#. Engines: There were some differences in the engines for the different FHIR versions that Firely Terminal supports (the ones you can switch with the fhir spec command). These engines have been aligned.


Release 2.4.0, October 15th, 2021
---------------------------------

Features
^^^^^^^^

#. Bake: Packages are now baked (snapshot are generated) when installed. Baking is also done with each restore (e.g. after cloning a project).
#. FHIR: Firely Terminal now has support for FHIR R4B, R5 (ballot).
#. Search: It is now possible to search multiple pages with the ``--page n``  parameter.
#. Search: Search now returns bundles unless you also provide a ``--split`` parameter.
#. Split bundles: You can now split all bundles on the stack in one command, with ``split --all``
#. You can now get command specific help by using ``-?``, ``--help``, or ``(-h)`` at the end of the command.


Release 2.2.0, June 21st, 2021
------------------------------

Features
^^^^^^^^

#. FQL3: This version of Firely Terminal integrates FQL3. FQL is now a licensed feature. For documentation on this, see `the FQL documentation <https://simplifier.net/docs/fql/home>`_.
#. SDK: This version of Firely Terminal makes use of .Net SDK version 3.2.
#. Performance: Validation, snapshot and Quality Control performance is improved.

Bugfixes
^^^^^^^^

#. Project synchronization was not picking up local changes, this is now fixed.
#. Snapshot generation was broken on Linux for Firely Terminal 2.1, this has now been fixed.

Release 2.1.0, April 26st, 2021
-------------------------------

Features
^^^^^^^^

1. QC: Quality Control for the command line for off line use and CI/CD pipeline scenarios is now available. This system uses the same engine as Simplifier QC. This feature requires a license.
2. New commands:
     - ``predicate``- evaluates the top of the stack a FHIRpath expression to true or false.
     - ``reverse`` - Reverses the order of all resources on the stack.
     - ``sort`` - sorts all elements on the stack on a given comparison expression.
     - ``query`` - FQL is now officially supported.
3. Synchronization: Project synchronization with Simplifier was redesigned to make it simpler and more predictable. We now use ``project update/clone/push`` instead of ``sync``.
4. SDK: Upgrade to .Net SDK version 3.0.
5. Renamed ``remove`` to ``uninstall``.
6. Renamed ``mode`` to ``spec``.
7. Logging: Log levels ``silent``, ``brief``, ``normal``, ``detailed``, ``debug`` are now available.


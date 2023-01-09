Release Notes
=============

This page contains the release notes of the current major version of Firely Terminal (v3).


Current Release
~~~~~~~~~~~~~~~

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

Previous releases
~~~~~~~~~~~~~~~~~

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



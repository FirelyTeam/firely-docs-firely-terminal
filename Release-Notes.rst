Release Notes
=============

This page contains the release notes of the current major version of Firely Terminal (v3).

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
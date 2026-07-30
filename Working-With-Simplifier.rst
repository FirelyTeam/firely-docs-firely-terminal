.. _working_with_simplifier:

Working with Simplifier
~~~~~~~~~~~~~~~~~~~~~~~

This page describes the typical flow for getting a Simplifier.net project onto your machine and keeping
the two in sync. See :ref:`simplifier_sync` and the :ref:`command_reference` for all available commands.

1. Log in
---------

Almost every project command talks to Simplifier.net on your behalf, so start by logging in:

.. code-block:: Bash

     > fhir login email=<your email> password=<your password>

If you leave out the parameters, they will be asked interactively and your password stays hidden.
The login token is stored, so you only have to do this once per machine.

To see which projects you are a member of:

.. code-block:: Bash

     > fhir projects

The name you need in the next step is the **URL key** of the project: the part after
``simplifier.net/`` in the project URL.

2. Get the project on your machine: clone or link
-------------------------------------------------

Use ``clone`` when you do **not** have the project locally yet. It creates a new sub folder with the
full contents of the project, much like ``git clone``:

.. code-block:: Bash

     > fhir project clone <urlkey>
     > fhir project clone <urlkey> <foldername>   #optional: pick your own folder name

Use ``link`` when you **already have a folder** with your project files, for example a checkout of a
Git repository that you want to connect to a Simplifier project:

.. code-block:: Bash

     > cd <your project folder>
     > fhir project link <urlkey>

.. note::
   Start ``link`` from an empty folder or from a folder that already contains exactly what you expect
   to be in the project. Linking a folder that has unrelated content in it will make the first
   synchronization messy, and you may end up uploading or deleting files you did not intend to.

Linking resolves conflicts with the ``--strategy`` parameter (see below). Its default is ``TakeLocal``,
so your local files win:

.. code-block:: Bash

     > fhir project link <urlkey> --strategy TakeLocal

Cloning or linking also installs the dependencies listed in the project's ``package.json``, so you
can start working right away. You can always trigger this yourself, for example after adding a
dependency, and check what ended up in your scope:

.. code-block:: Bash

     > fhir restore      #install the dependencies from package.json
     > fhir scope        #check the installed dependencies

3. Keep both sides in sync
--------------------------

There is **no two-way synchronization between Simplifier and GitHub** (or any other Git host).
Firely Terminal is the bridge: changes made in the Simplifier web UI do not appear in your Git
repository until you pull them down and commit them, and local commits do not appear on Simplifier
until you push them up.

.. code-block:: Bash

     > fhir project status              #see what changed on either side
     > fhir project sync                #synchronize both ways
     > fhir project pull                #only bring changes from Simplifier down
     > fhir project push                #only send local changes up

A useful habit is to run ``fhir project status`` before synchronizing, and to commit your local
changes to Git first, so you always have a way back.

Resolving conflicts
-------------------

A conflict occurs when the same file changed on both sides. ``pull``, ``push`` and ``sync`` leave
conflicts unresolved by default; ``--strategy`` tells Firely Terminal how to decide:

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Strategy
     - Effect
   * - ``TakeLocal``
     - Take the files from your project folder. Files on Simplifier that do not exist locally are deleted.
   * - ``TakeRemote``
     - Take the files from Simplifier. Files in your project folder that do not exist on Simplifier are deleted.

.. code-block:: Bash

     > fhir project sync --strategy TakeLocal

If your project folder is a Git repository, ``TakeLocal`` is almost always the right choice: Git is
your source of truth, and anything you might lose is recoverable from your repository history.
Use ``TakeRemote`` when the work you want to keep was done in the Simplifier web UI and has not
been committed locally yet.

.. warning::
   Both strategies delete files that are missing on the winning side. Commit or back up your work
   before running a synchronization with a strategy.

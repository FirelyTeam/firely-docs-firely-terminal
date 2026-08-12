.. _private_package_feeds:

Private package feeds
=====================

.. important::

   Package feeds are available from the Professional plan and up. `See the pricing page for details. <https://simplifier.net/pricing>`_

A feed is a named container for packages on Simplifier, with its own access control. By default
everything lives in the main public feed. A **private feed** is a separate space that is only visible to
the members of the team that manages it, which is how you keep packages internal to your organization.

.. seealso::
   Feeds themselves are created and managed on Simplifier, not from Firely Terminal. For creating a
   feed, assigning it to a project, upstream feeds and access control, see
   :ref:`private package releases with feeds <simplifier_docs:package_feeds>` in the Simplifier
   documentation.

This page covers the Firely Terminal side: pointing a project at an existing feed, so that installing
and publishing packages happen against that feed instead of the public one.

Setting the feed of a project
-----------------------------

You need to be logged in with an account that has access to the feed:

.. code-block:: Bash

   > fhir login email=<your email> password=<your password>

Then set the feed on your project, using the **URL key** of the feed (the short identifier you chose
when creating it on Simplifier, for example ``my-org-internal``):

.. code-block:: Bash

   > fhir feed my-org-internal
   > fhir feed                    #shows the feed currently configured, or (none)
   > fhir feed --clear            #back to the public package server

Setting a feed writes a ``feed.config.json`` file next to your ``package.json``. It contains nothing but
the feed key — no credentials — so you can safely commit it, and everyone working on the project
resolves packages from the same feed.

After changing or clearing the feed, run a restore. The set of packages that can be resolved changes
with the feed, so the existing dependency closure is no longer necessarily valid:

.. code-block:: Bash

   > fhir restore

Working with a feed
-------------------

Once a feed is configured, all server-facing package commands in that project use it. There is no
separate flag: the feed determines which remote packages you see and whether the request is authenticated.

.. code-block:: Bash

   > fhir find myprofiles                  #searches the feed
   > fhir versions xyz.myprofiles          #versions available in the feed
   > fhir install xyz.myprofiles           #installs from the feed
   > fhir publish-package mypackage.tgz    #publishes to the feed

.. warning::
   A feed replaces the package server for the project — it is not a second source that is searched in
   addition to the public one. Make sure that **every** package you need is available in your feed,
   including public packages such as ``hl7.fhir.r4.core``. On Simplifier you arrange this by adding the
   public feed as an upstream feed, so that public packages are pulled in on demand.

A project has one feed at a time. If you need packages from two private feeds, combine them on
Simplifier with an upstream feed rather than switching back and forth.

Publishing to a feed
--------------------

Publishing works the same as publishing publicly, but the package ends up in the configured feed.
``publish-package`` reports the target feed before it uploads, so you can check it:

.. code-block:: Bash

   > fhir pack
   > fhir publish-package mypackage.tgz --first

.. warning::
   A package cannot be moved from a private feed to the public feed afterwards. Make sure you publish to
   the intended feed the first time.

Notes
-----

* Packages from a private feed are stored in the same package cache as public ones. If you want to keep
  them separate — on a build agent, for instance — give the project its own cache with
  ``fhir cache use-local``.
* The ``--authenticate`` and ``--private`` flags of ``fhir install`` are obsolete. Authentication is
  determined by the feed configuration of the project.
* Do not use ``fhir source`` to point at a feed. ``source`` is a machine-wide setting for the package
  server itself and does not authenticate; ``fhir feed`` is the per-project setting you want.

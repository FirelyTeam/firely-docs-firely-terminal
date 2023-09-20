.. _Package_management:

Package Management
==================

Firely Terminal is a FHIR package manager. Packages are the core distribution mechanism for sets of profiles and the recommended way to do resource versioning. Profiles and other conformance 
resources rarely live in isolation. Together they form a use case, and they usually need other resources from a national initiative, and they certainly need the FHIR core base profiles.

In FHIR these sets are distributed according to the `FHIR package standard <https://confluence.hl7.org/display/FHIR/NPM+Package+Specification>`_, 
which is based on, but does not conform to, `NPM package standard <https://www.npmjs.com/>`_. There are a few additions and a few restrictions.

In the pages below you will find more information on the possibilities of package management within Firely Terminal.

.. toctree::
  :maxdepth: 1
  :titlesonly:
 
  Managing-Packages
  Using-NPM-instead
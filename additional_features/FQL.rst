FQL
===

FQL is a query language that allows you to retrieve, filter and project data from any data source containing FHIR Resources. It combines the power of three existing languages: SQL, JSON and FhirPath.
Although the primary goal is to make tables from FHIR data, FQL can be used in a much broader scope and can cast data into almost any form. FQL is also implemented in Firely Terminal. You can execute FQL queries on your own projects, using a statement like this::

    > fhir query "from Patient select name.given"

For more extensive documentation on FQL, see the link to `the Simplifier.net documentation <https://simplifier.net/docs/fql/home>`_.
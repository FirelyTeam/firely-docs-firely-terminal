Simplifier Sync
===============

You can interact with Simplifier.net to use your own FHIR resources. In order to 
login to your Simplifier account you use the ``login`` command:

.. code-block:: Bash

     > fhir login email=<your email> password=<your password> 

Other operations are: 

.. code-block:: Bash

     > fhir logout            #Clears the credentials for Simplifier.net
     > fhir license           #Lists the features in your license
     > fhir doc <canonical>   #Brings you to the documentation page of the resource

After a successful login you can use the ``projects`` command to list all your projects on Simplifier

.. code-block:: Bash

     > fhir projects

You can perform the following commands with your projects:

.. code-block:: Bash

     > fhir project clone <project>     #Clones a project from Simplifier.net in a sub folder
     > fhir project update              #Synchronizes your project with a specific folder
     > fhir project sync                #Synchronizes current folder with the corresponding Simplifier project
     > fhir push                        #Pushes all changes to the corresponding project on Simplifier
     > fhir pack                        #Creates a package from the current project

The intended workflow is as follows: 

* **Clone your project.**
* **Set the working directory to <your project>.**
* **Make changes to your project locally.**
* **Sync your project with Simplifier.**

After a project is cloned you can see what is in the folder by using ``ls``, this will show you a list of all the content. 
You will see that there is a package.json file in there. These are the dependencies that this project depends on. This is setup
in Simplifier with the dependencies, but we also have to install them locally. You can install them by running the ``restore`` command.

.. code-block:: Bash

     > fhir restore

You can check whether the restore is performed correctly by using the ``scope`` command.
This will show you the full list of dependencies installed for your project. 

.. code-block:: Bash

     > fhir scope

**Note:** When using the ``sync`` and ``push`` command Firely Terminal deduces the project name from the foldername you are currently
in. With these operations a .ZIP file is send to Simplifier to import. This currently does not give you feedback in Firely Terminal.


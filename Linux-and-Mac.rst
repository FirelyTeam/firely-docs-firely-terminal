Using Firely Terminal on Linux and Mac
======================================

Firely Terminal can be used cross platform. For Linux and Mac you need to make sure that the dotnet tools are added ot the path.

The workflow for both platforms: 

1. Make sure you have installed .NET SDK 3.1. 
    - You can have other versions of the SDK and runtimes, but you should at least have 3.1. 

2. Install Firely Terminal using the `Firely Terminal download command <https://simplifier.net/downloads/firely-terminal>`_
    - ``dotnet tool install -g firely.terminal``


3. Make sure that the dotnet tools directory is added to your path, this makes it possible bo run the ``fhir`` command from any directory.
    - Please follow the `instructions <https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-tool-install#description>`_ on how to install the directory on any operating system. 
    - For Linux and Mac, make sure you add your .profile or .bash_profile to your path.

4. Test if everything is working.  
    - ``fhir --version``
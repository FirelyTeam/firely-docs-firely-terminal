Installing Firely Terminal
==========================

Go to the `Firely Terminal download page <https://simplifier.net/downloads/firely-terminal>`_ for instructions on how to install Firely Terminal.

Install Firely Terminal:

``dotnet tool install -g firely.terminal``

Update to the newest version of Firely Terminal:

``dotnet tool update -g firely.terminal``

Uninstall Firely Terminal:

``dotnet tool uninstall -g firely.terminal``

Installing Firely Terminal on Linux and Mac
-------------------------------------------

Firely Terminal can be used cross platform. For Linux and Mac you need to make sure that the dotnet tools are added ot the path.

The workflow for both platforms: 

1. Make sure you have installed .NET SDK 6 if you are using Firely terminal 3.0.0 or later versions.
    - You can have other versions of the SDK and runtimes, but you should at least have .NET 3.1 for FT versions < 3.0.0 and .NET 6.0 for FT versions >= 3.0.0. 

2. Install Firely Terminal using the `Firely Terminal download command <https://simplifier.net/downloads/firely-terminal>`_
    - ``dotnet tool install -g firely.terminal``

3. Make sure that the dotnet tools directory is added to your path, this makes it possible bo run the ``fhir`` command from any directory.
    - Please follow the `instructions <https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-tool-install#description>`_ on how to install the directory on any operating system. 
    - For Linux and Mac, make sure you add your .profile or .bash_profile to your path.

4. Test if everything is working.  
    - ``fhir --version``
    
Trouble shooting
~~~~~~~~~~~~~~~~
After installing the .NET SDK you might have to reload your shell in order for 'dotnet' to be in your PATH. 
Sometimes you will have to manually add 'fhir'  by adding ``~/.dotnet/tools`` to your PATH. For more information please see the `.NET documentation. <https://docs.microsoft.com/en-us/dotnet/core/tools/troubleshoot-usage-issues#global-tools>`


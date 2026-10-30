Installing Firely Terminal
==========================

Go to the `Firely Terminal download page <https://simplifier.net/downloads/firely-terminal>`_ for instructions on how to install Firely Terminal.

.. important::
   Firely Terminal 4.0.0 and later require the `.NET 10 SDK <https://dotnet.microsoft.com/download/dotnet/10.0>`_.
   Install it before you install or update Firely Terminal. You can keep other .NET versions installed next to it.

   With only an older SDK installed, ``dotnet tool install`` and ``dotnet tool update`` fail with
   *Settings file 'DotnetToolSettings.xml' was not found in the package*. That message comes from
   .NET and does not mention the version: install the .NET 10 SDK and run the command again.

   ===================== =================
   Firely Terminal       .NET SDK required
   ===================== =================
   4.0.0 and later       .NET 10
   3.3.0 to 3.5.x        .NET 8
   3.0.0 to 3.2.x        .NET 6
   Before 3.0.0          .NET Core 3.1
   ===================== =================

Install Firely Terminal:

``dotnet tool install -g firely.terminal``

Update to the newest version of Firely Terminal:

``dotnet tool update -g firely.terminal``

Uninstall Firely Terminal:

``dotnet tool uninstall -g firely.terminal``

Installing Firely Terminal on Linux and Mac
-------------------------------------------

Firely Terminal can be used cross platform. For Linux and Mac you need to make sure that the dotnet tools are added to the path.

The workflow for both platforms: 

1. Make sure you have installed the .NET 10 SDK, which Firely Terminal 4.0.0 and later require.
    - You can have other versions of the SDK and runtimes installed next to it. For older versions of Firely Terminal, see the table at the top of this page.

2. Install Firely Terminal using the `Firely Terminal download command <https://simplifier.net/downloads/firely-terminal>`_
    - ``dotnet tool install -g firely.terminal``

3. Make sure that the dotnet tools directory is added to your path, this makes it possible bo run the ``fhir`` command from any directory.
    - Please follow the `instructions <https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-tool-install#description>`_ on how to install the directory on any operating system. 
    - For Linux and Mac, make sure you add your .profile or .bash_profile to your path.

4. Test if everything is working.  
    - ``fhir --version``
    
Troubleshooting
~~~~~~~~~~~~~~~~
After installing the .NET SDK you might have to reload your shell in order for 'dotnet' to be in your PATH. 
Sometimes you will have to manually add 'fhir'  by adding ``~/.dotnet/tools`` to your PATH. For more information please see the `.NET documentation. <https://docs.microsoft.com/en-us/dotnet/core/tools/troubleshoot-usage-issues#global-tools>`


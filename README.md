# windows10_multiuser_session

**instructions took for this [link](http://www.serverwatch.com/server-tutorials/remote-desktop-connections-for-multiple-users-on-windows-10-and-windows-server-2012.html)**

## Remote Desktop Connections for Multiple Users on Windows 10 and Windows Server 2012

Our previous Server Tutorial on enabling remote desktop connections for multiple users covered earlier Windows operating systems like Windows 7 and Windows Vista.

Today we'll look at potential issues and workarounds involving remote desktop connections for multiple users on Windows 8, Windows 10, Windows Server 2012 and the forthcoming Windows Server 2016.

Multiple Remote Desktop Connections on Windows 10 and Windows 8

Server Tutorials - Rounded As with earlier versions of the Windows operating system like Windows 7 and Windows Vista, both Windows 10 and Windows 8 / Windows 8.1 restrict the use of Microsoft's Remote Desktop Protocol (RDP) to one remote connection, preventing users from making multiple remote desktop connections.

As a result, only one remote user can work on a local machine at a time, and if a second RDP session is attempted, the session of the first user will be prompted to shut down.

There are several workarounds that can be deployed to enable simultaneous multiple remote desktop connections on Windows 10 or Windows 8. We'll take a closer look at two of the most popular options, although we recommend first consulting your Microsoft License Agreement as these system modifications may be treated as a violation of the agreement by Microsoft.

**Option 1: Modifying termsvrl.dll**
The first option to enable multiple concurrent remote desktop connections in Windows 10 / Windows 8 is to modify the termsrv.dll library file used by Remote Desktop Services, which restricts the number of RDP connections to one.

There are several key steps you'll need to take prior to modifying the termsrv.dll file.

First, create a backup of the termsrv.dll file, which can be found in the C:\Windows\System32 directory. You can do this through the command line prompt (cmd.exe) with the following command:

**copy c:\Windows\System32\termsrv.dll termsrv.dll_backup**

Once the file has been backed up, you'll need to become the owner of the termsrvl.dll file and assign the Administrators group full rights to the file.

To do this, click on the Properties tag after right-clicking on the termsrv.dll file, then go to the Security tab and click on Edit. In the access list, find the local administrators group and give it full control over this file and then save the changes.

Next, stop the Remote Desktop service (TermService) in either the services.msc console or from the command prompt with the following:

Net stop TermService

Now open the termsrv.dll file using any HEX editor (such as the freeware HxD editor) and search for the following line:

**39 81 3C 06 00 00 0F 84 73 42 02 00**

Replace this line with:

**B8 00 01 00 00 89 81 38 06 00 00 90**

Finally, save the termsrv.dll file and run TermService before attempting to run multiple remote desktop connections by opening a second RDP session.

**Option 2: RDP Wrapper Library**
Fortunately, there's an even simpler and more efficient alternative to modifying the termsrv.dll file in the form of the RDP Wrapper Library project, which can be downloaded at https://github.com/stascorp/rdpwrap/releases(the latest release is v1.6).

It's important to note that the RDP Wrapper Library doesn't make any actual changes to the termsrv.dll file; rather, it simply loads termsrv with changed parameters.

The benefit to this is that even if Microsoft updates the termsrv.dll file in a Windows update, the RDP Wrapper Library should still work without any issues.

The RDP Wrapper Library serves as a layer between SCM (Service Control Manager) and Terminal Services, and it not only enables support for multiple simultaneous RDP sessions, it also enables RDP Host support on Windows 10.

To use the RDP Wrapper, you'll first need to install the RDP Wrapper Library by running install.bat with Administrator privileges.

Once the installation is complete, run RDPConfig.exe and first check that all elements in the Diagnostics section are green.

Next, test the ability to run multiple remote desktop connections by opening a second RDP session. You should be able to successfully enable remote desktop connections for multiple users on Windows 10 or Windows 8 at this point.

Now we'll turn our attention to the server versions of Windows, Windows Server 2012 and the forthcoming Windows Server 2016.

## Multiple Remote Desktop Connections on Windows Server 2016 and Windows Server 2012

Turning to the server editions of Windows, both Windows Server 2012 and Windows Server 2016 allow only a single Remote Desktop session, preventing multiple remote desktop connections. You can however choose to enable (or disable) multiple RDP sessions as needed.

Enabling Multiple Remote Desktop Connections in Windows Server
To enable multiple remote desktop connections in Windows Server 2012 or Windows Server 2016, you'll need to access the server directly or through Remote Desktop. Once you've logged in, press the Windows key in Windows Server 2012 to open the Start screen or simply type the following into the Start bar in Windows Server 2016: gpedit.msc.

This will launch the Group Policy Editor (**gpedit.msc**), which is a management console through which you can configure many Windows system properties or run scripts.

Once the Group Policy Editor is running, navigate to:

**Computer Configuration > Administrative Templates > Windows Components > Remote Desktop Services > Remote Desktop Session Host > Connections**.

From here, first set the Restrict Remote Desktop Services user to a single Remote Desktop Services session parameter to Disabled.

Next, double-click on Limit number of connections and then set the RD Maximum Connections allowed to 999999.

Disabling Multiple Remote Desktop Connections in Windows Server
To disable multiple remote desktop connections in Windows Server 2012 or Windows Server 2016, first log in to the local computer and launch the Group Policy Editor through the Start menu as detailed above.

In gpedit.msc, navigate to:

Computer Configuration > Administrative Templates > Windows Components > Remote Desktop Services > Remote Desktop Session Host > Connections.

Next, simply set the Restrict Remote Desktop Services user to a single Remote Desktop Services session parameter to Enabled, and now you're once again restricted to a single Remote Desktop session.

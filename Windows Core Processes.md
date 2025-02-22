- User mode vs Kernel mode:
	- User mode:
		- Private virtual address space for each app
		- Apps cannot modify another app's data
		- A process running in user mode can't access virtual addresses that are reserved for the operating system
		- Therefore we need something to access kernel mode resources which is syscalls
	- Kernel mode:
		- It grants unrestricted access to system resources and hardware
		- All code running in kernel mode shares a single virtual address space
		-  No isolation between drivers and OS
-----
- PID is random, doesn't show parent relation
- First user to login has session id of 1
----
- The System process
	- Has kernel-mode system threads which execute code in kernel mode only, and they must allocate dynamic storage from heaps.
	- Has no parent (except System Idle Process (0))
	- One instance only
	- Should run in session 0 only
	- process ID 4
----
![[i82y9725.bmp]]

----
- **Session Manager Subsystem process** (smss.exe)
	- Also known as the **Windows Session Manager**
	- responsible for creating new sessions & environment variables
	- first user-mode process started by the kernel
	- This process starts the kernel and user modes of the Windows subsystem
	- Windows subsystem: includes win32k.sys (kernel mode), winsrv.dll (user mode), and csrss.exe (user mode).
	- starts ==`csrss.exe`== (Windows subsystem) and ==`wininit.exe`== in Session 0
	- starts ==`csrss.exe and winlogon.exe`== for Session 1
	- self terminates after starting children
	- launches other subsystems with required value in ``HKLM\System\CurrentControlSet\Control\Session Manager\Subsystems``
	- [![](https://upload.wikimedia.org/wikipedia/commons/thumb/5/5d/Windows_2000_architecture.svg/275px-Windows_2000_architecture.svg.png)](https://en.wikipedia.org/wiki/File:Windows_2000_architecture.svg)
	- What the properties of this process must be:
		- Parent is system PID 4
		- 1 instance per session
		- path is `Windows\System32`
		- user is local SYSTEM user
-----
- **Client Server Runtime Process** (csrss.exe)
	- user-mode side of the Windows subsystem (win32)
	- always running
	- responsible for the Win32 console window and process thread creation and deletion & making the Windows API available to other processes, mapping drive letters, and handling the Windows shutdown process.
	- For each instance, csrsrv.dll, basesrv.dll, and winsrv.dll are loaded
	- What the properties of this process must be:
		- path is `Windows\System32`
		- Created by an instance of smss.exe
		- two or more instances (one per session)
		- **User Account**:  Local System
		- **Start Time**:  Within seconds of boot time for the first two instances (for Session 0 and 1) keep other sessions start time in consideration
----
- **Windows Initialization Process** (**wininit.exe**)
	- responsible for launching ==`services.exe`== (Service Control Manager), ==`lsass.exe `==(Local Security Authority), and ==`lsaiso.exe`== within Session 0.
	- lsaiso.exe is a process associated with **Credential Guard and Keyguard**, only visible if Credential Guard is enabled.
	- What the properties of this process must be:
		- path is `Windows\System32`
		- Created by an instance of smss.exe
		- 1 instance only
		- Session 0
		- **User Account**:  Local System
		- **Start Time**:  Within seconds of boot time
-----
- **Service Control Manager** (SCM)  (**services.exe**)
	- responsibility is to handle system services: start, stop, interact 
	- maintains a database that can be queried using `sc.exe`
	- Information regarding services is stored in the registry, `HKLM\System\CurrentControlSet\Services`.
	- This process also loads device drivers marked as auto-start into memory.
	- When a user logs on, this process is responsible for setting the value of the Last Known Good control set (Last Known Good Configuration), `HKLM\System\Select\LastKnownGood`
	- parent to several other key processes: ==`svchost.exe`==, ==`spoolsv.exe`==, ==`msmpeng.exe`==, and ==`dllhost.exe`==
	- What the properties of this process must be:
		- path is `Windows\System32`
		- **Parent Process**:  wininit.exe
		- 1 instance only
		- Session 0
		- **User Account**:  Local System
		- **Start Time**:  Within seconds of boot time
---
- **Service Host** (**svchost.exe**)
	- Responsible for hosting and managing Windows services.
	- SCM handles starting and stopping of services  while svchost provide the execution environment (acts like a container for hosting the service along with needed dll)
	-  A system process in Windows that acts as a host for DLLs. It loads and executes the code within those DLLs on behalf of various Windows services.
	- Most of services running in this process are implemented as DLLs. The DLL to execute is stored in the registry for the service under the `Parameters` subkey in `ServiceDLL`. The full path is `HKLM\SYSTEM\CurrentControlSet\Services\SERVICE NAME\Parameters`.
	- can see what services are hosted in process explorer & process hacker.
	- services are grouped in a number of svchost processes,  with the command line being ==``svchost.exe -k %service group%``==
	- Starting with Windows 10 version 1903, systems that have more than 3.5gb ram, services are no longer grouped.
	- Target for malicious actors, as there are many instances running 
	- What the properties of this process must be:
		- path is `Windows\System32`
		- **Parent Process**:  services.exe
		- **User Account**:  SYSTEM, Network Service, Local Service, some instances run as the logged-in user
		- **Start Time**:  Within seconds of boot time, other instances can vary in time
---
- Local Security Authority Subsystem Service (lsass.exe)
	- responsible for enforcing the security policy on the system
	- verifies users logging on to a Windows computer or server, handles password changes, and creates access tokens. It also writes to the Windows Security Log.
	- It creates security tokens for SAM (Security Account Manager), AD (Active Directory), and NETLOGON
	- It uses authentication packages specified in `HKLM\System\CurrentControlSet\Control\Lsa`
	-  Target for malicious actors (why?)
	- What the properties of this process must be:
		- path is `Windows\System32`
		- **Parent Process**: wininit.exe
		- 1 instance
		- **User Account**:  SYSTEM
		- **Start Time**:  Within seconds of boot time
---
- Windows Logon (winlogon.exe)
	- responsible for handling Secure Attention Sequence (ctrl+alt+del) combination
	- responsible for loading the user profile, it loads ==`NTUSER.DAT`== into HKCU -> current user, and ==`userinit.exe`==
	- What the properties of this process must be:
		-  path is `Windows\System32`
		- created by an instance of smss.exe that exits
		- 1 or more instances
		- **User Account**:  SYSTEM
		- **Start Time**:  Within seconds of boot time (for session 1)
		- shell value in registry is explorer.exe
---
- Windows explorer (explorer.exe)
	- Gives access to folder, files, start menu and taskbar
	- Started by userinit.exe that exists
	- Having outbound TCP/IP connections is suspicious 
	- What the properties of this process must be:
		- path is `Windows\explorer.exe`
		-  **Parent Process**:  Created by userinit.exe and exits
		- 1 instance per logged in user
		- User account: logged in user
		- **Start Tim**e:  First instance when the first interactive user logon session begins
---
taskhostw.exe, runtimebroker.exe -> **taskhost.exe** and **taskhostex.exe**

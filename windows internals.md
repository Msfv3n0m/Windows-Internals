# Windows Internals

## Concepts and Tools

### Windows Operating System Versions

![Image](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9780133986471/files/graphics/01tab01.jpg)

![Image](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9780133986471/files/graphics/01tab01a.jpg)

### Foundation Concepts and Terms

#### Windows API

- Windows API refers to both 32-bit and 64-bit programming interfaces
  - Documentation on the Windows API is described in the Windows SDK documentation

#### Component Object Model

- The Component Object Model (COM) was originally created to enable Microsoft Office applications to communicate and exchange data between documents
- COM is based on 2 principles:
  1. Clients communicate with objects
  2. Component implementation is loaded dynamically
- WinRT is an extension of COM to include namesppace hierarchies, consistent naming, and programmatic patterns

#### The .NET Framework![Image](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9780133986471/files/graphics/01tab02.jpg)

Default .NET Framework installations on Windows

![Image](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9780133986471/files/graphics/01fig01.jpg)This diagram shows the relationship between .NET and the Windows OS.

#### Services, Functions, and Routines

| Term                     | Definition                                                                                                                                                                       |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Windows API Function     | documented, callable subroutines in the Windows API                                                                                                                              |
| Native System Calls      | undocumented, callable subroutines in the Windows API                                                                                                                            |
| Kernel Support Functions | subroutines inside Windows that can only be called from kernel mode                                                                                                              |
| Windows Services         | processes started by the Windows service control manager                                                                                                                         |
| DLLs                     | callable subroutines linked together via an executable. The advantage of this is that applications can share DLLs so that only one in-memory copy of the DLL's code is in memory |
| Processes                | a container that stores details about a program's execution. Each process points to its parent                                                                                   |
| Thread                   | an entity wthin a process that Windows schedules                                                                                                                                 |

#### Kernel Mode vs. User Mode

Applications started in user mode can only access a restricted subset of memory locations whereas kernel mode can access all of it

#### Virtualization Based Security

| Setting          | Description                                                            |
| ---------------- | ---------------------------------------------------------------------- |
| Device Guard     | enforces stronger code signing guarentees                              |
| Hyper Guard      | protects key kernel-related code                                       |
| Credential Guard | prevents unauthorized access to domain account credentials and secreds |

Not all data structures in the Windows OS are objects. Only data that needs to be shared, protected, named, or made visible to user-mode programs (via system services) is placed in objects.

#### Security

Core security features of Windows:

- Discretionary protection for all sharable objects
- Security auditing
- User authentication at logon
- Preventing users from accessing uninitialized resources
- Discretionary access control
- Privelaged access control
- Mandatory integrity control

#### Registry

The registry is the system database where all aspects of the system is configured

#### Dependency Lab

Use dependency walker to view exported functions from a Windows DLL

#### Tools for viewing Windows internals

![Image](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9780133986471/files/graphics/01tab04.jpg)

- LiveKd
- WinDbg
- Windows Software Development Kit
- Windows Driver Kit

## System Architecture

### Architecture Overview

![Image](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9780133986471/files/graphics/02fig01.jpg)

**basic** overview of the Windows architecture

![Image](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9780133986471/files/graphics/02tab01.jpg)

Core Windows system files

#### Virtual Trust Levels vs priveleges

- privelege levels (user vs kernel) enforce *power*
- VTLs enforce isolation

### Key system components

![Image](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9780133986471/files/graphics/02fig04.jpg)

#### Environment subsystem and subsystem DLLs

The role of an environment subsystem is to expose some subset of the base Windows executive system services to application programs. Examples of environment subsystems include:

- console-based applications
- graphical user interface (GUI) applications
- 16-bit Windows applications.

When an application calls a function in a subsystem DLL, one of three things can occur:

- the function is entirely implemented in the DLL
- the function requires 1+ calls to the Windows executive
- the function requires work to be done where it must wait for some operation

#### Subsystem startup

Subsystem startup information is stored in `HKLM\SYSTEM\CurrentcontrolSet\Control\SessionManager\Subsystems`

#### Windows subsystem

The Windows subsystem deals with displaying I/O. Naturally, other components of the OS depend on it. Therefore it is a critical process indicating that without it the system will crash

Here are some major responsibilities of the Windows subsystem:

* For each session, an instance of the environment subsystem process (Csrss.exe) loads four DLLs (Basesrv.dll, Winsrv.dll, Sxssrv.dll, and Csrsv.dll) to support the following operations:
  * creating and deleting threads
  * shutting down windows applications
  * containing .ini file to registry location mappings for backward compatibility
  * sending kernel notifications
  * caching functions
* a kernel-mode device driver (win32k.sys) that contains the following:
  * windows manager which controls window displays and screen output
* the console host process (conhost.exe) manages command prompt windows

#### Win32k.sys

* on a full desktop system Win32k.sys loads win32kbase.sys and win32kfull.sys

#### Ntdll.dll

Special DLL used for subsystem DLLs and native applicaions

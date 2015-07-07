---
title: Windows batch script to start VMWare guests
author: James Tang
layout: post
permalink: /notes/windows-batch-script-to-start-vmware-guests/
categories:
  - Notes
tags:
  - batch script
  - CMD
  - VMWare
  - Windows
---
The GUI of VMWare Workstation seemed quite slow and always run into &#8220;not responding&#8221; state when I try to boot a guest. I tried the vmrun command and it works very smoothly, so I wrote a simple batch script which can be used to run a guest automatically on windows start.

<pre class="brush: plain">@echo off

if "%1" == "" (
    set vm_name=X:\\default\\path\\to\\linux.vmx
) else (
    set vm_name=%1
)

vmrun list | findstr /E "%vm_name%" 1>NUL

if errorlevel 1 (
    echo Starting %vm_name% ...
    vmrun -T ws start %vm_name% nogui
) else (
    echo %vm_name% is running
)

pause
</pre>

Windows batch script is rarely used (at least for me) so I make a note here in case I or other guys need such script in the future.

CMD reference: <http://ss64.com/nt/>.
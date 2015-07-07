---
title: Run VMWare guest with command line
author: James Tang
layout: post
permalink: /server/run-vmware-guest-with-command-line/
categories:
  - Server
tags:
  - VMWare
---
WMWare workstation provides a command line utility, called vmrun, to control the guest machines, they are especially useful if GUI is not required.

For example, start a guest instance with following command:

<pre class="brush:bash">vmrun -T ws start /path/to/instance.vmx nogui
</pre>

You can check how many guest instance are running:

<pre class="brush:bash">rmrun list
</pre>

For more information, just hit vmrun on terminal or refer to [Using vmrun to control Vitual Machines][1].

 [1]: https://www.vmware.com/support/developer/vix-api/vix111_vmrun_command.pdf
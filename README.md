# Develop ptlite/ptliteui with ansiblilized Windows 10 box

The title pretty much explains what the purpose of this repo is. The one detail missing in the title is that this is intended to set up a Windows 10 box to be ready to develop the whole ptlite stack.

## History

### First order of business - make machine ansible controlable.
* Being able to sign on to the windows box remotely from linux is not a needed step, I just want to work on the linux box on the dining room table where my wife is working.
1. Install remmina on the linux box
2. Allow remote desktop and sharing
   1. My Computer > Properties > Remote Settings
   2. Allow remote connections to this computer, and click <APPLY>
   3. Change firewall settings (search on main menu)
   4. Allow app through windows firewall
   5. Check *Remote Desktop* **public** and **private**
   6. Note the windows machine ip address using *ipaddress* in **CMD**
3. Start *remmina* on the linux machine and enter the ipaddress
4. Log in with your windows credentials
5. Accept the certificate

### This next bit is needed to control the win machine with ansible
* ensure PowerShell version 3.0 and .NET Framework 4.0 or newer are installed
For finding which versions of .NET freamework are installed, type in a CMD terminal:

```
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP"
```

To find out which version of PowerShell is installed type in a PowerShell terminal:

```
$PSVersionTable
```

Note, you can do other things through PowerShell to find this information but, they are much more convoluted.

* [Setup WinRM](https://docs.ansible.com/ansible/latest/os_guide/windows_setup.html#winrm-listener)
   * after not being successful at a simple *ansible win -i inventory -m win_ping* command, tried this:
   * [ConfigureRemotingForAnsible.ps1](https://github.com/ansible/ansible/blob/devel/examples/scripts/ConfigureRemotingForAnsible.ps1)
   * **However**, this won't run by default. You can check in Powershell (admin mode) > *get-exectionpolicy*
   * I had to do this: *set-exectionpolicy remotesigned*
   * Now on to running the script again ... 
      * in CMD (admin) -> *powershell.exe -File ConfigureRemotingForAnsible.ps1 -CertValidityDays 100*
      * or in Powersell -> 
   * Now this is still not working correctly
      * Tried this in Powershell -> *winrm set winrm/config/service '@{AllowUnencrypted="true"}'* which failed
      * in Powershell -> *Get-NetConnectionProfile*, showing that *NetworkCategroy* was set to *Public*
      * in Powershell -> *Set-NetConnectionProfile -NetworkCategory Private*
      * in PS -> *winrm set winrm/config/service/auth '@{Basic="true"}'*
      * still no go, hmm, in PS, *winrm set winrm/config/client/auth '@{Basic="true"}'*

* The controlling computer must have winrm installed
   * may need to install *pip*
   * then install winrm with *pip install winrm*
* The controlling computer must have ansible windows modules installed
   * *ansible-galaxy collection install ansible.windows

* Test out the most basic set up with authentication (not the most secure though)

Create and *inventory* and fill it with:

```
[win]
<IP ADDR of win machine>

[win:vars]
ansible_user=<win username>
ansible_password=<win passwd>
ansible_connection=winrm
ansible_winrm_server_cert_validation=ignore
```
* Test it out like this -> *ansible win -i inventory -m win_ping*

### Packages to install and configuration needed

### Required manual configuration

## References

1. [ansible docs](https://docs.ansible.com/ansible/latest/os_guide/windows_setup.html)
2. [sign on to windows through linux box](https://opensource.com/article/18/6/linux-remote-desktop)
3. [more on setting up to connect a windows machine using winrm](https://www.ansible.com/blog/connecting-to-a-windows-host)
# Windows setup using ansible

## First order of business - get windows machine ready for ansible.
### Being able to sign on to the windows box remotely from linux is not a needed step.
I just want to work on the linux box on the dining room table where my wife is working.
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

## Then figure out what needs installing

## References

1. [ansible docs](https://docs.ansible.com/ansible/latest/os_guide/windows_setup.html)
2. [sign on to windows through linux box](https://opensource.com/article/18/6/linux-remote-desktop)
# README

This playbook is developed for automatic software distribution on the client machines defined in inventory.yml. To do this task succesfuly, using "Windows Remote Management Service". So the client computers must accept this connection (WinRM:5985). It is used to chocolatey package manager on endpoints to manage softwares and ansible for the remote administration.

## Prepare Windows Machines

Run the following code in Powershell as Administrator:

```powershell
PS C:\Users\user> Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

winrm quickconfig
```

Also the windows firewall must be allowed windows remote management services port.

## Management Station:

In this case, we use windows WSL 2 for the management station. To do this, you can now install everything you need to run Windows Subsystem for Linux (WSL) by entering the following command in an administrator PowerShell or Windows Command Prompt and then restarting your machine;

```powershell
PS C:\Users\user> wsl --install
```

This command will enable the required optional components, download the latest Linux kernel, set WSL 2 (Hyper-V is installed, if you use another virtualisation software you cant use it anymore) as your default, and install a Linux distribution for you (Ubuntu by default, see below to change this).

The first time you launch a newly installed Linux distribution, a console window will open and you'll be asked to wait for files to de-compress and be stored on your machine. All future launches should take less than a second.

Run the following commands in the console of WSL:

```bash
sudo apt-get update
sudo apt-get install ansible
sudo apt-get install python3-pip python3-dev libkrb5-dev krb5-user
sudo pip3 install pywinrm[kerberos]
```

and now we are ready ! You clone this repository into your WSL user's home directory and start to manage your applications deployed entire workstations. 

To add new package to ftpclients group (we can filter the client or client group with --limit parameter);

```bash
ansible-playbook -u <admin user> --ask-pass package-manager.yml --limit ftpclients --extra-vars '{"op": "install", "apps": [filezilla]}'
```

To update existing package to ftpclients group;

```bash
ansible-playbook -u <admin user> --ask-pass package-manager.yml --limit ftpclients --extra-vars '{"op": "update", "apps": [filezilla]}'
```

To remove existing package to ftpclients group;

```bash
ansible-playbook -u <admin user> --ask-pass package-manager.yml --limit ftpclients --extra-vars '{"op": "remove", "apps": [filezilla]}'
```

That is it !

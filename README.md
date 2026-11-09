<p align="center">
<img src="https://i.imgur.com/Clzj7Xs.png" alt="osTicket logo"/>
</p>

# osTicket - Prerequisites and Installation

This project documents how to deploy the open-source help desk system osTicket on a Windows Server virtual machine in Microsoft Azure, using IIS, PHP and MariaDB. It is Part 1 of a three-part series that takes a blank cloud VM to a working help desk.

## Portfolio Project

**Author:** Anthony Maleke

**Contact:** [Linkedin](https://github.com/anthonymaleke/osticket-prereqs.git)

## Video Demonstration

- [YouTube: osTicket Tutorial (Part 1 of 3) - Prerequisites and Installation](https://www.youtube.com/watch?v=REPLACE_ME)

## The Series

| Part | Repository | Topic |
|---|---|---|
| 1 | **osticket-prereqs** (this repo) | Azure VM, IIS, PHP, database, osTicket installation |
| 2 | [post-install-config](https://github.com/anthonymaleke/post-install-config) | Roles, departments, teams, agents, users, SLAs, help topics |
| 3 | [ticket-lifecycle](https://github.com/anthonymaleke/ticket-lifecycle) | A ticket from intake to resolution |

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines, Network Security Groups)
- Remote Desktop Protocol (RDP)
- Internet Information Services (IIS)
- PowerShell and Command Prompt
- PHP running through FastCGI
- MariaDB and HeidiSQL

## Operating Systems Used

- Windows Server 2022 Datacenter (Azure Edition)

## Software Versions

| Component | Version |
|---|---|
| osTicket | Latest 1.18.x stable release |
| PHP | 8.4, Non Thread Safe, x64 |
| MariaDB | 10.11 LTS |
| IIS URL Rewrite Module | 2.1 |
| Visual C++ Redistributable | 2015-2022, x64 |

Versions follow the requirements published in the official osTicket documentation: a web server with URL Rewrite, PHP 8.2 to 8.4, and a MySQL-compatible database.

## List of Prerequisites

- **IIS:** The Windows web server that hosts osTicket. The CGI feature is required so that IIS can run PHP.
- **PHP:** The scripting language osTicket is written in, installed as a FastCGI handler.
- **Visual C++ Redistributable:** Runtime components that PHP for Windows depends on.
- **URL Rewrite Module:** An IIS extension required by osTicket.
- **MariaDB:** The database that stores all tickets, users and settings.
- **HeidiSQL:** A graphical database client used to create the osTicket database.

## Overview

1. Create a Windows Server VM in Azure and restrict network access to your own IP address.
2. Turn the server into a web server by installing IIS.
3. Install the prerequisites: Visual C++ Redistributable, URL Rewrite, PHP, MariaDB and HeidiSQL.
4. Create the database and a dedicated database user.
5. Install osTicket with the web-based installer.
6. Verify the end-user portal and the agent portal.
7. Remove the setup directory and lock down the configuration file.

## Outline

### 1. Create the Azure virtual machine

- Resource group `rg-osticket-lab`, VM `vm-osticket`, Standard_B2s, Windows Server 2022 Datacenter
- Limit RDP (port 3389) and HTTP (port 80) to your own public IP in the network security group
- Enable auto-shutdown to control cost

### 2. Prepare Windows as a web server

- Install the IIS role with the CGI feature using `Install-WindowsFeature`
- Open port 80 in Windows Firewall
- Confirm the default IIS page loads on `http://localhost`

### 3. Install the prerequisite programs

- Install the Visual C++ Redistributable and the URL Rewrite Module
- Extract PHP to `C:\PHP`, create `php.ini` and enable the required extensions
- Register PHP with IIS as a FastCGI handler
- Test with a temporary `phpinfo()` page, then delete it

### 4. Install the database

- Install MariaDB 10.11 with HeidiSQL
- Create the `osticket` database and a dedicated `osticket_user` account with privileges on that database only

### 5. Install osTicket

- Copy the `upload` folder contents to `C:\inetpub\wwwroot\osTicket`
- Create `ost-config.php` from `ost-sampleconfig.php` and temporarily allow the installer to write to it
- Complete the browser-based installer

### 6. Verify

- End users: `http://localhost/osTicket/`
- Agents and admins: `http://localhost/osTicket/scp/login.php`

### 7. Clean up and secure

- Delete the `setup` directory
- Change `ost-config.php` to read-only for the web server accounts
- Confirm the helpdesk status is Online

## Design Decisions

- **Dedicated database user instead of root.** osTicket only needs full privileges on its own database, so the root account is never used by the application.
- **Network access limited to one IP.** RDP and HTTP are not open to the internet.
- **Manual PHP registration with `appcmd`.** Uses built-in Windows tooling and is fully scriptable.
- **MariaDB instead of MySQL 8.0.** MySQL 8.0 reached end of life in April 2026, while MariaDB 10.11 is listed in the osTicket documentation and is still supported.

## Known Limitations

- The lab uses HTTP, not HTTPS. Use test credentials only.
- The PHP IMAP extension is not bundled with PHP 8.4, so fetching email from a mailbox is not configured in this series.


## References

- [osTicket Installation Guide](https://docs.osticket.com/en/latest/Getting%20Started/Installation.html)
- [osTicket Downloads](https://osticket.com/download)
- [PHP for Windows](https://windows.php.net/download)
- [IIS URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite)
- [MariaDB Downloads](https://mariadb.org/download/)

## Next Step

Continue to [Part 2: post-install-config](https://github.com/anthonymaleke/osticket-post-install).

# Networking Environment with Active Directory

1. Download an ISO file for Windows Server 2019 and Windows 10.

2. Download VirtualBox and Extension Pack.

3. Create a virtual machine for your server:
    - New
    - Name
    - Other
    - Allocate 2048MB
    - Create

    *In settings, change shared clipboard/drag and drop to bidirectional for a better user experience. For more speed, increase the processors on the VM.*

    - Network:
        - Adapter 1: NAT
        - Adapter 2: Internal Network
    - Start up:
        - Select your ISO file
        - Choose the desktop experience
        - Custom install
        - Install Server Guest Edition:
            - Run amd64 version
        - Shut down VM
    - Set up network configurations:
        - Ethernet – Internet
        - Ethernet 2 – Internal
            - Assign an IP address manually
            - Properties: IPv4
            - Use these settings:
                - IP address: 172.16.0.1
                - Subnet Mask: 255.255.255.0
                - DNS: 127.0.0.1 (loopback address)

    *No default gateway is needed; the Domain Controller (DC) will serve this purpose with its 2 NICs (Network Interface Controllers).*

    - Rename the PC:
        - System
        - Rename this PC
            - DC = Domain Controller
    - Restart

4. Install Active Directory Domain Services:
    - Configure this local server
    - Add roles and features
    - Active Directory Domain Services
    - Post-deployment configuration
    - Add new forest
    - Name it (e.g., mydomain.com)
    - Set a password

5. Create a domain admin account:
    - Active Directory Users and Computers
    - mydomain.com
        - New
        - Organizational Unit (OU) (folder in AD)
        - New User
        - Name: (e.g., a-manriquez, admin)
    - Properties
    - Member Of:
        - Domain Admins
    - Log in as your admin user

6. Install RAS/NAS:
    *Allows the client to be on the private VM network but still access the internet through the Domain Controller.*

    - Add roles and features
    - Remote Access
    - Routing
        - Tools
        - Routing & Remote Access
        - DC (local)
            - Configure and Enable Routing and Remote Access
            - NAT
            - Use: Internet interface

7. Configure DHCP server on DC:
    *Allows the Windows 10 client to get an IP address and browse the internet while on the private internal network.*

    - Add roles and features
    - DHCP
        - Tools
        - DHCP
        - New Scope:
            - Name: 172.16.0.100-200
            - Start IP address: 172.16.0.10
            - End IP address: 172.16.0.200
            - Subnet Mask: 255.255.255.0
            - Router: 172.16.0.1 (Default Gateway)
                - Add
            - DC: mydomain.com
    - Authorize and Refresh

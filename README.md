# Networking Environment with Active Directory (Simulating a Corporate Network)

1. **Download an ISO file for Windows Server 2019 and Windows 10.**

2. **Download VirtualBox and Extension Pack.**

3. **Create a virtual machine for your server:**
    - New
    - Name
    - Other
    - Allocate 2048MB
    - Create

    *In settings, change shared clipboard/drag and drop to bidirectional for a better user experience. For more speed, increase the processors on the VM.*

    - **Network:**
        - Adapter 1: NAT
        - Adapter 2: Internal Network
    - **Start up:**
        - Select your ISO file
        - Choose the desktop experience
        - Custom install
        - Install Server Guest Edition:
            - Run amd64 version
        - Shut down VM
    - **Set up network configurations:**
        - Ethernet – Internet
        - Ethernet 2 – Internal
            - Assign an IP address manually
            - Properties: IPv4
            - Use these settings:
                - IP address: `172.16.0.1`
                - Subnet Mask: `255.255.255.0`
                - DNS: `127.0.0.1` (loopback address)

    *No default gateway is needed; the Domain Controller (DC) will serve this purpose with its 2 NICs (Network Interface Controllers).*

    - **Rename the PC:**
        - System
        - Rename this PC
            - DC = Domain Controller
    - Restart

4. **Install Active Directory Domain Services:**
    - Configure this local server
    - Add roles and features
    - Active Directory Domain Services
    - Post-deployment configuration
    - Add new forest
    - Name it (e.g., `mydomain.com`)
    - Set a password

5. **Create a domain admin account:**
    - Active Directory Users and Computers
    - `mydomain.com`
        - New
        - Organizational Unit (OU) (folder in AD)
        - New User
        - Name: (e.g., `a-manriquez`, `admin`)
    - Properties
    - Member Of:
        - Domain Admins
    - Log in as your admin user

6. **Install RAS/NAS:**
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

7. **Configure DHCP server on DC:**
    *Allows the Windows 10 client to get an IP address and browse the internet while on the private internal network.*

    - Add roles and features
    - DHCP
        - Tools
        - DHCP
        - New Scope:
            - Name: `172.16.0.100-200`
            - Start IP address: `172.16.0.10`
            - End IP address: `172.16.0.200`
            - Subnet Mask: `255.255.255.0`
            - Router: `172.16.0.1` (Default Gateway)
                - Add
            - DC: `mydomain.com`
    - Authorize and Refresh
    - Download the script to add users using Powershell
    - Note: The default browser of Internet Explorer was outdated, causing the sites to not load. To avoid this, I installed Google Chrome onto the VM. Then went to the file location and downloaded the zip file.
    - *You can also type out the URL provided: `https://gitub.com/joshmadakor1/AD_PS-master`*
    SCREENSHOT
    - Extract the files from the folder, save to desktop
    - Name file = 1000 random names
        - Add your name to the file
        - Save

- **Open Powershell ISE, run as administrator**
    - Open the script
    - Enable execution of Powershell scripts
        - Input: `Set-ExecutionPolicy Unrestricted`
            - Enter
        - Go to the directory of where the script is
            Input: `cd C:\Users\a-mmanriquez\Desktop\AD_PS-master`
    - Press play on the script window
    - Go to Active Directory Users and Computers
        *You will now see a folder named "_USERS" and inside will be the users created by the Powershell script.*

- **Create VM Windows 10, that uses its own NIC**
    - Name: `Client1`
    *If you have enough RAM, give it `4096mb (4GB)`*
    - Go to settings
        - Adjust the processor 
        - Shared Clipboard, and Drag’n’Drop: Bidirectional
        - Network: Adapter 1 to Internal Network 
        *This is to mimic a corporate network environment, where you have users added to the company’s Active Directory. Then you can control and adjust permissions, and enable/disable users as needed.*
    - Open the client
        - Add the ISO of Windows 10
        - Go through configuration, for passkey, click `don’t have one`
        - Use Windows 10 Pro
        - Custom install
        - Connect with no internet
        - Limited features
        - Name: `user`
        - No password

    - **Check the VM has internet connection**
        - Command line
            Input: `ipconfig`
            *There is no default gateway*
    - Go to your Server Manager
        - Tools
        - DHCP
        - Server Options
        - Configure
            - Add `003 Router`
            - IP address: `172.16.0.1`
            - Right click on `dc.mydomain.com`
                - All tools
                - Restart
        SCREENSHOT

    - Go back to Client1
        - Command prompt
        - Input: `ipconfig /renew`
        *You should now have a default gateway*
        Input: `ping www.google.com`
        *If it resolves, then you should have a connection established*
    SCREENSHOT
    - Rename the computer
        - Right click Windows
        - System
            - Rename this computer (advanced)
            - Change…
            - Computer name: `CLIENT1`
            - Member of, Domain: `mydomain.com` (joining the domain)
            - Computer Name/Domain Changes
                - User: `mmanriquez`
                - Password: `password1`

    - **Go back to the DC**
        - DHCP server
            - Select Address Leases
            *You will see your client computer there.*

SCREENSHOT
    - Now go to Active Directory Users and Computers
        - Computers
        *The `CLIENT1` computer will be there as well*
    - Go to the CLIENT1 VM
*As proof of concept, you can log in as any other user from the `names.txt` file with Password: `Password1`*

**Conclusion:** On the `CLIENT1` Virtual Machine, you can now see and use it as if you were an employee on a corporate network. That VM is under the domain controller, and it all routes through the DC. If you wanted to expand on this project, you could apply Group Policies, Firewall, and much more that users would have to abide by.


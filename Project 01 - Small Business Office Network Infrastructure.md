## Project 01 - Small Business Office Network Infrastructure

### 1. Business Scenario

A small company called Sakinah Ventures Enterprise has recently moved into a small office with only 1 level and needs a basic computer network for its daily operations.

* Company requirements:
  
  | Item | Quantity |
  | :---| :---|
  |Employees | 10
  |Desktop computers |  10
  |Network printer | 1 |
  |Network Switch | 1
  |Router | 1 |
  |Internet Connection | 1

* Business requirements:
  1. All 10 employees must be able to communicate with the office network.
  2. All employees must be able to access the internet.
  3. All employees must be able to print documents using one shared network printer.
  4. Each employee must receive an IP address automatically.
  5. The network should be easy to manage and troubleshoot.
  6. The office should have a basic network security configuration.

### 2.Proposed network topology
* Type of topology used : Star topology
  `INSERT NETWORK DIAGRAM HERE`
* The logical network structure :
  `INSERT NETWORK DIAGRAM HERE`

  *All 10 computers and the printer connect directly to the central switch. The router connects to the office LAN to the internet.*

### 3.Hardware and devices in Cisco Packet Tracer
|Device | Packet Tracer device | Quantity
| :--- | :--- | :--- |
|Desktop | PC-PT | 10 |
|Switch | Cisco 2960 | 1 |
|Router | Cisco 2911 | 1 |
|Network Printer | Printer-PT | 1 |
|Internet | Cloud-PT | 1 |


### 4. IP addressing plan
For this company, I use the private network `192.168.10.0/24` . (yes, the typical private IP 😚).

There two type of IP used :
* Static IP : For Printer & Router
* Dynamic IP : Automatically assign throught DHCP

`*Why choose static IP for printer and router ? To keep these devices reachable all the time.*`

|Device | IP address | Assignment |
|:--- |:--- |:--- |
|Router | 192.168.10.1 | Static |
|Printer | 192.168.10.50 | Static |
|PC-01 - PC010 | DHCP | Automatic |

Network Information:
* Network address - 192.168.10.0
* Subnet Mask - 255.255.255.0
* Default gateway - 192.168.10.1
* Broadcast address - 192.168.10.255
* DHCP range (Usable Host ) - 192.168.10.100 - 192.168.10. 149
* Printer IP - 192.168.10.50

```
Why use DHCP ?
```

### 5.Configuration steps

Step 1 - Build the topology in Cisco Packet Tracer:
  1. Add one Cisco 2911 router.
  2. Add one Cisco 2960 switch.
  3. Add 10 PCs.
  4. Add one Printer-PT.
  5. Connect all the PCs to the switch using Copper Straight-through cable.
  6. Connect the printer to the switch using Copper Straight-through cable.
  7. Connect the router to the switch using Copper Straight-through cable.

|Connected Device | Switch Port |
|:--- |:--- |
|PC-01 | Fa0/1 |
|PC-02 | Fa0/2 |
|PC-03 | Fa0/3 |
|PC-04 | Fa0/4 |
|PC-05 | Fa0/5 |
|PC-06 | Fa0/6 |
|PC-07 | Fa0/7 |
|PC-08 | Fa0/8 |
|PC-09 | Fa0/9 |
|PC-10 | Fa0/10 |
|Network Printer | Fa0/11 |
|Router G0/0 | Fa0/10 |


Step 2 - Configure the router
Click the Cisco 2911 router, open CLI and enter:

A. Configure the router interface

```
enable
configure terminal

hostname ABC-ROUTER

interface gigabitEthernet 0/0
description CONNECTION_TO_OFFICE_LAN
ip address 192.168.10.1 255.255.255.0
no shutdown

exit

```

This configures the router's LAN interface.
The router now acts as the *default gateway* for all the office devices.

B. Configure DHCP
```
ip dhcp excluded-address 192.168.10.1 192.168.10.99

ip dhcp pool ABC-OFFICE
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8

exit

```
Commands explaination:

|Command | Explanation |
|:--- |:--- |
|ip dhcp excluded-address | Prevents DHCP from assigning reserved addresses |
|ip dhcp pool | ip dhcp pool |
| network | Defines the network available to DHCP clients |
| default-router | Specifies the default gateway |
| dns-server | Provides a DNS server address to clients |

*I reserve addresses `.1` to `.99` for infrastructure and other static devices.

C. Save the configuration
```
end
write memory
```

The router is now configured to provide DHCP services to office computers.

Step 3 - Configure the network printer.
Click Printer-PT.
Go to `IP Configuration` settings.

Set the following:
IP address : 192.168.10.50
Subnet mask : 255.255.255.0
Default gateway : 192.168.10.1

Step 4 - Configure employee computers
Go to each PC:
1. Click the PC.
2. Open Desktop.
3. Select `IP Configuration`.
4. Select DHCP.

The computer should automatically receive:
- IPv4 address: 192.168.10.100 onwards.
- Subnet mask: 255.255.255.0.
- Default gateway: 192.168.10.1.
- DNS server: 8.8.8.8.
The exact IP allocation depends on the order in which devices request DHCP leases.

Step 5 - Configure the switch
Configure the switch hostname and description to make it easier to manage.

```
enable
configure terminal

hostname ABC-SWITCH

interface range fastEthernet 0/1-10
description EMPLOYEE_COMPUTERS
switchport mode access
exit

interface fastEthernet 0/11
description OFFICE_NETWORK_PRINTER
switchport mode access
exit

interface fastEthernet 0/24
description CONNECTION_TO_ROUTER
exit

end
write memory

```
The switch will automatically learn the MAC addresses of connected devices.

### 6. Testing and verification

Test 1 - Verify IP configuration

On PC-01, open `Command Prompt`, run :
`ipconfig /all`

Check whether the PC received:
* Correct IPv4 address
* Correct subnet mask.
* Correct default gateway.
* Correct DNS server.

Test 2 - Test gateway connectivity
Run :
`ping 192.168.10.1`

Expected result: Successful replies from the router.

Test 3 - Test communication between employees

From PC-01, run :
`ping 192.168.10.101`

*Use the actual IP address assigned to PC-02*
Expected result: Successful communication.

Test 4 - Test printer connectivity

From PC-01, run:
`ping 192.168.10.50`
Expected result: Successful replies from the printer.

Test 5 - Inspect DHCP leases

On the router:
`show ip dhcp binding`

This display IP addresses assigned to DHCP clients.

Test 6 - Inspect switch MAC address table
`show mac address-table`

This allows us to see which devices the switch has learned and the ports on which their MAC addresses were discovered.

### 7. What I learned from this project

By completing this project, I able to understand these technical concepts:

|Technical concept | 	Why it matters in IT |
| :--- | :--- |
|LAN	|Understanding office networks
|Star topology|	Understanding physical network design
|Ethernet switching	|Understanding device communication
|IPv4 addressing|	Configuring network devices
|Subnetting	|Understanding network boundaries
|DHCP	|Managing automatic IP allocation
|Default gateway	|Understanding routing
|DNS|Understanding domain name resolution
|MAC address|	Understanding Layer 2 communication
|Network printer |	Supporting shared office resources
|Ping and troubleshooting |	Diagnosing connectivity problems


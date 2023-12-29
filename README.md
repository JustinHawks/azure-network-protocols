1. Remote into VM1. Download and run Wireshark. Select "Ethernet" and click the shark fin icon to start capturing data packets.

![01_wireshark](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/f7b9275b-fd9c-40b1-84ff-f803ff8c79fa)

2. Wireshark will begin to display all live packet traffic happening on VM1.

![02_alltraffic](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/e79edebd-0bbd-43c5-9544-dde168e04ea8)

3. Type ICMP in the filter above. ICMP stands for Internet Control Message Protocol. This is the protocol that "Ping" uses. For now there should be no ICMP traffic until we attempt to ping VM2.
  
![03_ICMP](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/7efe1be6-bec7-44f5-8013-977978de1c92)

4. We will need VM2's Private IP address which can be found in the azure portal.
   
![04_vm2_privIP](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/a2d5f406-6475-416c-8423-0b9eb631941e)

5. Back in VM1, run Powershell and type "ping" and then the private IP address for VM2. VM1 will send and receive 4 packets with VM2. Now there will be a series of request and replies in Wireshark showing traffic between VM1 and VM2.
   
![05_ping](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/cb0502ff-e498-480e-a114-846e52269969)
![06_ping_wireshark](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/065e30e7-8cf5-4f00-b36d-fb7b07278af5)

6. As an experiment, we can ping google.com. Type "ping www.google.com -4" in powershell and there will now be an Echo request and reply with google. This trafffic will also appear in Wireshark. At the bottom of the Wireshark interface, we can read what was in the packets. It appears that only random arrangements of letters and numbers were sent.

![07_ping_google](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/675f4179-4cf5-4dcd-8697-d9daef68dc73)
![08_ping_google_wireshark](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/98063d03-def7-4d88-9c28-5161e39e0194)
![09_icmp_junkdata](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/7de76723-af5a-4176-81b5-e429313aa90f)

7. To run a perpetual ping, type "ping 10.0.0.6 -t" into powershell. This will initiate non-stop traffic between VM1 and VM2.
   
![10_perpetualping](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/13332dd4-a0ba-4f4d-b876-1934a70baa46)

8. While this is running, we will change the Firewall on VM2 to block ICMP traffic. As a result, the flow of traffic should stop. In Microsoft Azure, search for Network Security Group and click on "VM2-nsg" > inbound security rules > Add: For Protocol select ICMP. For Action, select Deny. Set the priority to 200 which will set it as a high priority in the order of rules. Create a name for this rule and click add.

![11_addinboundrule](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/b7f162b2-24d1-4e6d-994c-0f3d9c848daf)

9. Soon, there weill only be request traffic with "(no response found!)" in the information.
    
![12_noresponsefound](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/ab301928-6058-406c-916d-256050df9e90)

10. Now, let's allow ICMP traffic again. In VM2's inbound security rules, edit the Deny rule to "Allow". Now there should be response traffic again.
    
![13_allowicmp](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/2d50ab17-ef47-4283-9c01-0b772967cf92)

11. We can also filter by SSH trtaffic. Type SSH into the filter. Another way to do this is to type "tcp.port==22" because ssh traffic uses port 22 on the firewall. Now we will "SSH" into VM2 from VM1 to gain access to it's Powershell. To do this open Powershell on VM1 and run the command "ssh (username@VM2-private-IP). In this case I will run "ssh Labuser@10.0.0.6". Enter Yes wwhen asked if you are sure you want to continue connecting. Next, enter the password for VM2 and this will give us access to the Command Line for Ubuntu/Linux running in VM2.
    
![14_ssh_wireshark](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/5f254366-c55f-4b62-85e8-11086e487ffc)
![15_ssh_powershell](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/404d004d-4586-46a9-ad43-7e31229be51b)

12. If we run some linux commands in VM2, we can see the ssh traffic appear in Wireshark running on VM1.

![16_ssh_wireshark_inaction](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/bc69d4c6-c64d-4d01-9dc8-3f14c2cde84f)

13. To close the command line session with VM2, type "exit" and we will be logged out from the connection.

![17_vm2_logout](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/2014bd71-d1ed-4eb6-b90a-1dcd5eeab45d)

14. Now we can filter DNS traffic in wireshark. DHCP stands for Dynamic Host Configuration Protocol and is used for automatically assigning IP address to devices connected to the network. If we type "ipconfig /renew" into powershell, it should reissue the IP address. We should see DHCP traffic appear in Wireshark.

![18_dhcp_traffic](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/b9fd87d2-cc79-48bd-a969-abf0a6b37520)

 15. Next we can filter by DNS traffic. We can also type "udp.port==53" for the same result. In powershell if we can use "nslookup" for google.com we can see the IP address of the server.
![19_dns_wireshark](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/326c8cc5-4bdf-4571-8f87-c74df5708aba)
![20_dns_nslookup](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/80a72821-5f1f-498c-8b52-3b6474339bbf)

16. If we change the filter to RDP, we will now see the live session between our local machine and the virtual machines.
    
![21_rdp](https://github.com/JustinHawks/azure-network-protocols/assets/88342524/60e8dcc3-b742-41be-8108-1a81db0186c4)


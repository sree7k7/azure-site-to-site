# Azure-site-to-site
# Network diagram
![diagram](/pics/NetworkDesign.png)
## Prerequsites
- [terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
- [azure cli](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)

This repo contains two spoke accounts (simulated on-prem network) with respective parameters.
- spoke1 ([spoke1-Vnet.azcli](./On-prem/spoke1-Vnet.azcli))
- spoke2 ([spoke1-Vnet.azcli](./On-prem/spoke1-Vnet.azcli))


1. execute the spokes in terminal (copy and paste):
   -  ./spoke1-Vnet.azcli
   -  ./spoke2-Vnet.azcli

2. From spokes Vnets. **Copy** the public **public ip's** of both VM's. You get as output after execution.
3. These ip's are required for VPN setup
4. execute below cmds: (If you are in root dir: site-to-site-terrafrom)
   - terraform init
   - terraform plan
   - terraform apply

change below parameters acc to your needs or keep default. **Note**: Change the *spoke1_Vm_pip* and *poke2_Vm_pip*
```
  resource_group_location = "centralindia"
  vnet_cidr = "10.6.0.0/16"
  public_subnet_address = "10.6.1.0/24"
  private_subnet_address = "10.6.2.0/24"
  gateway_subnet_address = "10.6.3.0/24"
  # Simulated on-prem details
  spoke1cidr = "10.1.0.0/16" 
  spoke2cidr = "10.2.0.0/16"
  spoke1_Vm_pip = "87.49.45.xxx" 
  spoke2_Vm_pip = "87.49.45.xx"
```

If fails, try to execute: *terraform init -upgrade* on terminal and execute cmd: *terraform apply --auto-approve*



#OnPrem PC
-> Goto on-prem (Local Server e.g: windows server 2022)
-> Goto server manager.
-> Add roles -> Remote Access -> click: next -> next -> tick: DirectAccess and VPN (RAS), Routing -> Install

After installation the Roles -> click on Flag (On top right corner) -> Open the Getting Started Wizard -> Choose: Deploy VPN only

![](/pics/Routing-and-Remote-Access.png)

-> Configure and Enable Routing and Remote Access
-> next -> Choose: Custom configuration -> select: Demand-dial connections (used for branch office routing), LAN routing, VPN access.
![](/pics/Demand-dial-connections.png)

Finish -> start service

-> click: computerName as shown in below pic.
![](/pics/Demand-dail-Interface.png)


-> add: New Demand-dial interface
-> Interface name: Azure -> connection Type: Connect using virtual private networking (VPN) -> VPN Type: IKEv2 -> Destination Address: Virtual Network Gateway Public IP address

![](/pics/AzureInterface.png)
-> Next
![](/pics/DestinationAddress.png)

-> In Protocols and Security: Route IP packets on this interface -> Next -> Static Routes for Remote Networks -> click: add -> Destination: 10.0.0.0/16 (i,e cloud cidr), Network Mask: 255.255.0.0 -> Metric: 16
![](/pics/StaticRouteForRemoteNetworks.png)


->Dail-Out Credentials (Optional) -> Finish
-> Select: Azure Network Interface -> Go to properties -> click security -> choose: Use preshared key for authentication -> type: keyname (e.g: abc 123 (this key is from connections in Virtual Network Gateway))

![](/pics/AzureProperties.png)

![](/pics/connect.png)
-> Check the status in Azure: Connections under Virtual Network Gateway
Goto -> Virtuanl Network Gateway (VPN Gateway) -> On left side click: connections

![](/pics/VPNGW-connection.png)

The update will take sometime.

Links: 
- [https://learn.microsoft.com/en-us/azure/vpn-gateway/tutorial-create-gateway-portal](https://learn.microsoft.com/en-us/azure/vpn-gateway/tutorial-create-gateway-portal)


- [https://learn.microsoft.com/en-us/azure/vpn-gateway/tutorial-site-to-site-portal](https://learn.microsoft.com/en-us/azure/vpn-gateway/tutorial-site-to-site-portal)
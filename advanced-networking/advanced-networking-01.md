1. Virtual network for payment processing department
- On-prem range: 10.128.0.1 - 10.128.255.254
- Payment vnet: 10.0.0.0 - 10.0.255.255
	- Web server subnet: 10.0.1.0 - 10.0.1.254
	- App server subnet 10.0.2.0 - 10.0.2.254

![[Pasted image 20221220222007.png]]

2. Virtual network for finance department.
 - Finance vnet: 10.1.0.0 - 10.1.255.255
	- Finance subnet: 10.1.0.0 - 10.1.0.254

![[Pasted image 20221220222700.png]]

## Challange 3

1. Deploy the virtual machine scale set and configure scaling
	- Maximum instances of 3 (Increase by 1)
	- Minimum instances of 1 (De-crease by 2)
	- CPU treshold at 75%
2. Deploy application gateway
	- Create new subnet for the app gw
	- Create the rout table to route between the front end (app gw subnet) and backend (vmss subnet).
	- Create a self signed certificate and uploaded it azure key vault
	- Create two listener rules
		- One rule for HTTP
		- Another rule for SSL (HTTPS). Use the created certificate.
3. Deploy the applicationg gateway
4. Once the app gw has been deployed, upgrade all the vmss instances for changes to take effect.

## Challange 4

1. Create a new on-prem vnet to be connected with azure
	-  On-prem vnet: 10.128.0.0/9
		- Subnet 01: 10.128.0.0/24
2. Create secure connection between On-Prem and Azure resources.
	1. Use Azure VPN Gateway for this.
		- Azure VPN gateway is a service that uses a specific type of VPN to send encrypted traffic. It can be used between on-prem and azure, or between azure vnets to route across MS network. Multiple connections can be made to the same gateway that all share the same bandwidth. Azure VPN gatway uses a special subnet called gateway subnet.
		- https://learn.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways
	2. Create a S2S connection between on-prem and azure vpn gateway.
	3. Create vnet-to-vnet connection between existing vnets and azure vpn gateway vnet.
3. Enable BGP in Azure VPN Gateway vnet.
	- BGP is the standard routing protocol to exchange routing between two or more networks. In Azure, BGP enables the azure vpn gateway and on-prem vpn devices to exchange routes that will inform both gateways on the availability and reachability for those prefixes to go thorugh the gateways or routers involved.
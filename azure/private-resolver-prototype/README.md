# Azure Private Resolver Prototype

<table style="width: 100%; border-style: none;"><tr>
<td style="width: 140px; text-align: center;"><a href="https://learn.microsoft.com/en-us/azure/dns/dns-private-resolver-overview"><img width="130px" src="./docs/images/dns-private-resolver-logo.png" alt="Azure Private Resolver logo"/></a></td>
<td>
<strong>Prototype for DNS name resolution using the Azure Private Resolver</strong><br />
<i>Terraform scripts to build an Azure prototype</i><br />
</td>
</tr></table>

The Azure Private Resolver is a feature in Azure DNS that allows you to resolve DNS names in a virtual network without the need to configure a custom DNS servers. 

Use the Azure Private Resolver to:
- Resolve DNS names to on-premise resources from Azure 
- Resolve DNS names to a Azure resources from a on-premise network

We implement Azure Private Resolver so we don't have to use host name files to map the IP addresses to DNS names. Host files are difficult to maintain and do not scale well.

## Prototype

The Terraform scripts will create 3 virtual networks
- **On-premise network** - An Azure virtual network that simulates an on-premise network. Our real on-premise network is connected to Azure through ExpressRoute. This virtual network has a:
  - **DNS Server** - An Azure virtual machine with a Windows Server 2019 DNS server. The DNS server will resolve DNS names for on-premise resources and forward DNS queries for Azure resources to the Azure Private Resolver Inbound subnet.
  - **VM** - An Azure virtual machine for testing DNS name resolution.

- **Hub network** - An Azure virtual network that is the Azure Hub in our Hub and Spoke architecture. This virtual network has a:
    - **Private Resolver** - An Azure Private Resolver that is used to resolve DNS queries across Azure and on-premise
    - **Private Resolver Inbound subnet** - An Azure subnet that is used to forward DNS queries from the on-premise DNS server to the Azure Private Resolver.
    - **Private Resolver Outbound subnet** - An Azure subnet that is used to forward DNS queries from the Azure Private Resolver to the on-premise DNS server.
    - **Private Resolver endpoint rule-set** - An Azure Private Resolver endpoint rule-set is attached to forward DNS queries for on-premise resources to the Azure Private Resolver.
    - **Private DNS Zone** - An Azure Private DNS Zone is attached to the Hub VNet to resolve DNS queries for Azure resources.
    - **VM** - An Azure virtual machine for testing DNS name resolution.
    

- **Spoke network** - An Azure virtual network that represents an Azure spoke in a Hub and Spoke architecture. This virtual network has the 
    - **Azure Storage account**- an Azure Storage account with a single table used to verify DNS name resolution works to Azure PaaS services.
    - **VM** - An Azure virtual machine for testing DNS name resolution.

![Alt text](docs/images/private-resolver-prototype.svg)

The on-premise and the spoke virtual network are peered to the hub network. The Hub and Spoke virtual networks also have the DNS Forwarding ruleset attached to allow the on-premise DNS queries to be send to the on-premise DNS server. 

## Features

The Terraform script will create everything in the architecture diagram plus:
- Support for SSH to connect to each virtual machine. 
  - SSH keys are generated in the test folder
  - Scripts to SSH to each VM for manual testing
  - Scripts to automatically connect to each VM and test DNS name resolution
- Support for RDP to connect to the Windows 2019 DNS server




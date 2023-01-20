---
lab:
    title: 'Lab 9 - Troubleshoot site-to-site VPN gateway connectivity'
    module: 'Troubleshoot VPN gateways in Microsoft Azure'
---

An Azure subscription is provided for this lab, see the credentials above. If you're interested in completing this lab using your own Azure subscription, sign up for a free trial at <https://azure.microsoft.com/free/>.

# Troubleshoot site-to-site VPN gateway connectivity

**Estimated Time: 20 minutes**

As your organization's support engineer, you've been asked to help fix an issue between your resources in the US and northern Europe. you've existing infrastructure of virtual networks in two different regions. The VMs in the US virtual network (VNet1) are unable to get a ping response from the VMs in northern Europe (VNet2).

Checking the topology, you can see that there are VPN gateways and connections.

![Diagram of the topology of the network that needs troubleshooting."](../media/5-network-diagram.png)

In this lab, you'll troubleshoot and resolve the connectivity issue.

## Test the connection

We're going to test the connection between the two VMs, by sending a ping request between them.

1. Sign in to the [Azure portal](https://portal.azure.com) using the credentials above, or if you'd like your own Azure subscription.

1. In the search bar, type **virtual machines** then, under Services, select **Virtual machines**.

   ![Screenshot showing the search bar and the results of searching for virtual machines.](../media/5-virtual-machines.png)

1. From the list of VMs, select **VM1**.

   ![Screenshot showing the two virtual machines.](../media/5-two-virtual-machines.png)

1. Make a note of the **Public IP address** and **Private IP address**.

   ![Screenshot of V M 1 showing networking the private and public IP addresses."](../media/5-network-ip-address.png)

1. Repeat the last two steps for **VM2** and note the Public IP address and Private IP address.

1. Use the Azure Cloud Shell and SSH to connect to VM1 using the public IP address:

    ![Screenshot showing how to use the cloud shell.](../media/mod9-cloud-shell.png)

   ```azurecli

   ssh azureuser@<VM1 public IP address>;

   ```
   > [!NOTE]
   > Replace \<VM1 public IP address\> with the public IP address you noted for VM1.

1. At the prompt, ` Are you sure you want to continue connecting (yes/no)? ` type yes.

1. At the prompt for a password, type **azur3Pa55w.rd**.

1. Your prompt should change to `azureuser@VM1:~$`.

1. This means you've successfully connected to **VM1**.

1. Ping the private IP address of **VM2**.

   ```bash
   ping <private IP address VM2>

   ```

   > [!NOTE]
   > Replace \<private IP address\> VM2 with the private IP address you noted for VM2. 

   ![Screenshot showing the ping command to check whether machines can connect.](../media/5-ping-request.png)

1. We can confirm that the two machines can't connect, as there is no response from **VM2**.

1. Press **CTRL** + **C** keys to quit the ping command.

## Troubleshoot the gateways

You'll check the types are correct for both gateways.

1. On the Azure portal, in the search bar, type **virtual network gateways**, and then select the service to continue.

   ![Screenshot showing the virtual gateway service.](../media/5-virtual-network-gateways.png)

1. Select **VNet1GW**.

   ![Screenshot of the virtual gateways.](../media/5-virtual-network-gateways-result.png)

1. Confirm that the VPN type is route-based, and the gateway type is VPN.

   ![Screenshot showing the Gateway and V P N Type."](../media/5-vpn-route-based.png)

1. Scroll down the page to check the tunnel Ingress and Egress. Can you see a time when something might have happened to cause a problem?

   ![Screenshot of the Tunnel ingress and Egress stats.](../media/5-tunnel-ingress.png)

1. Repeat for VNet2GW.

## Troubleshoot the virtual networks

You'll now check the address spaces don't overlap for the two virtual networks.

1. In the search bar, type **virtual networks**, and then select the **Virtual network** service.

1. Select **VNet1**.

   ![Screenshot showing the virtual networks.](../media/5-virtual-networks.png)

1. Make a note of the **Address space**.

   ![Screenshot showing the address spacer.](../media/5-address-space.png)

1. Select **VNet2**, and check that the address spaces do not overlap.

   ![Screenshot showing the address space of V Net 2.](../media/5-dns-server.png)

1. The two address spaces are different, so we can rule out any problems with them.

1. You'll now check the subnets are correctly set up.

1. Select **VNet1**, then select **Subnets**.

   ![Screenshot showing the subnet menu.](../media/5-subnets.png)

1. Check the **subnet address** is a subset of the **address space**.

1. Repeat for **VNet2**.

   ![Screenshot showing the Gateway subnet address.](../media/5-gateway-subnet.png)

1. The **GatewaySubnet** addresses have been correctly created and correspond with the default range.

## Check the gateway connections

1. In the search bar, type virtual network gateway and then select **virtual network gateways**.

1. The two gateways will be displayed.

1. Select **VNet1GW**.

   ![Screenshot of the V Net 1 gateway.](../media/5-gateway-connections.png)

1. Select **Connections**.

   ![Screenshot of the Connections option.](../media/5-virtual-network-gateway-connection.png)

1. The issue seems to be with the connections between the gateways.

   ![Screenshot showing the two virtual networks not connected.](../media/5-virtual-network-to-virtual-network.png)

1. Select **Refresh** to check that there is still an issue with connection.

   ![Screenshot of the refresh button.](../media/5-virtual-network-to-virtual-network-connection.png)

1. A connection still can't be made, so you'll check the shared keys.

1. Select **VNet1-VNet2**.

1. Select **Shared key**.

   :::row:::
       :::column:::
       :::column-end:::
       :::column:::
           (../media/5-shared-key.png)![Screenshot showing the Shared Key option.]
       :::column-end:::
       :::column:::
       :::column-end:::
   :::row-end:::

1. Make a note of the **Shared key**.

   ![Screenshot of the shared key.](../media/5-correct-shared-key.png)

1. On the breadcrumb trail, select **VNet1GW**, then select **VNet2-VNet1**.

1. Select **Shared key**.

   ![Screenshot of the second shared key showing it's different to the first shared key.](../media/5-incorrect-key.png)

1. The shared keys are not the same. For the connections to work, the shared key must be identical.

## Resolve the VM connection issues

You've explored the possible reasons why your two machines can't connect to each other. You found that the shared key for the two connections is different.

You'll change the shared key on a VPN gateway connection to resolve the issue.

## Change a gateway connection's shared key

Follow these steps to check the shared key for both the VPN gateways connections:

1. In the Azure portal, search for **virtual network gateways**, then under **Services**, select **Virtual network gateways**.

   ![Screenshot showing virtual gateway search.](../media/6-search-gateways.png)

1. Select the first VPN gateway, **Vnet1GW**.

1. Under Settings, select **Connections**.

   ![Screenshot showing all v net to v net connections.](../media/6-gateway-connections.png)

1. Select the first connection, **VNet1-VNet2**.

1. Under Settings, select **Shared key**.

   ![Screenshot showing the first shared key.](../media/6-copy-shared-key.png)

1. Copy the text in the **Shared key (PSK)**.

1. In the breadcrumb, select **VNet1GW** to return the VPN gateway pane.

1. Select the second connection, **VNet2-VNet1**.

   ![Screenshot showing the second vnet to vnet connection.](../media/6-gateway-second-connection.png)

1. Under Settings, select **Shared key**.

1. Paste the value you copied into the **Shared key (PSK)**. This is to make sure that the values match.

## Test the issue is resolved

Repeat the steps you did in the previous exercise to see if the two VMs can connect to each other. Using the Cloud Shell on the right and the Azure portal, follow these steps:

1. Open the Azure portal in a new tab.

1. Search for **virtual machines** then, under Services, select **Virtual machines**.

   ![Screenshot showing search screen for virtual machines.](../media/6-search-virtual-machines.png)

1. From the list of VMs, select **VM1**.

   ![Screenshot showing the I P address of the first virtual machine.](../media/6-virtual-machine-1-ip-addresses.png)

1. Make a note of the Public IP address and Private IP address.

1. Repeat the last two steps for VM2 and note the Public IP address and Private IP address.

1. On the right, in the Cloud Shell, connect to VM1 with SSH to the public IP address:

   ```azurecli

   ssh azureuser@<VM1 public IP address>

   ```

   > [!NOTE]
   > Replace &lt;VM1 public IP address&gt; with the public IP address you noted for VM1.

1. At the prompt for a password, type **azur3Pa55w.rd**.

1. Your prompt should now be `azureuser@VM1:~$`.

1. This means you have successfully connected to VM1.

1. Ping the private IP address of VM2.

   ```

   ping <private IP address VM2>

   ```

   > [!NOTE]
   > Replace &lt;private IP address VM2&gt; with the private IP address you noted for VM2.

   ![Screenshot showing the ping command working.](../media/6-ping-virtual-machine-2.png)

1. If you have resolved the connection issue, you should see a response from VM2.

# Troubleshoot name resolution issues in Microsoft Azure

**Estimated Time: 15 minutes**

You work for Contoso as a network engineer, and users are complaining that they cannot access VM1 or VM2. You have configured two Azure virtual networks: VNet1 and VNet2. They are connected with peering.

| **Virtual network**| **IPv4 network address**| **Subnet**| **IPv4 network address**|
| :--- | :--- | :--- | :--- |
| VNet1| 10.1.0.0/16| Subnet1| 10.1.1.0/24|
||| Subnet2| 10.1.2.0/24|
| VNet2| 10.2.0.0/16| Default| 10.2.0.0/24|

![Screenshot showing the topology of v net 1.](../media/5-network-1.png)

![Screenshot showing the topology of v net 2.](../media/5-network-2.png)

| **Virtual machine**| **Operating system**| **VNet and subnet**| **DNS domain**|
| :--- | :--- | :--- | :--- |
| VM1| Windows Server 2019| VNet1, Subnet1| contoso.com|
| VM2| Windows Server 2019| VNet1, Subnet2| contoso.com|
| VM3| Windows Server 2019| VNet2, default| contoso.com|

## Task 1: Diagnose networking issues between virtual machines

You'll connect to VM1-<inject key="Deployment ID" enableCopy="false" /> and VM3-<inject key="Deployment ID" enableCopy="false" /> using Azure Bastion and perform some networking checks.

1.  From the portal menu on the top left, select **Resource groups**.

     ![Screenshot of the resource visualizer showing the Vnet resources.](../media/mod3-rg.png)
    
1. Under Resource groups, select **lab02-rg-<inject key="Deployment ID" enableCopy="false" />**.

    ![](../media/mod2-lab02rg1.png)
    
1. Now, search for **vm1 (1)** and select the virtual machine named **VM1-<inject key="Deployment ID" enableCopy="false" /> (2)**.

    ![Screenshot showing the stopped vm in the Azure portal.](../media/mod2-vm1rg.png)

1. On the **Overview** pane, select **Connect (1)**, then select **Bastion (2)**.

   ![Screenshot showing the stopped vm in the Azure portal.](../media/mod2-bastion.png)

1. Select **Create Azure Bastion using defaults**.

   ![Screenshot showing the stopped vm in the Azure portal.](../media/mod2-default.png)

    >**Note**: It may take a 10-15 minutes to create the Bastion service. When it's created enter the below details.

1. Under **Connection Settings**, enter the following details and click on **Connect (3)**, a new tab in your browser should connect to **VM1-<inject key="Deployment ID" enableCopy="false" />**
 
     Username: `AdminXyz` (1).
     
     Password: `Azur$Pa55w0rd` (2).

    ![Screenshot showing selecting option 15.](../media/mod2-connect.png)

1. In the command window, enter **15**.

    ![Screenshot showing selecting option 15.](../media/mod2-connect1.png)

1. Run this powershell command:

    ```powershell
    nslookup VM1-DID.contoso.com
    ```

1. Run this powershell command:

    ```powershell
    nslookup VM2-DID.contoso.com
    ```

1. Run this powershell command:

    ```powershell
    nslookup VM3-DID.contoso.com
    ```
    
    >**Note**: Replace **DID** with the Deployment Id from your Environment details page.

    ![Screenshot showing the results of n s lookup in the command prompt." lightbox="../media/5-nslookup.png](../media/mod2-powershellcommands.png)

    The last command is unable to connect to **VM3-<inject key="Deployment ID" enableCopy="false" />.contoso.com**.

1. Switch back to the tab with the Azure portal, in the menu on the top left, select **Virtual machines**, search for **VM3 (1)** and then select **VM3-<inject            key="Deployment ID" enableCopy="false" /> (2)**.

   ![Screenshot showing selecting option 15.](../media/mod2-vm3.png)

1. On the **Overview** pane, select **Connect**, then select **Bastion**.

1. Under **Connection Settings**, enter the following details and click on **Connect (3)**, a new tab in your browser should connect to **VM3-<inject key="Deployment ID" enableCopy="false" />**
 
     Username: `AdminXyz` (1).
     
     Password: `Azur$Pa55w0rd` (2).

    ![Screenshot showing selecting option 15.](../media/mod2-connect.png)

1. In the command window, enter **15**.

   ![Screenshot showing selecting option 15.](../media/mod2-connect2.png)

1. Run this powershell command:

    ```powershell
    nslookup VM1-DID.contoso.com
    ```

1. Run this powershell command:

    ```powershell
    nslookup VM2-DID.contoso.com
    ```

1. Run this powershell command:

    ```powershell
    nslookup VM3-DID.contoso.com
    ```
    
    >**Note**: Replace $DID with the Deployment Id from your Environment details page.

    ![Screenshot of results of running n s lookup in the command prompt on vm3." lightbox="../media/5-nslookup-3.png](../media/mod2-powershellcommands1.png)

    >**Note**: VM3-<inject key="Deployment ID" enableCopy="false" /> is unable to find any of the domains. 

### Task 2: Examine the Internet Protocol configuration of the Virtual Machines

The internal IP addresses of the virtual machines are:

- VM1 = 10.1.1.4

- VM2 = 10.1.2.4

- VM3 = 10.2.0.4

The DNS server address is 168.63.129.16, which is the wire server.

![Screenshot of command prompt results after running i p config / all." lightbox="../media/5-network-configure.png](../media/mod2-ip.png)

### Task 3: Test network connectivity

Use **ping** to test network connectivity between the three virtual machines. Use the open Bastion tab for virtual machine VM1 try and try to ping the other two machines.

1. In the command window, run this command.

    ```powershell
    ping 10.1.2.4
    ```
1. Run this command.

    ```powershell
    ping 10.2.0.4
    ```

    If you ran these commands on the other machines you'd see that all three VMs are able to ping each other, so network connectivity is good at the IP level (OSI Layer 3).

    ![Screenshot of command screen after pinging for network connectivity." lightbox="../media/5-ping-connectivity.png](../media/mod2-pingconnection.png)

### Task 4: Examine the Azure resource group

There are two virtual networks (VNets) called **VNet1** and **VNet2**.

There is a private DNS zone, which is **contoso.com**.

   ![Screenshot showing the resources within the resource group." lightbox="../media/5-resource-group.png](../media/mod2-venets1.png)

The private DNS zone has VM1-<inject key="Deployment ID" enableCopy="false" /> and VM2-<inject key="Deployment ID" enableCopy="false" /> automatically registered, but VM3-<inject key="Deployment ID" enableCopy="false" /> does not appear.

   ![Screenshot showing that that vm 3 is not appearing." lightbox="../media/5-no-machine-3.png](../media/mod2-contoso.png)

Switch back to the Azure portal tab. 

1. Search for private dns zones, then select **contoso.com**.

   ![Screenshot showing that that vm 3 is not appearing." lightbox="../media/5-no-machine-3.png](../media/mod2-contoso1.png)

1. Under **Settings**, select **Virtual network links**.

    ![Screenshot showing the virtual links." lightbox="../media/5-virtual-network.png](../media/mod2-vnetlink1.png)

    Note that the private DNS zone is linked to **VNet1**, but not to **VNet2**.

## Task 5: Resolve the connection issue

Your investigation points to a configuration issue with the private DNS zone.

### Link the private Domain Name System zone to Virtual Network 2

1. On the Virtual network links pane, select **+ Add** and enter these details:

    - Link name: **vnet2_dns (1)**
    
    - [ ] I know the resource ID of virtual network – leave unchecked (2)
    
    - Subscription: &lt;Select the name of your subscription&gt; (3)
    
    - Virtual network: VNet2 (lab02-rg-<inject key="Deployment ID" enableCopy="false" /> 4))
    
    - Configuration: [&#10003;] Enable auto registration (5)
   
      Select **OK (6)**.
      
      ![Screenshot showing the d n s table." lightbox="../media/5-table.png](../media/mod2-vnet2.png)

    >**Note**: It may take a few minutes for the link to be created. Select Refresh occasionally to see the latest status. Wait until the link status says Completed.

### Inspect the Domain Name System name table

Navigate to the Overview page and inspect the DNS name table.

VM1-<inject key="Deployment ID" enableCopy="false" />, VM2-<inject key="Deployment ID" enableCopy="false" />, and VM3-<inject key="Deployment ID" enableCopy="false" /> should appear. You may need to wait a short while for VM3 to appear. Select Refresh if necessary.

Nslookup on VM1-<inject key="Deployment ID" enableCopy="false" /> and VM2-<inject key="Deployment ID" enableCopy="false" /> should resolve VM3-<inject key="Deployment ID" enableCopy="false" />.contoso.com.

>**Note**: If VM3-<inject key="Deployment ID" enableCopy="false" /> does not appear after several minutes, try restarting the VM.

![Screenshot showing the d n s table." lightbox="../media/5-table.png](../media/mod2-vm3dns.png)

1. Switch back to the VM1 tab in your browser. 

1. Run this powershell command:

    ```powershell
    nslookup  VM1-DID.contoso.com
    ```

1. Run this powershell command:

    ```powershell
    nslookup  VM2-DID.contoso.com
    ```

1. Run this powershell command:

    ```powershell
    nslookup  VM3-DID.contoso.com
    ```
    
    >**Note**: Replace **DID** with the Deployment Id from your Environment details page.

   ![Screenshot showing the results of running the n s lookup commands." lightbox="../media/5-command-prompt-machine.png](../media/mod2-powershellcommands2.png)

Optionally, you can test pinging each VM, using their DNS names.

- `ping  VM1-<inject key="Deployment ID" enableCopy="false" />.contoso.com`

- `ping  VM2-<inject key="Deployment ID" enableCopy="false" />.contoso.com`

- `ping  VM3-<inject key="Deployment ID" enableCopy="false" />.contoso.com`


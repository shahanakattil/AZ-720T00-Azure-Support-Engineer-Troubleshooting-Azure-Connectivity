---
lab:
    title: 'Lab 4 - Troubleshoot virtual machine connections'
    module: 'Troubleshoot platform-as-a-service issues in Microsoft Azure'
---

An Azure sandbox subscription is provided for this lab. You can use the following Azure subscription credentials to complete this lab:

| | |
|---:|:---|
| Email Address | +++@lab.CloudPortalCredential(User1).Username+++ |
| Password | +++@lab.CloudPortalCredential(User1).Password+++ |

> [!tip] If you're interested in completing this lab using your own Azure subscription, sign up for a free trial at <https://azure.microsoft.com/free/>.

# Troubleshoot virtual machine connections

In this lab you'll troubleshoot connection issues to a virtual machines running in Azure.

## Check a virtual machines settings

You need to find the causes of connectivity problems to an Azure virtual machine using SSH.

1. Sign in to the [Azure portal](https://portal.azure.com) using the credentials above, or if you'd like your own Azure subscription.

1. In the resource menu, select **All Resources**.

1. Select **LabClientVM**.

1. On the top row of the **LabClientVM Overview** page, select **Connect** and select **SSH**.

   :::image type="content" source="../media/7-exercise-1-connect.png" alt-text="screen shot showing the connect option for a virtual machine.":::

1. Notice that there is a **Test your connection** link. This provides a troubleshooter to test connections between resources, but this functionality is not available in this sandbox and we will view the settings manually.
  
   :::image type="content" source="../media/7-exercise-1-test-connection.png" alt-text="Screen shot showing the test your connection option.":::

1. Select **Networking**.

1. In the **Network security group** section, notice that there is a rule at the top of the priority list that will allow TCP connections on port 22.

    :::image type="content" source="../media/7-exercise-1-network-rule-allow.png" alt-text="Screenshot of rule to allow SSH" lightbox="../media/7-exercise-1-network-rule-allow.png":::

1. At the top of the screen, select **All resources**.

1. Select **LabVM**.
1. Select **Networking**.

1. In the **Network security group** section, notice that there is not a rule at the top of the priority list that will allow TCP connections on port 22.

    :::image type="content" source="../media/7-exercise-1-network-rule-deny.png" alt-text="Screenshot showing that there is no rule to allow SSH connections." lightbox="../media/7-exercise-1-network-rule-deny.png":::

1. Notice that, except for load balancers and virtual networks, the highest priority rule will deny all inbound traffic. This will cause connectivity problems.

## Resolve the connection issues

Follow these steps in the Azure portal:

1. Select **Home** to return to the Azure portal home screen.

1. In the resource menu, select All **Resources**.

1. Select **LabVM**.

1. In Settings, **select Networking**.

   :::image type="content" source="../media/8-exercise-1-settings-networking.png" alt-text="Screen shot showing the networking option.":::

1. Select **Add inbound port rule**.

   :::image type="content" source="../media/8-exercise-1-add-inbound-port-rule.png" alt-text="Screen shot showing the add inbound rule button.":::

1. On the Add inbound security rule tab, type or select the following values:

   1. **Destination port ranges: 22**

   1. **Action: Allow**

   1. **Priority: 100**

   1. **Name: SSH_port_22**

1. Select **Add**.

1. Wait until the security rule has been deployed.

1. In **Settings**, select **Connect**.

1. Select **Test your connection**.

   :::image type="content" source="../media/8-exercise-1-test-connection-button.png" alt-text="screen shot showing the test connection button.":::

1. Select **Test connection**.

1. Notice that connectivity is now allowed and you've resolved the connectivity issue.

   :::image type="content" source="../media/8-exercise-1-network-connectivity-allowed.png" alt-text="Screen shot showing that connectivity is now allowed.":::


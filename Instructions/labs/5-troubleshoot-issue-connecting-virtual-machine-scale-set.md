---
lab:
    title: 'Lab 6 - Troubleshoot issue connecting virtual machine scale set'
    module: 'Troubleshoot network security issues with Microsoft Azure'
---

An Azure subscription is provided for this lab, see the credentials above. If you're interested in completing this lab using your own Azure subscription, sign up for a free trial at <https://azure.microsoft.com/free/>.

# Troubleshoot virtual machine connections

**Estimated Time: 10 minutes**

You work as a support engineer supporting Azure infrastructure. You've been contacted by your web team about an issue with the website not responding. The web team have a pool of webservers behind a load balancer and public IP address.

![Screenshot of a network topology diagram showing a pool of webservers behind a load balancer](../media/6-topology.png)

In this lab, you'll use what you've learned to go through steps to troubleshoot the connection issues to the virtual machines.

## Verify that the website can't be reached

1. Sign in to the [Azure portal](https://portal.azure.com) using the credentials above, or if you'd like your own Azure subscription.

1. Use this Azure CLI command in the Cloud Shell to get the public IP address of the scale set.

    ![](../media/mod6-cloudshell.png)

1. Select **Bash**, then select **Create storage**.
1. In the cloud shell run this command:

    ```
    az network public-ip show \
    --resource-group lab6rg \
    --name webPublicIP \
    --query '[ipAddress]' \
    --output tsv
    ```

2. Copy the IP address, in a new tab in your browser, try to navigate to it.


    ![Screenshot of the website not responding.](../media/6-website-not-responding.png)

    > [!note] You're IP address will be different to the one in the above screenshot.

### Check that Network Security Groups are configured correctly

1. In the Azure portal search for **Network security groups**.

   ![A screenshot showing the virtual machine scale set is detailed." lightbox="../media/6-search-nsg.png](../media/6-search-nsg.png)

1. Under **Services**, select **Network Security Groups**.

    ![A screenshot showing that in this environment there are no NSGs created." lightbox="../media/6-no-network-security-groups.png](../media/6-no-network-security-groups.png)

1. Select **webNetworkSecurityGroup**.

1. Check that internet traffic over port **80** is allowed by the Network Security Group.

    ![Screenshot of the Network Security Group inbound rules are correct.](../media/6-check-inbound-traffic-allowed.png)

### Check the network settings for the virtual machines

1. On the left, select **Virtual Machines**.

   ![A screenshot showing an instance in the scale set highlighted." lightbox="../media/6-check-virtual-machine-networking.png](../media/6-check-virtual-machine-networking.png)

1. Select the first virtual machine listed, in the above example this is **webVirtualMachine1**. In your environment this could be different.

1. On the left, under **Settings**, select **Networking**.

1. Note that port 80 is allowed.

1. Repeat these steps for **webVirtualMachine2**.

### Check the load balancer

1. In the Azure portal, search for **Load balancers**, then under **Services**, select **Load balancers**.

    ![Screenshot showing searching for load balancers.](../media/6-search-load-balancers.png)

1. Select the **webLoadBalancer**.

1. On the left, under **Settings**, select **Frontend IP configuration**.

   ![A screenshot showing  the frontend IP address highlighted.](../media/6-front-end-ip-configuration.png)

1. Check that there is a frontend IP address, and that this is the IP you tested at the beginning of this exercise.

1. On the left, under **Settings**, select **Load balancing rules**.

   ![Screen shot showing the load balancing rules.](../media/6-load-balancing-rules.png)

   > [!NOTE] 
   > There is a rule for port **80** and port **443**.

1. On the left, select **Diagnose and solve problems**.

### Use the Diagnose and solve problems troubleshooter

1. Select the **No connectivity to the backend pool** troubleshooter.

   ![A screenshot showing the No connectivity to backend pool troubleshooter.](../media/6-no-connectivity.png)

1. In the **Tell us more about the problem you are experiencing** drop-down box, select **Intermittent connectivity**.

1. Scroll down and read the insight found.

    ![Screen shot of the connectivity results showing the backend pool can't be connected to over port 443.](../media/6-no-connectivity-back-end-pool.png)

    The insight points to the fact that the backend instances in the pool aren't listening for port **443**. The website instances should be listening to port **80**. This insight points to a problem in the load balancer rule.

## Resolve the connection issue

After investigating the connection issues to your website, you've found an issue with the load balancer rule user by the virtual machine scale set. You'll now resolve the issue and check that the website can be accessed.

### Validate load balancer rules

1. In the Azure portal, search for **load balancers**, and then under **Services**, select **Load balancers**.

   ![A screenshot showing searching for load balancers." lightbox="../media/7-load-balancers.png](../media/7-load-balancers.png)

1. Select the **webLoadBalancer** load balancer.

1. Under **Settings**, select **Load balancing rules**.

   ![A screenshot showing the load balancer rule highlighted." lightbox="../media/6-load-balancing-rules.png](../media/6-load-balancing-rules.png)

1. From the list of rules, select **webLoadBalancerRule**.

   ![A screenshot of the load balancer rule, showing the port set to 80 and backend port set to 443. This is the error." lightbox="../media/7-my-load-balancer-rule-web.png](../media/7-my-load-balancer-rule-web.png)

1. To resolve the backend issue, change the **Backend port** from **443** to **80**, and then select **Save**.

    > [!NOTE]
    > The front and backend in this environment need to be the same to get a response from the webserver to http requests.

1. Wait until the rule has been deployed successfully.

    ![A screenshot showing the successful deployment of the updated load balancer rule." lightbox="../media/7-successful-rule.png](../media/7-successful-rule.png)

### Verify that the website can now be reached

1. Refresh the tab you opened to test the public IP address.

    > [!NOTE]
    > If you have closed the previous browser tab, run this command to get the public IP address:
    >
    > ```
    > az network public-ip show \
    > --resource-group lab6rg \
    > --name webPublicIP \
    > --query '[ipAddress]' \
    > --output tsv
    > ```

    If the website is online, you'll see a page with a **Hello World** message from the backend instance.

(../media/7-hello-world.png)![A screenshot of the website showing it's online.]

## Resolve the load balancer rule problems

1. In the Azure portal, search for **load balancers**, and then under **Services**, select **Load balancers**.

   ![A screenshot showing searching for load balancers." lightbox="../media/7-load-balancers.png](../media/7-load-balancers.png)

1. Select the **webLoadBalancer** load balancer.

1. Under **Settings**, select **Load balancing rules**.

   ![A screenshot showing the load balancer rule highlighted." lightbox="../media/6-load-balancing-rules.png](../media/6-load-balancing-rules.png)

1. From the list of rules, select **webLoadBalancerRule**.

   ![A screenshot of the load balancer rule, showing the port set to 80 and backend port set to 443. This is the error." lightbox="../media/7-my-load-balancer-rule-web.png](../media/7-my-load-balancer-rule-web.png)

1. To resolve the backend issue, change the **Backend port** from **443** to **80**, and then select **Save**.

    > [!NOTE]
    > The front and backend in this environment need to be the same to get a response from the webserver to http requests.

1. Wait until the rule has been deployed successfully.

    ![A screenshot showing the successful deployment of the updated load balancer rule." lightbox="../media/7-successful-rule.png](../media/7-successful-rule.png)

## Verify that the website can now be reached

1. Refresh the tab you opened to test the public IP address.

    > [!NOTE]
    > If you have closed the previous browser tab, run this command to get the public IP address:
    > ```
    > az network public-ip show \
    > --resource-group lab6rg \
    > --name webPublicIP \
    > --query '[ipAddress]' \
    > --output tsv
    > ```

    If the website is online, you'll see a page with a **Hello World** message from the backend instance.

    ![A screenshot of the website showing it's online.](../media/7-hello-world.png)

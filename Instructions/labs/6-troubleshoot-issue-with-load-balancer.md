# Module 06: Troubleshoot routing, traffic control and load balancing issues

## Lab Scenario

In this lab, you are working as a support engineer responsible for Azure infrastructure. The web team in your organization has reported performance issues with the organization's website. The web team uses a pool of web servers behind a load balancer to distribute incoming web traffic. The web team has noticed that the internet traffic isn't being distributed equally between all the web servers, leading to performance problems.

## Lab Objectives

In this lab you will perform:

+ Task 1: Check that the issue still exists
+ Task 2: Check all the virtual machines are responding on port 80
+ Task 3: Check the Load Balancer settings
+ Task 4: Resolve load balancer issue
+ Task 5: Test that the issue has been resolved

## Estimated timing: 30 minutes

You work as a support engineer supporting Azure infrastructure. You've been contacted by your web team about an issue with website performance. The web team has a pool of webservers behind a load balancer and public IP address.

![Screenshot of a network topology diagram showing a pool of webservers behind a load balancer and public IP address.](../media/mod6-1.png)

The web team thinks that the internet traffic isn't being distributed equally between all the webservers.

In this lab, you'll use what you've learned to go through steps to troubleshoot the performance issues with your organization's website.

### Task 1: Check that the issue still exists

1.  If you are not logged in already, click on Azure portal shortcut that is available on the desktop and log in with below Azure credentials or skip to **step 2**.
    * Azure Username/Email: <inject key="AzureAdUserEmail"></inject> 
    * Azure Password: <inject key="AzureAdUserPassword"></inject>

1. Use this Azure CLI command in the Cloud Shell to get the public IP address of the scale set.

    ![Screenshot showing how to access the cloud shell.](../media/mod6-2.png)

1. Select **Bash**

1. Click **Show advanced settings** and enter the details as shown below:
 
    ![](../media/az720-5-6-cli.png)

   * Resource group : Select **Use existing** -> **lab06-rg-<inject key="DeploymentID" enableCopy="false"/>**
   * Storage account : Select **Create new** and Enter **cloudstore<inject key="DeploymentID" enableCopy="false"/>**
   * File Share: Select **Create new** and Enter **blob**

> **Note :** Please wait until the storage account is created and proceed with step4.

4. In the cloud shell run this command:
   
    ```
    az network public-ip show \
    --resource-group lab06-rg-DID \
    --name webPublicIP \
    --query '[ipAddress]' \
    --output tsv
    ```
    > **Note** In the above snippet, replace **DID** with <inject key="Deployment ID" enableCopy="false" />

5. Copy the IP address, in a new tab in your browser, try to navigate to it.

     ![Screenshot of the virtual machines.](../media/az720-6-2.png)
    > **Note**
    > Your IP address will be different to the one shown in the screenshot.

6. Hit refresh in your browser for few times and notice that you are getting a response from the same webserver every single time.

    ![Screenshot showing a single web server returing a response.](../media/mod6-5.png)

7. Leave this tab open to check you resolve the issue in the next exercise.

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - Navigate to the Lab Validation Page, from the upper right corner in the lab guide section.
    > - Hit the Validate button for the corresponding task. You can proceed to the next task if you receive a success message.
    > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

### Task 2: Check all the virtual machines are responding on port 80

1. Navigate to the Azure Portal in a new tab.

1. Select **Virtual machines**.

    ![Screenshot of the virtual machines.](../media/mod6-6.png)

1. Select **WebVM1-labrg06-<inject key="Deployment ID" enableCopy="false" />**.

1. Under **Settings**, select **Networking**.

    ![Screenshot of the networking settings for the virtual machine.](../media/mod6-7.png)
  
1. Check that inbound traffic is allowed on port **80**.

1. Repeat the above for **WebVM2-labrg06-<inject key="Deployment ID" enableCopy="false" />**.

The current settings appear to be correct.

### Task 3: Check the Load Balancer settings

1. Select **Home**.

1. Search for **Load balancers**.

    ![A screenshot of the Azure portal showing Load balancers being selected.](../media/mod6-8.png)

1. Under **Services**, select **Load balancers**.

    ![A screenshot showing the load balancers.](../media/az720-6-3.png)

1. Select **webLoadBalancer**.

1. Under **Settings**, Select the **Frontend IP configuration**.

    ![Screenshot of the frontend IP settings of the load balancer.](../media/mod6-10.png)

    > **Note**
    > There is a correctly configured frontend IP address. Check that it matches the IP address you have in your other tab.

1. Under **Settings**, select **Backend pools**.

    ![Screenshot of the load balancer backend pool.](../media/mod6-11.png)

    > **Note**
    > The virtual machines are both running.

1. Under **Settings**, select **Load balancing rules**, then select **webLoadBalancerRule**.

    ![Screenshot showing the webLoadBalancerRule to select.](../media/mod6-12.png)

1. Check all the settings for the load balancer rule and leave the rest at default.

    ![Screenshot of the webLoadBalancerRule settings.](../media/mod6-13.png)

You think you have identified the issue. At the moment once a user visits the website they are routed to one virtual machine. This will persist because of the **Session persistence** setting.

### Task 4: Resolve load balancer issue

1. To resolve the backend issue, change the **Session persistence** from **Client IP and protocol** to **None**, and then select **Save**.

    ![Screenshot showing the rule fixed.](../media/mod6-14.png)

    > **Note**
    > By setting **Session persistence** to **None**, successive request from clients can be handled by different virtual machines.

1. Wait until the rule has been deployed successfully.

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - Navigate to the Lab Validation Page, from the upper right corner in the lab guide section.
    > - Hit the Validate button for the corresponding task. You can proceed to the next task if you receive a success message.
    > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

### Task 5: Test that the issue has been resolved

Switch back to the tab where you pasted the public IP address. If you have closed the tab select the load balancer rule and the public IP address is listed there.

1. Refresh the browser 20 or 30 times, you should see the message switch between **WebVM1-labrg06-<inject key="Deployment ID" enableCopy="false" />** and **WebVM2-labrg06-<inject key="Deployment ID" enableCopy="false" />**.
1. The traffic is now being shared correctly between all the machines in the backend pool.

    ![Animated gif showing the different webservers responding.](../media/traffic_distribution.gif)

## Review

In this lab you have Performed:

- Confirming website performance issue related to uneven traffic distribution.
- Checking VM settings and Load Balancer configuration.
- Adjusting Load Balancer session persistence settings to "None" for improved traffic distribution.
- Testing the fix by refreshing the browser and verified that traffic was evenly distributed among web servers.

## You have Successfully Completed the Lab

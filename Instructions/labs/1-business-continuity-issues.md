---
lab:
    title: 'Lab 1 - Troubleshoot business continuity issues'
    module: 'Troubleshoot business continuity with Microsoft Azure'
---

An Azure sandbox subscription is provided for this lab. You can use the following Azure subscription credentials to complete this lab:

| | |
|---:|:---|
| Email Address | +++@lab.CloudPortalCredential(User1).Username+++ |
| Password | +++@lab.CloudPortalCredential(User1).Password+++ |

> [!tip] If you're interested in completing this lab using your own Azure subscription, sign up for a free trial at <https://azure.microsoft.com/free/>.

# Troubleshoot business continuity with Microsoft Azure

**Estimated Time: 15 minutes**

You've been asked to make a backup of a virtual machine. Your development team have asked that you make the backup application consistent.

## Create a backup

1. Sign in to the [Azure portal](https://portal.azure.com) using the credentials above, or if you'd like your own Azure subscription.

1. Select the portal menu on the top left, select **Virtual machines**, and then select **VM1**.

1. If the VM is running, select **Stop**, select **Do you want to reserve the Public IP address**, then select **OK**. 

1. Wait for the **Status** to show **Stopped (deallocated)**.

    ![Screenshot showing the stopped vm in the Azure portal.](../media/mod1-stopped-vm.png)

1. In the left navigation pane, under **Operations**, select **Backup**.

1. Enter the following settings:

   - Recovery Services vault: **Create new**
   - Backup vault: **labdemo**
   - Resource Group: Use the default resource group
   - Policy sub type: **Standard**
   - Choose backup policy: Use the default policy

    Review the policy details at the bottom of the page.

1. Select **Enable backup** and wait for the deployment to complete.

1. Select the portal menu on the top left, select **Virtual machines**, and then select **VM1**.

1. In the left navigation pane, under **Operations**, select **Backup**.

1. Select **Backup now**, and then select **OK**.

    ![Screenshot showing the backup screen.](../media/4-machine-backup-screen.png)

1. The backup starts, and runs in two phases.

    - In the first phase, it takes a snapshot of the VM. This takes about 10 minutes.

    - In the second phase, it moves the data into the recovery services vault. This takes about 50 minutes.

## Monitor progress of the first phase

You won't see anything in the Backup page of the VM until the backup has completed. However, you can monitor it in the Backup center.

1. In the Azure portal, search for **backup center**, and then under **Services**, select **Backup center**.

1. In the left navigation pane, under **Monitoring + reporting**, select **Backup jobs**. You should see your job running.

    ![Screenshot showing the backup job running.](../media/4-backup-instances.png)

1. Select the backup job to see more info. You can see in the screen picture that the first phase has completed.

    ![Screenshot showing a single backup instance.](../media/4-single-backup.png)

## View results of first phase

When the first phase has completed, you can see that it is **Crash Consistent**.

1. In the breadcrumb trail, select **Backup center**.

1. In the left navigation pane, under **Manage**, select **Backup instances**.

    ![Screenshot showing multiple backup instances.](../media/4-multiple-backups.png)

1. Scroll down until you see **VM1** and select it.

    ![Screenshot showing a crash consistent backup only](../media/mod1-crash-consistent-backup.png)

    This shows that the backup is **Crash Consistent**. This is incorrect, the backup should be **Application Consistent**. You need to find out why and fix the problem.

    > [!note] Only the first phase has completed, and the data will now be transferring to the vault. You can continue with the lab; you don't have to wait for this to complete.

 
## Create an application consistent backup

The reason the backup isn't application consistent is because the VM is in a **Stopped** state. You need to start the VM and wait for it to be in a running state before making your backups.

1. Select the portal menu on the top left, select **Virtual machines**, and then select **VM1**.

1. Select **Start**, and wait for the **Status** to show **Running**.

1. In the left navigation pane, under **Operations**, select **Backup**.

1. On the **VM1** page, you should see that there is one **Crash Consistent** restore point.

1. Select **Backup now**.

    ![Screenshot of the backup screen.](../media/4-backup-screen.png)

1. On the **Backup now** page, select **OK**. The second backup will be an incremental backup and should take less time than the first backup.

1. You can monitor progress in the **Backup center**.

1. When the first phase is complete, navigate to the **Backup** page of the VM. 
1. You now have two backups, and the latest is **Application Consistent**.

    ![Screenshot showing the crash consistent restore point.](../media/4-crash-instance-restore-point.png)



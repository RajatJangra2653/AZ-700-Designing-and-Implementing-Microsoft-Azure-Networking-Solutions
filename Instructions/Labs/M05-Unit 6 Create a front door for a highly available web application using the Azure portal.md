# Module 05: Unit 6 Create a Front Door for a highly available web application using the Azure portal

## Lab scenario
In this lab, you will set up an Azure Front Door configuration that pools two instances of a web application that runs in different Azure regions. This configuration directs traffic to the nearest site that runs the application. Azure Front Door continuously monitors the web application. You will demonstrate automatic failover to the next available site when the nearest site is unavailable.

**Note:** An **[interactive lab simulation](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Create%20a%20Front%20Door%20profile%20for%20a%20highly%20available%20web%20application)** is available that allows you to click through this lab at your own pace. You may find slight differences between the interactive simulation and the hosted lab, but the core concepts and ideas being demonstrated are the same.

## Lab objectives
In this lab, you will complete the following tasks:

+ Task 1: Create two instances of a web app
+ Task 2: Create a Front Door for your application
+ Task 3: View Azure Front Door in action

## Estimated time: 30 minutes

## Architecture diagram

![Network configuration for Azure Front Door.](../media/front-door-environment-diagram.png)

## Task 1: Create two instances of a web app

This exercise requires two instances of a web application that run in different Azure regions. Both the web application instances run in Active/Active mode, so either one can take traffic. This configuration differs from an Active/Stand-By configuration, where one acts as a failover.

1. On Azure Portal page, in **Search resources, services and docs (G+/)** box at the top of the portal, enter **WebApp**, and then select **App Services** under services.

   ![Web App](../media/l5u6-1.png)

1. Select **+ Create (1)**  and then select **Web App (2)** to create a Web App.

   ![Web App](../media/create.png)

1. On the Create Web App page, on the **Basics** tab, enter or select the following information, and select **Review + create (9)**.

   | **Setting**      | **Value**                                                    |
   | ---------------- | ------------------------------------------------------------ |
   | Subscription     | Select your subscription (1).                                    |
   | Resource group   | Select **ContosoResourceGroup-<inject key="DeploymentID" enableCopy="false"/> (2)**                |
   | Name             | **WebAppContoso-1-<inject key="DeploymentID" enableCopy="false"/> (3)** |
   | Publish          | Select **Code (4)**                                             |
   | Runtime stack    | Select **.NET 6 (LTS) (5)**                                     |
   | Operating System | Select **Windows**                                          |
   | Region           | Select **Central US (6)**                                       |
   | Windows Plan     | Select **Create new** and enter **myAppServicePlanCentralUS (7)** in the text box |
   | Princing Plan    | Select **Standard S1 100 total ACU, 1.75 GB memory (8)**        |
   |||

   ![Web App](../media/l5u6-3.png)

1. Review the Summary, and then select **Create**.

   >**Note:** It might take 2 minutes for the deployment to complete.

1. Create a second web app,  in **Search resources, services and docs (G+/)** box at the top of the portal, enter **WebApp**, and then select **App Services** under services.

1. Select **+ Create**  and then select **Web App** to create a Web App.

1. On the Create Web App page, on the **Basics** tab, enter or select the following information, and select **Review + create (10)**.

   | **Setting**      | **Value**                                                    |
   | ---------------- | ------------------------------------------------------------ |
   | Subscription     | Select your subscription (1).                                    |
   | Resource group   | Select **ContosoResourceGroup-<inject key="DeploymentID" enableCopy="false"/> (2)**               |
   | Name             | **WebAppContoso-2-<inject key="DeploymentID" enableCopy="false"/> (3)** |
   | Publish          | Select **Code (4)**                                             |
   | Runtime stack    | Select **.NET 6 (LTS) (5)**                                     |
   | Operating System | Select **Windows (6)**                                          |
   | Region           | Select **East US 2 (7)**                                          |
   | Windows Plan     | Select **Create new** and enter **myAppServicePlanEastUS (8)** in the text box. |
   | Pricing Plan     | Select **Standard S1 100 total ACU, 1.75 GB memory (9)**        |
   |||

   ![Web App](../media/l5u6-4.png)

1. Review the Summary, and then select **Create**.

   **Note**: Wait for deployment to complete it might take several minutes.

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task.
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help.

   <validation step="f59939fe-eba5-483e-8067-81aa03a7bc70" />

## Task 2: Create a Front Door for your application

Configure Azure Front Door to direct user traffic based on lowest latency between the two web apps servers. To begin, add a frontend host for Azure Front Door.

1. On any Azure Portal page, in **Search resources, services and docs (G+/)** box at the top of the portal, Search for **Front Door and CDN profiles (1)**, and then select **Front Door and CDN profiles (2)**.

   ![Web App](../media/l5u6-5.png)

1. On the **Front Door and CDN profiles** page, select **+ Create**.

1. On the Compare offerings page, select **Quick create**. Then select **Continue to create a Front Door**.

    ![Web App](../media/l5u6-6.png)

1. On the Basics tab, enter or select the following information.

      | **Setting**             | **Value**                                    |
      | ----------------------- | -------------------------------------------- |
      | Subscription            | Select your subscription (1).                    |
      | Resource group          | Select **ContosoResourceGroup-<inject key="DeploymentID" enableCopy="false"/> (2)**                  |
      | Resource group location | Accept default setting (3)                      |
      | Name                    | **FrontDoor<inject key="DeploymentID" enableCopy="false"/> (4)**   |
      | Tier                    | Standard (5)  |
      | Endpoint Name           | FDendpoint (6)  |
      | Origin Type             | App Services (7) | 
      | Origin host name        | **WebAppContoso-1-<inject key="DeploymentID" enableCopy="false"/> (8)** |
      |||

1. Select **Review and Create (9)**, and then select **Create**.

   ![Web App](../media/l5u6-7.png)

1. Wait for the resource to deploy, and then select **Go to resource**.

1. On the Front Door resource in the Overview blade, locate the **Origin groups (1)**, to update select the origin group **default-origin-group (2)** from the list and click on **+ Add an origin (3)**

   ![Web App](../media/l5u6-8.png)

1. On the **Add on Origin** window, add the following information and select **Add (5)**.

   | **Setting**             | **Value**                                    |
   | ----------------------- | -------------------------------------------- |
   | Name                    | **default-origin-group (1)**                         |
   | Origin type             | **App services (2)**                                  |
   | Host name               | Select **WebAppContoso-2-<inject key="DeploymentID" enableCopy="false"/> (3)** |
   | Origin host header      | Select **WebAppContoso-2-<inject key="DeploymentID" enableCopy="false"/> (4)** |
   |||

   ![Web App](../media/l5u6-9.png)

1. Select **Update**.

   ![Web App](../media/l5u6-(10).png)

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help.

   <validation step="f5cf3eee-49ad-4985-88ca-5bc0c80e8ddc" />

## Task 3: View Azure Front Door in action

Once you create a Front Door, it takes a few minutes for the configuration to be deployed globally. Once complete, access the frontend host you created.

1. Navigate back to **Front Door and CDN profiles** page, on the Front Door resource in the Overview blade, locate the endpoint hostname that is created for your endpoint. This should be fdendpoint followed by a hyphen and a random string. For example, **fdendpoint-fxa8c8hddhhgcrb9.z01.azurefd.net**. **Copy** this FQDN.
      ![Web App](../media/l5u6-11.png)

1. In a new browser tab, navigate to the Front Door endpoint FQDN. The default App Service page will be displayed.

      ![Web App](../media/l5u6-12.png)

1. To test instant global failover in action, try the following steps:

1. Switch to the Azure portal, search for and select **App services**.

1. Select one of your web apps, then select **Stop**, and then select **Yes** to verify.

      ![Web App](../media/l5u6-13.png)

1. Switch back to your browser and select Refresh. You should see the same information page.

    **Note**: There may be a delay while the web app stops. If you get an error page in your browser, refresh the page.

1. Switch back to the Azure Portal, locate the other web app, and stop it.

1. Switch back to your browser and select Refresh. This time, you should see an error message.

      ![Web App](../media/l5u6-14.png)

Congratulations! You have configured and tested an Azure Front Door.

## Extend your learning with Copilot

Copilot can assist you in learning how to use the Azure scripting tools. Copilot can also assist in areas not covered in the lab or where you need more information. Open an Edge browser and choose Copilot (top right) or navigate to *copilot.microsoft.com*. Take a few minutes to try these prompts.
+ What are the differences between Azure Application Gateway and Azure Front Door? Provide examples where I would use each product.
+ Provide a checklist of things to do when configuring Azure Front Door.
+ What is an origin in Azure Front Door and how is it different from an endpoint?


## Learn more with self-paced training

+ [Introduction to Azure Front Door](https://learn.microsoft.com/training/modules/intro-to-azure-front-door/). In this module, you learn how Azure Front Door can protect your applications.
+ [Load balance your web service traffic with Front Door](https://learn.microsoft.com/training/modules/create-first-azure-front-door/). In this module, you learn to create and configure Azure Front Door. 

## Key takeaways

Congratulations on completing the lab. Here are the main takeaways for this lab. 
+ Azure Front Door is a cloud-based service that delivers your applications anywhere across the globe. 
+ Azure Front Door uses layer 7 load balancing to distribute traffic across multiple regions and endpoints.
+ Azure Front Door supports different traffic routing methods to determine how your HTTP/HTTPS traffic is distributed. The routing methods are: latency, priority, weighted, and session affinity. 

### Review
In this lab, you have completed:

- Create two instances of a web app
- Create a Front Door for your application
- View Azure Front Door in action

## You have successfully completed the lab.

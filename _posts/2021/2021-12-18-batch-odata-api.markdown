---
layout: single
title:  Batch OData API in Dynamics 365 Finance and Operations using Power Automate
tags:
  - Power Automate
  - D365FO
categories:
 - System Administration
---


Dynamics 365 Finance & Operations version 10.0.22 released a new API ( Batch OData API ) that can be used to requeue batch jobs from Power Automate or other external applications. More details of this feature can be found out in Microsoft docs [https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/get-started/whats-new-platform-updates-10-0-22](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/get-started/whats-new-platform-updates-10-0-22)

Is only possible to requeue batch jobs that are in a terminal state. By taking advantage of this new functionality, we will try to trigger jobs to be triggered by Power Automate.

## Requirement

A custom exchange rate provider of Dynamics 365 depends on exchange rates exported from an external system. Ability to retrieve exchange rates from an external service even the system does not provide an exact timeframe to export data. 

## Resolution approach

A possible approach can be scheduling a batch job several times a day. Eventually one of the runs would successfully import the file stored in a Microsoft One Drive folder.  

We will follow a different approach for this scenario, execute a batch job only if the file is available.

## Configuration

Open Batch jobs ( System Administration > Inquiries > Batch jobs ). Create a new batch job by pressing “New”.

![!](/assets/images/posts/2021/1812_image1.png)

Fill in all required information ( Description, Batch, tasks, etc. ). Select “Save” and take note of Batch job id. 
   
![!](/assets/images/posts/2021/1812_image2.png)

## Power Automate

In Power Automate create a trigger when a file is created in a specific Microsoft One Drive Folder. In this case, a folder named ExchageRates

![!](/assets/images/posts/2021/1812_image3.png)

Create an action to Dynamics 365 Finance & Operations. 
1. Fill in instance Url. 
2. In Action select "BatchJobs-SetBatchJobToWaiting".
3. batchJobId is Dynamics 365 Finance & Operations Batch Job Id that was created in the previous step.

![!](/assets/images/posts/2021/1812_image4.png)

Create a Parse Json action to handle answer values.

![!](/assets/images/posts/2021/1812_image5.png)

Create a condition to confirm if the Batch job was requeue successfully by checking IsSuccess value output. A success value confirms that the job was set to a waiting state and not that it executed without errors.

Save and active Power Automate Flow.


![!](/assets/images/posts/2021/1812_image6.png)

## Simulation

Now let's validate the whole process.  
1.  Drop a file in OneDrive that triggers flow from Power Automate Flow. 

![!](/assets/images/posts/2021/1812_image9.png)

2. Check Power Automate Flow Run Execution history

![!](/assets/images/posts/2021/1812_image8.png)

3. Open Batch Job History and confirm that Batch job was executed.


![!](/assets/images/posts/2021/1812_image7.png)









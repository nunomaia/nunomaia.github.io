---
title: Batch OData API in Dynamics 365 Finance and Operations using Power Automate
description: Manage batch jobs from Power Automate or other external applications using Batch OData API.
date: 2021-12-18 00:00:00+0000
image: /images/banners/minku-kang-aCniNTiIFd8-unsplash.jpg
categories:
    - System Administration
toc : false
---

D365 Finance & Operations version 10.0.22 introduced a new API, the Batch OData API. This API allows you to requeue batch jobs from Power Automate or other external applications. You can find more details about this feature in the Microsoft documentation Microsoft Docs [https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/get-started/whats-new-platform-updates-10-0-22](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/get-started/whats-new-platform-updates-10-0-22).

It's important to note that only batch jobs in a terminal state can be requeued.

By leveraging this new functionality, we can use Power Automate to trigger the resubmission of failed or cancelled batch jobs.

## Requirement

A custom exchange rate provider in Dynamics 365 depends on exchange rates data file exported from an external system. The ability to retrieve exchange rates from an external service even the system does not provide an exact timeframe to export data.

## Resolution approach

One approach might involve scheduling a batch job to run several times a day. However, this is inefficient, as it could lead to unnecessary executions if the file isn't always available in the Microsoft OneDrive folder.

Therefore, a better approach for this scenario would be to execute the batch job only when the file is actually available.

## Configuration

Open Batch jobs ( System Administration > Inquiries > Batch jobs ). Create a new batch job by pressing “New”.

![Image](1812_image1.png)

Fill in all required information ( Description, Batch, tasks, etc. ). Select “Save” and take note of Batch job id.

![Image](1812_image2.png)

## Power Automate

Using Power Automate, we can create a trigger that fires whenever a file is created in a specific Microsoft OneDrive folder called "ExchangeRates."

![Image](1812_image3.png)

Create an action to Dynamics 365 Finance & Operations.

1. Fill in instance Url.
2. In Action select "BatchJobs-SetBatchJobToWaiting".
3. batchJobId is Dynamics 365 Finance & Operations Batch Job Id that was created in the previous step.

![Image](1812_image4.png)

Create a Parse Json action to handle answer values.

![Image](1812_image5.png)

Create a condition to confirm if the Batch job was requeue successfully by checking IsSuccess value output. A success value confirms that the job was set to a waiting state and not that it executed without errors.

Save and active Power Automate Flow.

![Image](1812_image6.png)

## Simulation

Now let's validate the whole process.

1. Drop a file in OneDrive that triggers flow from Power Automate Flow.

    ![Image](1812_image9.png)

2. Check Power Automate Flow Run Execution history

    ![Image](1812_image8.png)

3. Open Batch Job History and confirm that Batch job was executed.

    ![Image](1812_image7.png)

> Photo by [Minku Kang](https://unsplash.com/@minkus) on [Unsplash](https://unsplash.com/)

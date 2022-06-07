---
layout: single
title:  General journals Posting Performance - Part I 
tags:                                             
  - D365FO
categories:
 - Finance 
---

Dynamics 365 General Journals were designed to handle large volume of rows.  It's difficult to define exactly the baseline line to classify a journal as a large volume. 

Our case was post journal **500 000** lines in a 6 hours time window. A performance throughput of roughly 1389 lines per minute during posting. 

The absolute numbers will vary in production environments due to various factors, configurations, dimensions per line, etc.

## Baseline

First, let's define our baseline. Let's post a journal with 10 000 lines. The posting job took 50 minutes. We got a performance of 200 lines per minute. These numbers are way beyond the expected performance.      

## Configuration

We will take advantage of parallel posting of general journals by configuring “Lines limit” field in the journal name setup. We will play with this value, by simulation different scenarios.

![!](/assets/images/posts/2022/06/20220606_01.png)


“Lines limit” will distribute lines from current the journal to smaller journals by the amount set by the user in setup.  Smaller journals might not be equal in size, there are splitting rules ( balance by voucher, etc.).  

Simplistic graphical representation of LedgerJournalPostBatch.  

![!](/assets/images/posts/2022/06/20220606_02.png)


First, let’s fine tuning values under a single AOS Batch Server. AOS was limited to 8 simultaneous batch tasks. In one of the executions, we have increased to simultaneous batch tasks to 12. Performance decreased by incrementing value to 12 batch tasks. Meaning that, AOS lost more time throttling between tasks.   

Post 10 000 lines, under a single AOS.


| Threads AOS | Number AOS | Lines to Post | Execution Time | Posting lines per Minute | Lines per Batch |
|-------------|------------|---------------|----------------|--------------------------|-----------------| 
| 8           | 1          | 10 000        | 00:24:47       | 417                      | 1 0000          |
| 12          | 1          | 10 000        | 00:17:03       | 589                      | 500             |
| 8           | 1          | 10 000        | 00:16:19       | 625                      | 500             |



Post 10 000 lines, with multiple AOS


| Threads AOS | Number AOS | Lines to Post | Execution Time | Posting lines per Minute | Lines per Batch |
|-------------|------------|---------------|----------------|--------------------------|-----------------| 
| 8           | 4          | 10 000        | 00:07:39       | 1 429                    | 500             |


Post 50 000 lines, multiple AOS 


| Threads AOS | Number AOS | Lines to Post | Execution Time | Posting lines per Minute | Lines per Batch |
|-------------|------------|---------------|----------------|--------------------------|-----------------| 
| 8           | 4          | 50 000        | 00:34:47       | 1 471                    | 250             |


All Values


| Threads AOS | Number AOS | Lines to Post | Execution Time | Posting lines per Minute | Lines per Batch |
|-------------|------------|---------------|----------------|--------------------------|-----------------| 
| 8           | 1          | 10 000        | 00:24:47       | 417                      | 1 0000          |
| 12          | 1          | 10 000        | 00:17:03       | 589                      | 500             |
| 8           | 1          | 10 000        | 00:16:19       | 625                      | 500             |
| 8           | 4          | 10 000        | 00:07:39       | 1 429                    | 500             |
| 8           | 4          | 50 000        | 00:34:47       | 1 471                    | 250             |

## Conclusion

By increasing the number of AOS and using smaller batches was possible to have a better throughout. 




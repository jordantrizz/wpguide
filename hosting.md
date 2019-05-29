# Hosting
This document is a means to remind myself of the hosting solutions available but also pointing out their technical infrastructure, function and performance.

<!--ts-->
   * [Hosting](hosting.md#hosting)
   * [Dedicated Cloud/VPS](hosting.md#dedicated-cloudvps)
      * [US](hosting.md#us)
   * [VPS](hosting.md#vps)
      * [US](hosting.md#us-1)
      * [Europe](hosting.md#europe)
   * [To Review](hosting.md#to-review)
   * [Testing Method](hosting.md#testing-method)
      * [Suggestions](hosting.md#suggestions)
      * [PHP Performance](hosting.md#php-performance)

<!-- Added by: jtrask, at: Wed 29 May 2019 12:53:32 PDT -->

<!--te-->

# Dedicated Cloud/VPS
## US
Company | Description|
 --- | --- | --- |
| [Vultr](https://vultr.com/pricing/dedicated) | Dedicated resources, SSD, w
# VPS
## US
Company | Type | Description|
 --- | --- | --- |
| [OpenVZ](https://openvz.io/) | VPS | Cheap VPS running OpenVZ

## Europe
Since I don't live here it's hard to keep track of decent providers.
Company | Description|
 --- | --- | --- |
| [UpCloud](https://upcloud.com/) | French only DC's |
| [UpCloud] (scaleway.com) | Europe |

# To Review
- vipsdime.com - Decent, don't know. USD.

# Testing Method
The testing method should ensure the following is completed.
1. CPU Performance
2. Disk Performance
3. PHP Function Performance (Important for WordPress)
4. Memory Performance
5. Network Latency and Throughout-put

##  Suggestions
Still need to hash this out.
- https://github.com/n-st/nench
- Disk Tests Random IO
    - ```fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=random_read_write.fio --bs=4k --iodepth=64 --size=4G --readwrite=randrw --rwmixread=75```
## PHP Performance
Need to update and include 
- https://github.com/jordantrizz/ultimate-linux-tool-box/blob/master/php-cpu-test.php

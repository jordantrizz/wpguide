# Hosting
This document is a means to remind myself of the hosting solutions available but also pointing out their technical infrastructure, function and performance.

This is a work in progress and I'd like to better be able to display information as well as sort it accurately. Open to suggestons.

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

# Dedicated Resources/Bare Metal
Recently only found this on Vultr, however it seems to out perform most providers. Dedicated resources if you need them.
## US
Company | Description|
 --- | --- | --- |
| [Vultr](https://vultr.com/pricing/dedicated) | Dedicated resources, no over-comitted.

# VPS/Cloud
I'm grouping VPS/Cloud due to the fact they're basically the same underlying technology, but with a different stack.

## US
| Company | Type | Description|
 --- | --- | --- |
| [OpenVZ](https://openvz.io/) | VPS | Cheap VPS running OpenVZ

## Europe
Since I don't live here it's hard to keep track of decent providers.
| Company | Description|
 --- | --- | --- |
| [UpCloud](https://upcloud.com/) | French only DC's |
| [UpCloud] (scaleway.com) | Europe |

# Container Based

| Company | Stack |  Description |
 --- | --- | --- |
| [Convesio](https://convesio.com/) | Docker | Using Docker Imaging for scaling/self-healing/high-availability.

# Managed Hosting
# Shared
# Control Panel and Stacks
Company | Description|
 --- | --- | --- |
[EasyWP](https://easywp.com)| Managed Shared Hosting by NameCheap, no idea on stack.
# Docker

# Control Panel and Stacks
Company | Description|
 --- | --- | --- |
| [Cloudways](https://www.cloudways.com/en/pricing.php) | Provides a decent NGiNX/PHP-FPM/Varnish Stack, includes a caching plugin called Breeze.
| [SpinupWP](https://spinupwp.com)| Control Panel that works with Digital Ocean and other VPS providers.
| [GRIDPANE](https://gridpane.com/) | Control Panel that works with multiple providers.

# Email Providers
The following section was added because I feel it's helpful.

<aside class="notice">The following links below may contain referral URL's and provide a kickback to myself.</aside>

Company | Cost | Storage | Description|
 --- | --- | --- |
| [GSuite Basic](https://goo.gl/P1dcnY) | $6/m USD | 30GB | Email + Storage. 30GB shared.
| [GSuite Business](https://goo.gl/P1dcnY) | $12/m USD | 1000GB | Basic + 1TB or unlimited storage after 5 users.
| [GSuite Non-profit](https://support.google.com/nonprofits/answer/3367223?hl=en) | FREE | 30GB | GSuite Basic but free.
| [Mail Cheap](https://www.mailcheap.co/client/aff.php?aff=51) | $2/m USD | 10GB | Lots of options for email services, including dedicated servers.
| [Zoho](https://www.zoho.com/mail/zohomail-pricing.html) | $1/m USD | 5GB | ?
| [Rack Space](https://www.rackspace.com/email-hosting) | $2.99/m USD | 25GB | ?
| [Migadu](https://www.migadu.com) | Unlimited features always. 1GB/10 Outgoing emails for Free. 100 Outgoing emails for $4/m USD. 

# To Review
- vipsdime.com - Decent, don't know. USD.
- frantech
- nosupportlinux

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

# Main Menu
<details><summary>Click to Expand</summary>
<p>

* [Home](README.md) - This page!
* [WordPress](wordpress.md) - A guide on self hosting WordPress.
* [Alternatives](alternatives.md) - Alternatives to WordPress
* [Hosting](hosting.md) - WordPress Hosting Providers
* [Tools](tools.md) - List of commonly used tools.
* [Troubleshooting](troubleshooting.md) - Troubleshooting guide.

</p>
</details>

# Disclaimer
* Please Read [Ultimate WordPress Guide](README.md) First!
* Contact is here [Ultimate WordPress Guide](README.md#Contact)

# Table of Contents

<!--ts-->
   * [Main Menu](#main-menu)
   * [Disclaimer](#disclaimer)
   * [Table of Contents](#table-of-contents)
   * [Hosting](#hosting)
   * [Bare Metal](#bare-metal)
      * [US](#us)
   * [Dedicated Resources](#dedicated-resources)
      * [US](#us-1)
   * [VPS/Cloud](#vpscloud)
      * [US](#us-2)
      * [Europe](#europe)
   * [Managed Hosting/WordPress Specific](#managed-hostingwordpress-specific)
      * [Container Based](#container-based)
         * [US](#us-3)
      * [Shared Hosting](#shared-hosting)
      * [US](#us-4)
   * [Control Panel and Stacks](#control-panel-and-stacks)
      * [Control Panels](#control-panels)
         * [Self Hosted](#self-hosted)
         * [Cloud Based](#cloud-based)
      * [Stacks](#stacks)
   * [Email Providers](#email-providers)
   * [Providers to Add](#providers-to-add)
   * [Testing Method](#testing-method)
      * [Suggestions](#suggestions)

<!-- Added by: jtrask, at: Fri Oct 25 14:34:21 PDT 2019 -->

<!--te-->

# Hosting

# Bare Metal
## US
Company | Description|
 --- | --- | 
| [Vultr](https://www.vultr.com/products/bare-metal/) | Bare Metal instances.

# Dedicated Resources
## US
Company | Description|
 --- | --- |
| [Vultr](https://www.vultr.com/products/dedicated-cloud/) | Dedicated cloud with overcommit ratio detailed, 25%, 50% to 75% commited resources.

# VPS/Cloud
I'm grouping VPS/Cloud due to the fact they're basically the same underlying technology, but with a different stack.

## US
| Company | Cost Description|
 --- | --- | --- |
| [OpenVZ](https://openvz.io/) | Cheap VPS running OpenVZ
| Google| [Free](https://cloud.google.com/free/docs/gcp-free-tier?fbclid=IwAR2_KCwkaUn5hPgxM-1g8LwS9vSyi63P7vpSiJIevaumSO7FMXvv9WfLfKQ#always-free-usage-limits) to $$ | Googles Cloud Platform Cloud Platform

## Europe
Since I don't live here it's hard to keep track of decent providers.

| Company | Description|
 --- | --- |
| [UpCloud](https://upcloud.com/) | French only DC's 
| [Scaleway](https://scaleway.com) | Europe 

# Managed Hosting/WordPress Specific
## Container Based
### Multiple Locations

| Company | Stack |  Description |
 --- | --- | --- |
| [ServeBolt](https://servebolt.com) | Boltz? | Container based, need more information.

### US
| Company | Stack |  Description |
 --- | --- | --- |
| [Convesio](https://convesio.com/) | Docker | Using Docker Imaging for scaling/self-healing/high-availability.

## Shared Hosting
### US
Company | Description|
 --- | --- |
[EasyWP](https://easywp.com)| Managed Shared Hosting by NameCheap, no idea on stack.
[BitWP]https://bitwp.io/| Managed Shared hosting using DigitalOcean and GCP.

# Control Panel and Stacks
## Control Panels
### Self Hosted
### Cloud Based
Company | Description|
 --- | --- |
| [Cloudways](https://www.cloudways.com/en/pricing.php) | Provides a decent NGiNX/PHP-FPM/Varnish Stack, includes a caching plugin called Breeze.
| [SpinupWP](https://spinupwp.com)| Control Panel that works with Digital Ocean and other VPS providers.
| [GRIDPANE](https://gridpane.com/) | Control Panel that works with multiple providers.

## Stacks
This will detail the various stacked used by most providers.

# Email
Because hosting email on the same resources as your website is not ideal.
## Email Forwarding
If you simply need to forward email.

Company | Cost | Description|
| --- | --- | --- | --- |
| ForwardMX |  $ | Don't use, online but not response from owner.
| [ForwardEmail.net](https://forwardemail.net/#/) | free | Utilizes DNS records, not entirely secure

## Email Providers
The following section was added because I feel it's helpful.
<aside class="notice">The following links below may contain referral URL's and provide a kickback to myself.</aside>

Company | Cost | Storage | Description|
 --- | --- | --- | --- |
 [GSuite Basic](https://goo.gl/P1dcnY) | $6/m USD | 30GB | Email + Storage. 30GB shared.
 [GSuite Business](https://goo.gl/P1dcnY) | $12/m USD | 1000GB | Basic + 1TB or unlimited storage after 5 users.
 [GSuite Non-profit](https://support.google.com/nonprofits/answer/3367223?hl=en) | FREE | 30GB | GSuite Basic but free.
 [Mail Cheap](https://www.mailcheap.co/client/aff.php?aff=51) | $2/m USD | 10GB | Lots of options for email services, including dedicated servers.
 [Zoho](https://www.zoho.com/mail/zohomail-pricing.html) | $1/m USD | 5GB | ?
 [Rack Space](https://www.rackspace.com/email-hosting) | $2.99/m USD | 25GB | ?
 [Migadu](https://www.migadu.com) | Free/$4 | Unlimited features always. 1GB/10 Outgoing emails for Free. 100 Outgoing emails for $4/m USD. | 1GB to Unlimited

# Domains
Need to flesh this out. 

## Domains with Email
- https://www.gandi.net/en/domain
- https://www.hover.com/

# Providers to Add
- vipsdime.com - Decent, don't know. USD.
- frantech
- nosupportlinux

# Testing Method
The testing method should ensure the following is completed for each provider.

1. CPU Performance
2. Disk Performance
3. PHP Function Performance (Important for WordPress)
4. Memory Performance
5. Network Latency and Throughout-put

##  Suggestions
- https://github.com/n-st/nench
- Disk Tests Random IO ```fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=random_read_write.fio --bs=4k --iodepth=64 --size=4G --readwrite=randrw --rwmixread=75```
- Need to update and include https://github.com/jordantrizz/ultimate-linux-tool-box/blob/master/php-cpu-test.php

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

<!--te-->


# Troubleshooting
This page outlines some of the methods I use to troubleshoot issues not only on a WordPress site but also other web frameworks and services.

## Order of Debugging
* DNS
* HTTP Protocol
* TTFB

# DNS
* [The Myth of DNS Propagation](https://www.tiger-computing.co.uk/myth-dns-propagation/)
* dnstracer - Linux command ```dnstracer -o -s b.root-servers.net -4 -r 1 yourdomain.com```

# HTTP Protocol
# TTFB Explained
Time to First Byte is a rather interesting topic. I really like the [WikiPedia](https://en.wikipedia.org/wiki/Time_to_first_byte)

>TTFB measures the duration from the user or client making an HTTP request to the first byte of the page being received by the client's browser. This time is made up of the socket connection time, the time taken to send the HTTP request, and the time taken to get the first byte of the page. Although sometimes misunderstood as a post-DNS calculation, the original calculation of TTFB in networking always includes network latency in measuring the time it takes for a resource to begin loading.[citation needed] Often, a smaller (faster) TTFB size is seen as a benchmark of a well-configured server application. For example, a lower time to first byte could point to fewer dynamic calculations being performed by the webserver, although this is often due to caching at either the DNS, server, or application level. More commonly, a very low TTFB is observed with statically served web pages, while larger TTFB is often seen with larger, dynamic data requests being pulled from a database.

## Static TTFB
It's commonly understood that the TTFB is linked to a hosting providers infrastructure, technology stack and configuration/performance tuning. This is partly true, but only part of the equation.

The first request to a server from your browser is going to be for content, this is done with a "GET" request. Most browsers will send a "GET /" which asks for the root content for the site. Sometimes redirects will kick in, such as HTTP to HTTPS or if you have a sub-directory as your sites default root. This to the load time of a website, but it isn't considered apart of the TTFB.

The content returned will provide all of the necessary information for a browser to display a website page. It's HTML content that is processed by your browser and then your browser will download the necessary CSS/JS, images and then render your website.

To test static TTFB, simply place a blank HTML file somewhere in the root directory of a website. You can then call that file directly through your browser and use Chrome Dev Tools to see the waterfall and get the TTFB. You can do the same with images, Javascript or CSS.

So what actually occurs during this time, and what could potentially affect the static TTFB. The web server has to actually handle your connection at the OS level, and within the web servers own process. The operating system on the server needs to be tuned at all levels to ensure that it's able to handle multiple requests. These days this isn't really a problem, as static content doesn't require a lot of resources to handle. Even with high traffic demands, you'll most likely saturate your connection versus a servers resources.

However, there is always a cost to each request and you need to ensure that you're able to cover the cost with the appropriate resources. When a static request does come in, the web server has to locate the file and pull it from its storage. This costs more if you use spinning disks versus flash based disks.  Ultimately this is where Varnish will be put in place to cache frequent requests from in memory storage for quicker access, versus looking for content and pulling it from storage.

## Dynamic TTFB

TLDR; Overall TTFB is affected by dynamic content or processes, as these require more resources to execute.

Haven't gotten time to write this section. 

## Testing TTFB
Here are some tools to test the TTFB.

### Curl - Using this command in Linux/OSX 
* ```curl -s -o /dev/null -w "Connect: %{time_connect} TTFB: %{time_starttransfer} Total time: %{time_total} \n" mywebsite.com``
*   You can make the above an alias in your sell for easier access.
### httpstat - python script that uses curl, pip install httpstat or brew install httpstat
* see https://github.com/reorx/httpstat#installation
* example ```httpstat https://google.com```
![screenshot](https://github.com/reorx/httpstat/raw/master/screenshot.png)
### KeyCDN Tester - Multiple Locations.
* https://tools.keycdn.com/performance?url=https://google.com
    

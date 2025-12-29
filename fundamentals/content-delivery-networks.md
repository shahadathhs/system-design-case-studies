# Content Delivery Networks (CDNs)

A Content Delivery Network (CDN) is a geographically distributed network of proxy servers and their data centers. The goal is to provide high availability and performance by distributing the service spatially relative to end-users.

## How CDNs Work

The core idea of a CDN is to cache content in multiple locations around the world. When a user requests content (e.g., an image, a video, a CSS file), the CDN serves the content from the cache location that is closest to the user. This reduces latency and improves the user experience.

The process typically looks like this:

1.  A user in London requests an image from your website.
2.  Instead of your origin server in San Francisco serving the image, the request is redirected to a CDN server in London.
3.  If the CDN server has the image in its cache, it serves it directly to the user.
4.  If the CDN server does not have the image in its cache, it requests the image from your origin server, caches it for future requests, and then serves it to the user.

## Why Use a CDN?

CDNs provide several benefits:

*   **Improved Performance:** By caching content closer to users, CDNs can significantly reduce latency and improve page load times.
*   **Increased Availability:** CDNs can handle traffic spikes and protect your origin server from being overwhelmed. If one CDN server goes down, traffic can be automatically rerouted to another server.
*   **Reduced Bandwidth Costs:** By caching content, CDNs reduce the amount of data that needs to be served from your origin server, which can lower your bandwidth costs.
*   **Improved Security:** CDNs can help to mitigate DDoS attacks by absorbing and filtering malicious traffic.

## Common CDN Use Cases

CDNs are used for a wide variety of content, including:

*   **Static Assets:** Images, CSS files, and JavaScript files.
*   **Video Streaming:** On-demand and live video streaming.
*   **Large File Downloads:** Software updates, games, and other large files.
*   **Dynamic Content:** While CDNs are primarily used for static content, they can also be used to accelerate dynamic content by using techniques like route optimization and connection pre-fetching.

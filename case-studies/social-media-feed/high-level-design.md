# High-Level Design

The system will consist of the following components:

1.  **Web Client:** A web interface for users to view their feed and post new content.
2.  **API Server:** A server to handle API requests for posting content and retrieving feeds.
3.  **Fanout Service:** A service that, upon a user posting new content, pushes that content to the feeds of their followers.
4.  **Feed Store:** A database (e.g., Redis) to store the feeds for each user.
5.  **Post Store:** A database to store all the posts.

## Diagram

[Link to Diagram](../../diagrams/social-media-feed.md)

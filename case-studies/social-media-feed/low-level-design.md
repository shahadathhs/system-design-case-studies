# Low-Level Design

## Posting Content

1.  A user posts new content via the web client.
2.  The web client sends a POST request to the API server.
3.  The API server stores the post in the Post Store.
4.  The API server sends a message to the Fanout Service with the new post and the user's followers.
5.  The Fanout Service iterates through the user's followers and for each follower, it adds the new post to their feed in the Feed Store.

## Retrieving the Feed

1.  A user requests their feed via the web client.
2.  The web client sends a GET request to the API server.
3.  The API server retrieves the user's feed from the Feed Store.
4.  The API server returns the feed to the web client.

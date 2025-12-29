# Low-Level Design

## URL Shortening Process

1.  The user enters a long URL into the web client and clicks "Shorten".
2.  The web client sends a POST request to the API server with the long URL.
3.  The API server gets a unique ID from the Counter Service.
4.  The API server encodes the unique ID into a short string (e.g., base62 encoding). This will be the short URL.
5.  The API server stores the mapping between the short URL and the long URL in the database.
6.  The API server returns the short URL to the web client.

## URL Redirection Process

1.  The user clicks on a short URL.
2.  The browser sends a GET request to the API server with the short URL.
3.  The API server decodes the short URL to get the unique ID.
4.  The API server looks up the long URL in the database using the unique ID.
5.  The API server returns a 301 redirect to the long URL.

# Database Indexing

A database index is a data structure that improves the speed of data retrieval operations on a database table at the cost of additional writes and storage space to maintain the index data structure. Indexes are used to quickly locate data without having to search every row in a database table every time a table is accessed.

## Why Use Indexes?

Without an index, a database would have to perform a full table scan, which means it would have to go through every row in the table to find the ones that match the query's conditions. This can be very slow, especially for large tables.

An index works like the index in a book. If you want to find a specific topic in a book, you can either go through every page (a full table scan) or you can look up the topic in the index and go directly to the page number.

## Types of Indexes

There are many different types of indexes, but some of the most common ones are:

*   **B-Tree Index:** This is the most common type of index. It's a self-balancing tree data structure that keeps data sorted and allows for efficient insertion, deletion, and searching. B-tree indexes are great for a wide range of queries, including equality and range queries.

*   **Hash Index:** A hash index is created by computing a hash value for each row in the table. Hash indexes are very fast for equality queries, but they are not suitable for range queries because the hash values are not sorted.

*   **Full-Text Index:** A full-text index is used for searching for words or phrases within a text column. It's what powers the search functionality in many applications.

*   **Geospatial Index:** A geospatial index is used for querying data that has a geographic component, such as latitude and longitude.

## Trade-offs

While indexes can significantly improve read performance, they also have some trade-offs:

*   **Slower Writes:** Every time you add, update, or delete a row in a table, the database also has to update the indexes. This can slow down write operations.

*   **Increased Storage:** Indexes take up extra storage space. The amount of space depends on the type of index and the number of indexed columns.

It's important to choose the right indexes for your application based on your data and query patterns.
# Database Schema

## Post Table

We can use a NoSQL database like Cassandra for storing posts.

| Column Name | Data Type | Description                        |
|-------------|-----------|------------------------------------|
| `post_id`   | `uuid`    | The unique ID of the post (primary key). |
| `user_id`   | `uuid`    | The ID of the user who created the post. |
| `content`   | `text`    | The content of the post.           |
| `created_at`| `timestamp`| The timestamp when the post was created. |

## Feed Table

We can use a key-value store like Redis for storing user feeds. The key would be the `user_id` and the value would be a list of `post_id`s.

## User Table

We can use a relational database like PostgreSQL for storing user information and relationships.

| Column Name | Data Type | Description                        |
|-------------|-----------|------------------------------------|
| `user_id`   | `uuid`    | The unique ID of the user (primary key). |
| `username`  | `varchar` | The username of the user.          |
| `created_at`| `timestamp`| The timestamp when the user was created. |

## Followers Table

| Column Name   | Data Type | Description                        |
|---------------|-----------|------------------------------------|
| `user_id`     | `uuid`    | The ID of the user being followed. |
| `follower_id` | `uuid`    | The ID of the follower.            |

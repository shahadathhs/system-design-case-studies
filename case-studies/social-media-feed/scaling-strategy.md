# Scaling Strategy

## Fanout on Write

The current design uses a "fanout on write" approach, where a new post is pushed to the feeds of all followers at the time of posting. This makes reads fast, as the feed is pre-computed. However, it can be slow for users with a large number of followers (the "celebrity problem").

## Fanout on Read

An alternative approach is "fanout on read". In this approach, when a user requests their feed, we would:

1.  Get the list of users they follow.
2.  Get the recent posts from each of those users.
3.  Merge the posts and sort them by date.

This approach makes writes fast, but it can be slow for users who follow a large number of people.

## Hybrid Approach

A hybrid approach can be used to solve the celebrity problem. For most users, we can use fanout on write. For celebrities, we can use fanout on read. When a user requests their feed, we would get the pre-computed feed (from fanout on write) and then merge in the posts from any celebrities they follow.

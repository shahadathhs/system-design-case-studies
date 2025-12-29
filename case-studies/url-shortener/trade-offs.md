# Trade-offs

## Custom Aliases vs. Hash Generation

Allowing custom aliases can lead to conflicts if two users want to use the same alias. We can either reject the second request or append a random string to the alias to make it unique.

Using a hash generation algorithm like Base62 for generating short URLs is simpler and avoids conflicts, but it doesn't allow for custom aliases.

## URL Expiration

Expiring URLs can save storage space, but it can also lead to broken links if a user tries to access an expired URL. We need to choose an appropriate expiration time based on the use case.

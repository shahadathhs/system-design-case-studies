# Trade-offs

## Fanout on Write vs. Fanout on Read

As discussed in the scaling strategy, there is a trade-off between fanout on write and fanout on read.

*   **Fanout on Write:**
    *   **Pros:** Fast reads.
    *   **Cons:** Slow writes for celebrities, high storage cost for feeds.

*   **Fanout on Read:**
    *   **Pros:** Fast writes, low storage cost for feeds.
    *   **Cons:** Slow reads for users who follow many people.

The choice between these two approaches depends on the specific requirements of the system. A hybrid approach can be used to get the best of both worlds.

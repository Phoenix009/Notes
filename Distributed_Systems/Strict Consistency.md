Rule: Loads will always see the most recent Store made by any machine

Under strict consistency rules:

The instant any process performs `write(var, 1)`; all the other processes must see `read(var) = 1`.

Suppose the time diff between the `write` and `read` is infinitely small $\epsilon$. This implies that the updates must propagate to all the processes faster then the speed of light.

Thus, Strict consistency is not realistic for distributed systems.



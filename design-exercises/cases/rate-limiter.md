# Exercise: Rate Limiter

Design a rate-limiting component. Clarify whether limits are per user, key, tenant, IP or global; whether bursts are allowed; acceptable overshoot; and behaviour if the limiter is unavailable.

**First task:** define policy and correctness requirements before choosing an algorithm.

**Later constraints:** limits span many instances and regions; the limiter itself is overloaded.

# Exercise: Notification Service

Design a service that sends notifications through external providers. Clarify channels, preferences, delivery guarantees, ordering, retries, rate limits and audit needs. Distinguish accepted, queued, attempted and provider-confirmed states.

**First task:** model one notification's lifecycle and duplicate-delivery risks.

**Later constraints:** long provider outage; poison message; replay without uncontrolled duplicates.

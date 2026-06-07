# Asynchronous APIs, Business Events, and Streaming

## When to use asynchronous APIs

Some operations are too large or slow for a synchronous request/response: bulk rate loads, large
reservation or financial extracts, and similar. OPERA Cloud exposes asynchronous variants for these.

### Standard async flow

1. Submit the request to the asynchronous endpoint. The response is an acknowledgement containing a
   location or identifier to track the job, not the result itself.
2. Poll the status resource until the job reports complete (or failed).
3. Retrieve the result from the completion location once ready.

### Async headers and behaviour

- The submit call typically returns quickly with a tracking handle rather than the payload.
- Honour the documented polling interval. Do not tight-loop. Use a sensible interval and back off
  if the job is still running.
- Treat a failed job status as a real failure with a reason in the body; do not assume success on
  timeout.

### Polling best practice

- Start polling after a short initial delay; the job will not be instant.
- Use exponential or fixed sensible intervals; respect rate limits (see `error-handling.md`).
- Set a maximum wait and a clear timeout behaviour rather than polling forever.

## Business Events

Business Events are OPERA's mechanism for notifying external systems that something changed
(a reservation was created, a profile updated, a rate changed). They turn OPERA from a system you
must poll into one that can push change notifications.

### Concepts

- Events are organised by module and event type. A subscription selects which event types it wants.
- Each event describes a change to a business entity with enough data to act or to fetch detail.
- Events can be delivered by streaming (near real time) or consumed by a polling subscription,
  depending on the integration model.

### Event subscription process

1. Define which event types the integration needs (scope to the minimum).
2. Create the subscription in the OHIP portal for the relevant property or properties.
3. The subscription enters an activation lifecycle; it must be active to deliver events.
4. Optionally apply conditions/filters so only events matching criteria are delivered.

### Subscription status

A subscription has a lifecycle state (for example pending, active, suspended). Only an active
subscription delivers events. Watch for suspension caused by repeated delivery failures.

### Conditions and filters

Events can carry an indicator showing that conditions were applied, so a consumer can tell a
filtered stream from an unfiltered one. Filters reduce noise by delivering only events matching
defined criteria rather than every event of a type.

## Streaming

Streaming delivers Business Events to a consumer over a persistent connection for near-real-time
processing.

### Connection pattern

- The consumer maintains a persistent connection to the streaming endpoint and receives events as
  they occur.
- Connection health is monitored; a dropped or failing connection can lead to missed events and,
  if persistent, to subscription suspension.

### Connection alerts

OHIP can send email alerts on streaming connection problems so operators learn about a broken
connection without watching dashboards. Configure alerting so an outage is noticed and the
connection is restored before the backlog or suspension becomes a problem.

### Operational rules

- Design the consumer to be resilient: reconnect on drop, and reconcile any gap.
- A streaming consumer that cannot keep up risks falling behind; size it for peak event volume.
- Keep a fallback reconciliation path (a periodic full read) so a missed event does not cause
  permanent divergence between OPERA and the external system.

## Polling subscription (alternative to streaming)

Where a persistent streaming connection is not practical, a polling subscription lets the consumer
pull queued events on a schedule. It trades latency for simplicity. The consumer requests pending
events, processes them, and acknowledges so they are not redelivered.

## Choosing between them

- Use streaming when low latency matters and the consumer can hold a reliable persistent connection.
- Use polling when the consumer is simpler, runs on a schedule, or cannot maintain a streaming
  connection.
- In both cases, design for at-least-once delivery: make event processing idempotent so a
  redelivered event does not double-apply.

---
name: Bid resource flexibility and receive activation
description: Submit a baseline for a resource, bid its flexibility into a market, and track acceptance and activation on the Powernaut Partner API.
api: openapi/powernaut-partner-api-openapi-original.yml
operations: [GetToken, BaselineResource, BidResource, FindAllBids, GetBid, GetActivationsForResource]
---

# Bid resource flexibility and receive activation

Use this to offer a resource's flexibility to a market and follow the bid through to activation.

## Auth
Obtain a bearer token with `GetToken` (`POST /v1/auth/token`) using HTTP Basic, then send `Authorization: Bearer <access_token>` on all calls (see the onboarding skill for details).

## Steps
1. Establish the baseline for the resource with `BaselineResource` (`POST /v1/connect/resources/{id}/baseline`) so delivered flexibility can be measured against it.
2. Submit a bid with `BidResource` (`POST /v1/connect/resources/{id}/bid`): provide `timing`, `power_type`, and the `curve`. Optionally set `webhooks.accepted` to be notified when the bid is accepted.
3. Track the bid with `FindAllBids` (`GET /v1/connect/bids`) or `GetBid` (`GET /v1/connect/bids/{id}`) — watch `status`, `activation`, and `report`.
4. When accepted, you receive a `bid.accepted` webhook (payload `{ id }`) if registered, else activation arrives via MQTT. Retrieve activations with `GetActivationsForResource` (`GET /v1/connect/resources/{id}/activations`).

## Rules
- 409 Conflict on baseline: you cannot update a baseline once activations exist in the window.
- Energy sign convention: negative = downward flexibility, positive = upward (see BidReportDto).
- No idempotency key — check bid state with a GET before re-posting.
- See asyncapi/powernaut-webhooks.yml, conventions/powernaut-conventions.yml, errors/powernaut-problem-types.yml.

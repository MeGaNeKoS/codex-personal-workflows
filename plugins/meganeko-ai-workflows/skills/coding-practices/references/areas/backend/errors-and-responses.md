# Backend Errors And Responses

Use this reference when defining product errors or mapping service outcomes at a protocol boundary.

## Product Errors

- Keep product errors stable and centralized.
- Give errors a stable code, useful detail or message, and transport-independent category where the product contract requires them.
- Let services and use cases map dependency failures into product errors rather than leaking adapter-specific failures inward.

## Boundary Mapping

- Centralize response and error formatting at the boundary that owns the protocol.
- Map product errors into the protocol's standard representation, such as HTTP Problem Details, gRPC status, or broker dead-letter metadata.
- Do not hand-build unrelated error envelopes in individual handlers.
- Use standardized response envelopes when the product requires them.
- Keep wire-specific statuses, envelopes, headers, and error details exclusively in the applicable protocol reference and boundary adapter.

### Applied

One product error travels unchanged through the stack, and each protocol boundary maps it to its own representation:

```text
                          OrderNotFound { code: "order_not_found", id }
                                       |
        +------------------------------+------------------------------+
        |                              |                              |
   HTTP adapter                   gRPC adapter                  worker adapter
   404 + problem+json             NOT_FOUND status              dead-letter reason
   type: urn:shop:problem:
         order-not-found
```

The service raising `OrderNotFound` knows nothing about 404, `NOT_FOUND`, or dead letters. That is what makes it reusable across all three transports and testable without any of them.

```ts
// Wrong: the service reaches into the protocol, so it now only works over HTTP.
if (!order) throw new HttpError(404, { error: 'not found' })

// Right: a product error; the boundary decides the wire shape.
if (!order) throw new OrderNotFound(orderId)
```

Dependency failures are normalized on the way in, not leaked inward:

```ts
// In the service, wrapping an outbound call.
try {
  await this.payments.charge(order.total)
} catch (cause) {
  if (cause instanceof StripeTimeout) throw new PaymentUnavailable({ cause })
  throw new PaymentRejected({ cause })
}
```

Callers depend on `PaymentUnavailable`, never on `StripeTimeout`. Swapping the provider does not change the product contract.

For the concrete HTTP body shape, see the `api` skill's Problem JSON reference. This file owns *where* mapping happens; that one owns *what the payload looks like*.
